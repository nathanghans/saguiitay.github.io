---
title: "Woring on a TV shows trivia game - alternative data source"
date: 2016-12-09 17:00
author: saguiitay
categories: [Development]
tags: [.Net]
---

This is another post in my TV Shows Trivia game serie. In our [last post](http://www.saguiitay.com/2016/11/27/working-on-a-tv-shows-trivia-game/) 
we used [TvMaze](http://www.tvmaze.com/) to retrieve information about various tv shows. After retrieving around 1600 shows, I came
to the conclusion that the data was a bit too much - it's not enough to have the data, we need to be able to know
how popular or trendy each show is, in order to provide some form of leveling. One of the lessons I've learned from
my other games, is that making the questions too hard is a gameplay killer - you can't expect players to know every
[isoteric TV show](http://www.tvmaze.com/shows/250/kirby-buckets).

So I decided to look at an alternative data source - [TheMovieDb](http://www.themoviedb.org) has a [very nice API](https://www.themoviedb.org)
with some [C# libraries](https://www.themoviedb.org/documentation/api/wrappers-libraries) ready to use.

![The Movie DB](/images/the-movie-database.png)

We'll use the Top Rated and Popular endpoints to get the most interesting shows. Each show will be leveled by the page where it appeared - shows that appear in the first page
will be marked as the easiest. We'll also rely on the page to level the actors, characters and images - actors and characters will have the level of the show 
multiplied by 2, and we'll add the position of the actor/character in the list of actors/characters - that way, secondary actors and characters will have a higher level.

Next, we'll take the images of the actors  - we'll take only images with a vote count of at least 10 votes - we don't want to have too many images.

After that, for each show we'll also take a look at it's seasons. For each season, we'll take the season itself (leveled to page multiplied by 5 - not that 
there's too much to ask about the actual seasons). For each season, we'll take the episodes - thoes have names, so there's a bit more "meat" to the data
which will allow us to ask questions. Each episode has an image, so we'll add those too. Lastly, each episode also has a list of guest appearances - those are
important for us - we want our graph to be as complete as possible, so if an actor appeared in a show, even as a guest, we want to keep that relation. that
being said - we'll set the level of guest actors quite high, cause we don't expect these appearances to be too meaningful. If the actor is well known, her level
will be overriden with a lower level where she played a major role. 

Below is some semi-pseudo code, on how we're going to do all that:

{% highlight csharp %}
public async Task GetData()
{
    SearchContainer<SearchTv> container = await m_client.GetTvShowListAsync(tvShowListType, pageNum);

    foreach (var serie in container.Results)
    {
        Console.WriteLine($"Getting serie: {serie.Name}");
        await HandleSerie(serie.Id);
    }
}

public async Task HandleSerie(int serieId)
{
    var tvShow = await m_client.GetTvShowAsync(serieId, TvShowMethods.Credits | TvShowMethods.ExternalIds | TvShowMethods.Images | TvShowMethods.Keywords);

    AddSerie(serie);

    if (tvShow.FirstAirDate != null)
    {
        AddYear(tvShow.FirstAirDate.Value.Year);

        AddRelation(new SerieWasReleasedInYear(serie, year));
    }

    var tvShowImages = await m_client.GetTvShowImagesAsync(tvShow.Id);
    // We'll take only the top 5 backdrops
    var imageDatas = tvShowImages.Backdrops.OrderByDescending(data => data.VoteCount*data.VoteAverage).Take(5).ToList();
    for (int index = 0; index < imageDatas.Count; index++)
    {
        var image = imageDatas[index];

        AddImage(image);
        AddRelation(new SeriePicturedInImage(serie, backdrop));
    }

    if (tvShow.Credits != null)
    {
        if (tvShow.Credits.Cast != null)
        {
            for (int index = 0; index < tvShow.Credits.Cast.Count; index++)
            {
                AddActor(cast);
                AddRelation(new ActorPlayedInSerie(actor, serie));

                var images = awai m_client.GetPersonImagesAsync(cast.Id);
                var profiles = images.Profiles.OrderByDescending(data => data.VoteCount * data.VoteAverage).Take(5).ToList();
                for (int index2 = 0; index2 < profiles.Count; index2++)
                {
                    var image = profiles[index2];
                    AddImage(image);
                    AddRelation(new ActorPicturedInImage(actor, image));
                }

                if (cast.Character != null)
                {
                    AddCharacter(cast);
                    AddRelation(new SeriePortrayedCharacter(serie, character));
                    AddRelation(new SeriePortraysCharacterPlayedByActor(serie, character, actor));
                }
            }
        }

        if (tvShow.Seasons != null)
        {
            for (int index = 0; index < tvShow.Seasons.Count; index++)
            {
                var tvSeason = tvShow.Seasons[index];
                var seasonDetails = await m_client.GetTvSeasonAsync(tvShow.Id, tvSeason.SeasonNumber);

                AddSeason(tvSeason);
                AddRelation(new SerieHasSeason(serie, season));

                for (int index2 = 0; index2 < seasonDetails.Episodes.Count; index2++)
                {
                    var seasonEpisode = seasonDetails.Episodes[index2];
                    AddEpisode(seasonEpisode);
                    AddRelation(new EpisodeIsInSerie(episode, serie));

                    AddImage(seasonEpisode);
                    AddRelation(new EpisodeOfSeriePicturedInImage(episode, serie, episodeImage));

                    for (int index3 = 0; index3 < seasonEpisode.GuestStars.Count; index3++)
                    {
                        AddActor(guest);
                        AddRelation(new SerieFeaturesActor(serie, guestActor));

                        AddCharacter(cast);
                        AddRelation(new SeriePortraysCharacterPlayedByActor(serie, guestCharacter, guestActor));
                    }
                }
            }
        }
    }
}
{% endhighlight %}

One thing you'll notice, is that I'm adding only one "direction" for each relation. The reason for that,
is that each relation has an `Invert()` method, which will return any complementary relations. For example,
the SeriePortraysCharacterPlayedByActor relation can be completed with SeriePortrayedCharacter, CharacterAppearsInSerie,
etc. to get me the complete list of relations.
