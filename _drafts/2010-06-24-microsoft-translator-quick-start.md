---
layout: post
title: "Microsoft Translator - Quick Start"
date: 2010-06-24 21:32
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development, Microsoft]
---
After adding a Service Reference to the following URL <http://api.microsofttranslator.com/v2/Soap.svc>, You can easily translate text by following the sample below:

``` csharpcode

LanguageService service = new LanguageServiceClient();

string appId = ""; // Application ID required for using the Bing Web Service


// Get all available languages for translation
var langs = service.GetLanguagesForTranslate(appId);
foreach (var lang in langs)
    Console.WriteLine("Language: " + lang);

// We request up-to 5 french translations for the english work 'number'
var result = service.GetTranslations(appId, "number", "en", "fr", 5, null);
foreach (var tranlationMatch in result.Translations)
{
    // TranslatedText: The translated text
    Console.WriteLine("TranslatedText: " + tranlationMatch.TranslatedText);
    // MatchDegree: The system matches input sentences against the store, including inexact matches.
    // MatchDegree indicates how closely the input text matches the original text found in the store.
    // The value returned ranges from 0 to 100, where 0 is no similarity and 100 is an exact case sensitive match.
    Console.WriteLine("MatchDegree: " + tranlationMatch.MatchDegree);
    // Rating: Indicates the authority of the person making the quality decision. Machine Translation
    // results will have a rating of 5. Anonymously provided translations will generally have a rating of 1 to
    // 4, authoritatively provided translations will generally have a rating of 6 to 10.
    Console.WriteLine("Rating: " + tranlationMatch.Rating);
}
```



