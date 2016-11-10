---
layout: single
title: "Geography Trivia Data"
permalink: /geography-data/
author_profile: true
---

{% include base_path %}

<div id="data-div">
</div>

<script>

		var countries = {% include /games/geography/countries %};
		addCollection(countries, "Countries");
		
		var seas = {% include /games/geography/seas %};
		addCollection(seas, "Seas");
		
		var cities = {% include /games/geography/cities.1 %};
		cities = cities.concat({% include /games/geography/cities.2 %});
		addCollection(cities, "Cities");

		var pois = {% include /games/geography/pois %};
		addCollection(pois, "Points of Interest");
		
		var currencies = {% include /games/geography/currencies %};
		addCollection(currencies, "Currencies");
		
		var languages = {% include /games/geography/languages %};
		addCollection(languages, "Languages");
		
		var religions = {% include /games/geography/religions %};
		addCollection(religions, "Religions");
		
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
