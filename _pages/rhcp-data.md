---
layout: single
title: "RHCP Trivia Data"
permalink: /rhcp-data/
author_profile: true
---

{% include base_path %}

<div id="data-div">
</div>

<script>

		var people = {% include /games/rhcp/people %};
		addCollection(people, "People");

		var albums = {% include /games/rhcp/albums %};
		addCollection(albums, "Albums");
		
		var songs = {% include /games/rhcp/songs %};
		addCollection(songs, "Songs");
		
		var lyrics = {% include /games/rhcp/lyrics %};
		addCollection(lyrics, "Lyrics");
		
		function addCollection(collection, title) {
			var element = document.getElementById("data-div");
			element.innerHTML += "<h2>" + title + "</h2>";
			element.innerHTML += "<ul>";
			for (i = 0; i < collection.length; i++) { 
				element.innerHTML += "<li>" + collection[i].Name + "</li>";
			}
			element.innerHTML += "</ul>";
		}
</script>
