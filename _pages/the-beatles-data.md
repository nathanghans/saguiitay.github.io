---
layout: single
title: "The Beatles Data"
permalink: /the-beatles-data/
author_profile: true
---

{% include base_path %}


<div id="data-div">
</div>

<script>
		var element = document.getElementById("data-div");

		var albums = {% include /games/the-beatles/albums %};
		addCollection(albums, "Albums");
		
		var songs = {% include /games/the-beatles/songs %};
		addCollection(songs, "Songs");
		
		var quotes = {% include /games/the-beatles/quotes %};
		addCollection(quotes, "Quotes");
		
		function addCollection(collection, title) {
			element.innerHTML += "<h2>" + title + "</h2>;
			element.innerHTML += "<ul>";
			for (i = 0; i < albums.length; i++) { 
				element.innerHTML += "<li>" + albums[i].Name + "</li>";
			}
			element.innerHTML += "</ul>";
		}
</script>
