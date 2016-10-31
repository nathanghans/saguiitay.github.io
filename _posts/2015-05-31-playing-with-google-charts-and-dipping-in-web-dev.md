---
title: Playing with Google Charts and experiencing in web development
date: 2015-05-31 16:00
author: saguiitay
categories: [Development]
tags: [.Net, Copy]
redirect_from:
 - /2015-05-31-playing-with-google-charts-and-dipping-in-web-dev/
 - /development/playing-with-google-charts-and-dipping-in-web-dev/
---
## Background

Over the last several months, I've been playing around more and more with web development. Until now, I've spent most of that time reading blogs and tutorials. I've decided that now's the time to start actually writing something.

For one of my ideas, I wanted to allow users to create a tree diagram dynamically. This post will describe my trial using Google Charts.

I've also experienced for the first time with the following technologies, to bear with me:

 *  [Gulp](http://gulpjs.com/)
 *  [Browserify](http://browserify.org/)
 *  [Handlebars](http://handlebarsjs.com/)

### Goals

My goal was to create a tree of panels - in each panel, users should be able to add a sub-node, or remove the node itself.

Each node will be displayed as a Bootstrap panel, with header, content and footer. Each node will also have 2 buttons: "Add Child" and "Remove".

You can find the final source code here: <https://github.com/saguiitay/GoogleCharts-Tree-Editor-Example>

And here's what it looks like:

![](https://raw.githubusercontent.com/saguiitay/GoogleCharts-Tree-Editor-Example/master/GoogleCharts-Tree-Screenshot.png)

### Main code

The main code is based on [Google Organization Chart example](https://developers.google.com/chart/interactive/docs/gallery/orgchart), with some modifications:

 * I've removed all the hard-coded rows, and kept only the root
 * Each node will be created using its full representation: 2 columns (Id, ParentId), with Id composed of an object with 3 properties:
   * "v": String Id of the node (will be used as the ParentId of child nodes)
   * "p": Object containing any properties we'd like to keep about the node
   * "f": HTML representation of the node

This should be pretty close to your starting point:

{% highlight javascript %}
$ = jQuery =  require('jquery');
var bootstrap = require('bootstrap');
var handlebars = require('handlebars');
	
google.load("visualization", "1", {
	packages : ["orgchart"]
});
google.setOnLoadCallback(drawChart);

function drawChart() {
	var data = new google.visualization.DataTable();
	data.addColumn('string', 'id');
	data.addColumn('string', 'parentId');

	var rootRowProperties =  { title: 'Root', numChildren: 0 };
	data.addRows([
			[{
			    v : 'Root',
                p: rootRowProperties,
				f : cardTemplate(rootRowProperties)
			}, '']
		]);

	var chartOptions = {
		allowHtml : true,
        nodeClass: 'none',
        selectedNodeClass: 'none',
		width: '800px'
	};
	var chart = new google.visualization.OrgChart(document.getElementById('chart_div'));
	
	chart.draw(data, chartOptions);
}{% endhighlight %}


### Handlebars

I've created an Handlebars template that displays a panel, along with some placeholders for the node's data:

{% highlight html %}
<div class="panel panel-default">
	<div class="panel-heading">
	    {{title}}
    </div>
    <div class="panel-body">
	    Children: {{numChildren}}
    </div>
    <div class="panel-footer">
        <div class="btn-toolbar" role="toolbar">
		    <div class="btn-group btn-group-xs" role="group">
		    	<button type="button" class="add btn btn-xs btn-primary">Add</button>
			    <button type="button" class="remove btn btn-xs btn-danger">Remove</button>
		    </div>
	    </div>
    </div>
</div>   
{% endhighlight %}

I've then added the template as a `require()` call in the main.js file:

{% highlight javascript %}
var cardTemplate = require('./templates/card.handlebars');
{% endhighlight %}

### Responding to events

Now that we've got our base code to display the Chart, and a template to display each node, it's time to start making it dynamic, by attaching handles to the buttons. In the `main.js`, add the following as part of the requirements (we'll implement the `cardChart` module in the next paragraph: 

{% highlight javascript %}
var cardChart = require('./cardChart');
{% endhighlight %}

And add these lines after the chart creation:

{% highlight javascript %}
cardChart.attach(data, chart, chartOptions);

$('#chart_div').on('click', '.add', cardChart.addChild);
$('#chart_div').on('click', '.remove', cardChart.removeNode);
{% endhighlight %}

These lines will notify our module about our Chart, and will add the `addChild` and `removeNode` methods as handlers to the "Add" and "Remove" buttons respectively.

### The `cardChart` Module

This module will be responsible for adding and removing nodes from out Chart. Here's the baseline for the module (details were removed for brevity):

{% highlight javascript %}
var cardTemplate = require('./templates/card.handlebars');

var _data, _chart, _charOptions;
function refreshChart() {
	_chart.draw(_data, _chartOptions);
	_chart.setSelection([]);
}

exports.attach = function(data, chart, chartOptions) {
	_data = data;
	_chart = chart;	
	_chartOptions = chartOptions;
}

exports.addChild = function() {
	var selectedRows = _chart.getSelection();
	var selected = selectedRows[0];
	
	// ... Code to add child node ...

	refreshChart();
}


exports.removeNode = function() {
	var selectedRows = _chart.getSelection();
	var selected = selectedRows[0];
	
	// ... Code to remove node ...
	
	refreshChart();
}
{% endhighlight %}

### Gulp

I've used Gulp for two purposes - merge my dependencies using Browserify, and to compile my Handlebars templates using the `browserify-handlebars` module.

I ended up with a simple `gulpfile.js` file, which takes my `main.js` file, and pass it through the browserify pipeline, with the browserify-handlebars transform:


{% highlight javascript %}
var gulp = require('gulp');
var browserify = require('gulp-browserify');
var browserifyHandlebars = require('browserify-handlebars');

gulp.task('scripts', function() {
    // Single entry point to browserify
    gulp.src('js/main.js')
        .pipe(browserify({
			transform: [browserifyHandlebars],
			debug : !gulp.env.production
        }))
        .pipe(gulp.dest('./'))
});

gulp.task('default', ['scripts']);
{% endhighlight %}

## Summary

Although I've decided not to use Google Chart, I believe I've learned a good deal to get me started - from structuring my code, to using the various tools (`npm`, `browserify`, `handlebars` to name a few).  