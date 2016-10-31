---
title: "KnockoutJS Mapping plugin to the rescue"
date: 2014-09-29 20:47
author: saguiitay
categories: [Development]
tags: [JavaScript, KnockoutJS]
redirect_from:
 - /2014-09-29-knockoutjs-mapping-plugin-to-the-rescue/
 - /development/knockoutjs-mapping-plugin-to-the-rescue/
---
I've been using [KnockoutJS](http://knockoutjs.com/) for a long time now, but only recently found the magic that is the [Knockout-Mapping plugin](http://knockoutjs.com/documentation/plugins-mapping.html).

A few days ago, I spent some time refining some old code, and encountered something along the following lines:

{% highlight javascript %}
function report(reportData) {
    var self = reportData;
    self.StartedText = friendlyPastMoment(reportData.Started);
    if (reportData.Ended == "0001-01-01T00:00:00") {
        self.EndedText = 'Still running...';
    }
    else {
        self.EndedText = friendlyPastMoment(reportData.Ended);
    }

    return self;
}
{% endhighlight %}

(the `friendlyPastMoment()` method is just a small wrapper method around [MomentJS](http://momentjs.com), for handling edge cases)

We had a couple more properties handled similarly. Now, this worked just fine when the information displayed to the end-user was static - we just
added a few text binding to a couple of span nodes, and the `StartedText` and `EndedText` were displayed beautifully.

But we've decided that we want to updated the information displayed to the user automatically, which meant that I now had to replace
those simple `StartedText` and `EndedText` properties into Knockout computed properties. But for that to work, I had to change the
implementation - the `Started` and `Ended` properties of the report need to be [observable properties](http://knockoutjs.com/documentation/observables.html).

I've started with a naive implementation:

{% highlight javascript %}
function report(reportData) {
    var self = reportData;
    self.Started = ko.observable(reportData.Started);
    self.Ended = ko.observable(reportData.Ended);

    self.StartedText = ko.computed(function() { return friendlyPastMoment(self.Started()) });
    self.EndedText = ko.computed(function() { 
        if (self.Ended() == "0001-01-01T00:00:00") {
            return 'Still running...';
        }
        else {
            return friendlyPastMoment(self.Ended());
        }});

    return self;
}
{% endhighlight %}

But since we had several properties in `reportData` that needed to implement this behavior, this started to become a code-smell. I was certain
that there must be a better way for handling these kind of scenarios.

The [Knockout-Mapping plugin](http://knockoutjs.com/documentation/plugins-mapping.html) was created exactly for such cases - it convert a simple
POCO object into its Knockout equivalent, with all properties implemented as observable properties, with arrays and all:

{% highlight javascript %}
function report(reportData) {
    ko.mapping.fromJS(reportData, {}, this);

    var self = this;

    self.StartedText = ko.computed(function() { return friendlyPastMoment(self.Started()) });
    self.EndedText = ko.computed(function() { 
        if (self.Ended() == "0001-01-01T00:00:00") {
            return 'Still running...';
        }
        else {
            return friendlyPastMoment(self.Ended());
        }});

    return self;
}
{% endhighlight %}

As you can see, the first line calls the `fromJS` method, which takes a regular JavaScript object, an options object (empty `{}` in the line above),
and the destination object. The result will be that this object will have the same properties as `reportData`, implemented as Knockout observable properties.

Now, it is very easy to add a refresh function to report, which will retrieve the most current information from the server, and update the report:
{% highlight javascript %}
self.refresh = function() {
	$.getJSON("api/report?id=" + self.Id(), function (data) {
		ko.mapping.fromJS(data, self);
	});
};
{% endhighlight %}

As you can see, we're making a `getJSON` call to the server, and once we get the data back, we just call the `fromJS` method again, this time passing
the data retrieved from the server, and our current report instance.
