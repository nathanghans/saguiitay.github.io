---
layout: single
title: "Movies Trivia Data"
permalink: /movies-data/
author_profile: true
---

{% include base_path %}

<div id="data-div">
</div>

<script>

		var movies = {% include /games/movies/movies %};
		addCollection(movies, "Movies");
		
		var people = {% include /games/movies/people %};
		addCollection(people, "People");
		
		var actors = {% include /games/movies/actors.1 %};
		actors = actors.concat({% include /games/movies/actors.2 %});
		addCollection(actors, "Actors");

		var characters = {% include /games/movies/characters %};
		addCollection(characters, "Characters");
		
		var taglines = {% include /games/movies/taglines %};
		addCollection(taglines, "Taglines");
		
		var awards = {% include /games/movies/awards %};
		addCollection(awards, "Awards");
		
		function addCollection(collection, title) {
			var element = document.getElementById("data-div");
			var html += "<h2>" + title + "</h2>";
			html += "<ul>";
			for (i = 0; i < collection.length; i++) { 
				html += "<li>" + collection[i].Name + "</li>";
			}
			html += "</ul>";
			element.innerHTML += html
		}
</script>
