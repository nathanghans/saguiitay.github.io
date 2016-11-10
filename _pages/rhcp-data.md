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
			var html += "<h2>" + title + "</h2>";
			html += "<ul>";
			for (i = 0; i < collection.length; i++) { 
				html += "<li>" + collection[i].Name + "</li>";
			}
			html += "</ul>";
			element.innerHTML += html
		}
</script>
