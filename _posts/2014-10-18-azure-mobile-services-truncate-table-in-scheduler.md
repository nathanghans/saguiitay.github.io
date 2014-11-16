---
layout: post
title: "Azure Mobile Services: Truncate table in Scheduler"
date: 2014-10-18 15:58
author: saguiitay
comments: true
categories: [Development]
tags: [Azure, Mobile Services]
share: true
redirect_from:
 - /2014-08-20-azure-mobile-services-truncate-table-in-scheduler/
 - /development/azure-mobile-services-truncate-table-in-scheduler/
---
I am using Windows Azure Mobile Services to manage the Leaderboard of my [Movies Trivia]({{site.url}}/windows-phone/movies-trivia) and [Geo Trivia]({{site.url}}/windows-phone/geo-trivia).
Due to the nature of the data, I'm using 3 tables:

1. Users: to maintain the users' Id and Name
2. Leaders: the list of players, ordered by their position in the board
3. Results: the scores that are submitted by the players as they answer questions correctly.

Every time a user submit a result, the Leaders table is updated - that player's score is updated, and the list is reordered based on the updated scores. This works
perfectly fine, and gives me the exact results I need.

However, every now and then, my database reaches its storage limits. Since I'm using the free tier of Azure, my database is limited to 20MB, which is enough most
of the time, until I get too many players playing over a short period of time. That result in the Results table growing, and filling up the database. And then I have
to go and delete all that data (mind you, the data is not needed once the Leaders table is updated).

Until now, I just did that manually, since it never bothered me. But today, I decided it's time to automate the process. I've created a new Scheduler task in the
Mobile Service:

![]({{site.url}}/images/azure-mobile-scheduler-task.PNG)

Set it up to run every 6 hours. This schedule should be more than enough for my need, and there's no point overloading the service for nothing. If needed, I can
change the task to run one every 15 minutes:

![]({{site.url}}/images/azure-mobile-scheduler.PNG)

Lastly, I've set up the script to query the database for all the rows in the "Results" table, and if any result is found, issue a delete operation:

{% highlight javascript %}
function deleteoldresults() {
    // Query all the id's in the Result table
    mssql.query(
        "SELECT id FROM Result",
        {
            success: function(results) {
				// If any result is found
                if (results.length > 0) {
					// Get the result table, and issue a delete operation for each result
					var resultTable = tables.getTable('Result');
                    for (var i = 0; i < results.length; i++) {
                        resultTable.del(results[i].id);
                    }
                } else {
                    console.log('No results rows found.');
                }
            },
            error: function (err)
            {
                console.error("Error occurred. Details:", err);
            }
        });
}
{% endhighlight %}
