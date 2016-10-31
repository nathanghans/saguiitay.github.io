---
title: "Cap'n Proto - a call to action"
date: 2014-11-16 17:58
author: saguiitay
categories: [Development]
tags: [.Net]
redirect_from:
 - /2014-11-16-cap-n-proto-a-call-to-action/
 - /development/cap-n-proto-a-call-to-action/
---
[Marc Gravell](http://blog.marcgravell.com/) is a [valued associate in StackOverflow](http://blog.stackoverflow.com/2010/06/welcome-stack-overflow-valued-associates-00006-and-00007/), so I make sure to follow his blog - this
guy knows what he's doing (he's the main contributor to [Dapper.net](https://github.com/StackExchange/dapper-dot-net), [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) and
[protobuf-net](http://code.google.com/p/protobuf-net/)).

Over the weekend, Marc wrote about a project called [Cap'n Proto](http://kentonv.github.io/capnproto/) - a new "serialization" protocol, implemented by the same guy that developed Protobuf at Google(
[Kenton Varda](https://twitter.com/kentonvarda)). Cap'n Proto is not exactly another serialization library - it's more of a way to maintain structured data as raw memory. What this really means, is that you could just
take the raw bytes (of a file, transmitted over the net, or whatever), and use them as you would normally use your object model. No deserialization needed whatsoever.

Obviously, performance implications are drastic - you no longer need to serialize/deserialize your data. You don't need to allocate objects in memory, especially when you just need to access a subset of the data. You can
use memory mapped files for **fast** inter-process communication. And there are even on RPC advantages.

Cap'n Proto is implemented in C++, and the tools that come with it works / compiles for Linux. Marc has started to work on a .Net implementation, and made it available on GitHub <https://github.com/mgravell/capnproto-net/>.

And now, Marc asks for the community's assitance in improving his initial work. Here's the list of areas that need work (copied from Marc's website):

>  * Schema parsing: this is currently a major PITA, since the current ["capnp" tool](http://kentonv.github.io/capnproto/capnp-tool.html) only really works / compiles for Linux. There is a plan in the core Cap'n Proto project
>    to get this working on MinGW (for Windows), but it would be nice to have a full .NET parser - I'm thinking "[Irony](https://irony.codeplex.com/)"-based, although I'm not precious about the implementation
>  * Offset processing: related to schema parsing, we need to compute the byte offsets of a parsed schema. This is another job that the "capnp" tool does currently. Basically, the idea is to look at all of the defined fields,
>    and assign byte offsets to each, taking into account some fairly complicated "group" and "union" rules
>  * Code generation: I have some working code-gen, but it is crude and "least viable". It feels like this could be done much better. In particular I'm thinking "devenv" tooling, whether that means
>    [T4](http://msdn.microsoft.com/en-us/library/bb126445.aspx) or some kind of VS plugin, ideally trivially deployed (NuGet or similar) - so some experience making Visual Studio behave would be great. Of course, it would be
>    great if it worked on Mono too - I don't know what that means for choices like T4.
>  * Code-first: "__schemas? we don't need no stinking schemas!__"; here I mean the work to build a schema model from pre-existing types, presumably via attributes - or perhaps combining an unattributed POCO model with a regular schema
>  * POCO serializer: the existing proof-of-concept works via generated types; however, building on the code-first work, it is entirely feasible to write a "regular" serializer that talks in terms of some pre-existing POCO
>    model, but uses the library api to read/write the objects per the wire format
>  * RPC: yes, Cap'n Proto defines an RPC stack. No I haven't even looked at it. It would be great if somebody did, though
>  * Packed encoding: the specification defines an alternative "packed" representation for data, that requires some extra processing during load/save, but removes some redundant data; not currently implemented
>  * Testing: I'm the worst possible person to test my own code - too "close" to it. I should note that I have a suite of tests related to my actual needs that aren't in then open source repo (I'll try and migrate many of them),
>    but: more would be great
>  * Multi-platform projects: for example, an iOS / Windows Store version probably needs to use less (well, zero) of the "unsafe" code (mostly there for efficiency); does it compile / run on Mono? I don't know.
>  * Proper performance testing; I'm casually happy with it, but some dedicated love would be great
>  * Much more compatibility testing against the other implementations
>  * Documentation; yeah, telling people how to use it helps
>  * And probably lots more stuff I'm forgetting
