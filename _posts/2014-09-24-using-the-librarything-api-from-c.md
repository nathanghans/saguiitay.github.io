---
layout: post
title: "Using the LibraryThing API from C#"
date: 2014-09-24 13:19
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, HttpClient, Library, LibraryThing]
share: true
---
If you are not familiar with LibraryThing, go ahead and visit their [website](http://www.librarything.com/) - it's probably the most comprehensive books
collection in the web.

LibraryThing provide several APIs, one of them a RESTful API, which can be invoked from C# relatively easily. In this post I'll describe how to use that API.

First, you need to get a Developer Key. You can do that here: <http://www.librarything.com/services/keys.php>

Here's the most important thing you need:

{% highlight csharp %}
private const string CollectionByUser = "http://www.librarything.com/api_getdata.php?userid={0}&resultsets=books&max=9999&&booksort=title&responseType=json&key=" + DevKey;
private const string WorkUrlByISBN = "http://www.librarything.com/services/rest/1.1/?method=librarything.ck.getwork&isbn={0}&apikey=" + DevKey;
private const string WorkUrlById = "http://www.librarything.com/services/rest/1.1/?method=librarything.ck.getwork&id={0}&apikey=" + DevKey;
private const string WorkUrlByLCCN = "http://www.librarything.com/services/rest/1.1/?method=librarything.ck.getwork&lccn={0}&apikey="+ DevKey;
private const string WorkUrlByOCLC = "http://www.librarything.com/services/rest/1.1/?method=librarything.ck.getwork&oclc={0}&apikey=" + DevKey;
private const string AuthorUrlByCode = "http://www.librarything.com/services/rest/1.1/?method=librarything.ck.getauthor&authorcode={0}&apikey=" + DevKey;
private const string ImageUrl = "http://covers.librarything.com/devkey/" + DevKey + "/{0}/isbn/{1}";
{% endhighlight %}

These URLs allow you to retrieve information from LibraryThing, by providing a Developer Key, and the details about which data you want to get:

- CollectionByUser: Retrieve the list of books that a user has added in his collection. Notice that the URL returns the "books" result-set (you can also get the users' ratings, reviews and tags).
- WorkUrlByISBN: Retrieve information about a specific work (book), provided the book's ISBN.
- WorkUrlById: Retrieve information about a specific work (book), provided the book's LibraryThing ID.
- WorkUrlByLCCN: Retrieve information about a specific work (book), provided the book's LCCN (Library of Congress Control Number).
- WorkUrlByOCLC: Retrieve information about a specific work (book), provided the book's OCLC (<span id="dscexpitem_1">Online Computer Library Center).
- AuthorUrlByCode: Retrieve information about a specific author, provided the author's LibraryThing author-code.
- ImageUrl: Retrieve the cover image of a book, provided the book's ISBN, and the requested image size (

Now, if you want to see some code, we'll start by creating an HttpClient instance:

{% highlight csharp %}
_handler = new HttpClientHandler();
if (_handler.SupportsAutomaticDecompression)
    _handler.AutomaticDecompression = DecompressionMethods.Deflate | DecompressionMethods.GZip;
_httpClient = new HttpClient(_handler);
{% endhighlight %}

To get a book's details, we'll make the following call to the LibraryThing API:

{% highlight csharp %}
var url = string.Format(WorkUrlByISBN, isbn);
    string content;
    try
	{
        var response = await  _httpClient.GetAsync(url).ConfigureAwait(false);
        if (!response.IsSuccessStatusCode)
            return null;
 
	    content = await response.Content.ReadAsStringAsync().ConfigureAwait(false);
	}
    catch (Exception)
	{
        return null;
	}
{% endhighlight %}

You can then parse the result into a workable object model. The model is a bit complex, so I've posted it as Gist: <https://gist.github.com/saguiitay/be3868dae2efb12802a2>

One method in that Gist requires some explanation - the parsing of the Xml returned from LibraryThing is not straight forward - you need
to "decode" it, in order for some Xml entities to be processed correctly. For that, I've created an extension method for 
String (<https://gist.github.com/saguiitay/be3868dae2efb12802a2#file-stringextensions-cs>), that replaced the XmlEntities into their equivalent character.

All in all, LibraryThing provides an extensive API to their data, which is mostly painless to work with, and provide detailed and valuable information.

You can see the results of using LibraryThing to retrieve information about books and authors in my Windows Phone App "Library",
which you can download here: <http://www.windowsphone.com/s?appid=01f350f2-01d1-4210-a83b-9874b71e9496>