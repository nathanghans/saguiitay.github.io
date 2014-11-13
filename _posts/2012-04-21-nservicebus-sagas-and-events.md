---
layout: post
title: "NServiceBus, Sagas and events"
date: 2012-04-21 17:31
author: saguiitay
comments: true
categories: [Development]
tags: [NServiceBus]
share: true
redirect_from:
 - /2012/04/21/nservicebus-sagas-and-events/
 - /2012-04-21-nservicebus-sagas-and-events/
 - /development/nservicebus-sagas-and-events/
---
In the last few weeks, I've been spending some time with [NServiceBus](http://www.nservicebus.com), as part of my prototyping a new system 
we're working on at work. The system encompass quite a few moving parts, and using a Bus made sense. One of the expected services will be 
using the concept of Saga's - long running processes. It will be started by an arriving message, send some messages to other services, and will 
then wait for an event, signaling the other services finished their work.

Sounds simple - I created the handler, marked it as a Saga, implemented the 2 `Handle<T>` methods - one for the initial message, and the second 
for the event. Kicking all the services to live, I was thrilled to see all my services working as the various messages fly around. 
That is - until the systems appears to reached the end of it's work - the event published by the worker services was not received by the Saga's service. 

I've played with configuration, the Windsor container, the MSMQ queues, to no avail. I reviewed the samples, read blogs - nothing. 

After spending 3 days on this, I was starting to despair. Reviewing the documentation one more time, I finally found my solution:
[Sagas and automatic subscriptions](http://www.nservicebus.com/Sagas.aspx):

> *In the case where the saga is handling events, you might have thought that (like a regular message handler) NServiceBus would automatically subscribe to those events for you. 
> ... 
> Therefore, if the saga is the only code at a given endpoint requiring that message type, you will need to manually subscribe to message type handled by your saga. 
> Simply calling Bus.Subscribe() in that endpoint's startup code will be enough.*

To be honest - I previously just ignored this section, assuming it was not relevant for my case. I take full responsibility for that. 
But the documentation should make this more obvious too - they made the whole library so pain-free and stupid-easy to use - I just assumed 
that such a strict requirement would be pointed out in bold, red letters, for all us stupid end-users (read "me").
