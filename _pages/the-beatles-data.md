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
		var data = {% include the-beatles-data %};
		var element = document.getElementById("data-div");
		for (i = 0; i < data.length; i++) { 
			element.innerHTML += data[i].Name;
		}
</script>
