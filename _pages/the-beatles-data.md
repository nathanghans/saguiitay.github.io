---
layout: single
title: "The Beatles Trivia Data"
permalink: /the-beatles-data/
author_profile: true
---

{% include base_path %}

<div id="data-div">
</div>

<script>

		var people = {% include /games/the-beatles/people %};
		addCollection(people, "People");

		var albums = {% include /games/the-beatles/albums %};
		addCollection(albums, "Albums");
		
		var songs = {% include /games/the-beatles/songs %};
		addCollection(songs, "Songs");
		
		var quotes = {% include /games/the-beatles/quotes %};
		addCollection(quotes, "Quotes");

		var lyrics = {% include /games/the-beatles/lyrics %};
		addCollection(lyrics, "Lyrics");
		
		function addCollection(collection, title) {
			var element = document.getElementById("data-div");
			var html = "<h2>" + title + "</h2>";
			html += "<ul>";
			for (i = 0; i < collection.length; i++) { 
				html += "<li>" + collection[i].Name + "</li>";
			}
			html += "</ul>";
			element.innerHTML += html
		}
</script>
