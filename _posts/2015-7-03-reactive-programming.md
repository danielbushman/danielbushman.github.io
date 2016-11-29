---
title:  "Reactive Programming is people!"
description: I stream. You stream...
---

## Everything and nothing is always new in Computer Science.

You could easily say that there is nothing new about Reactive Programming, and you'd be mostly right. But you'd also be wrong. Let's look into why some [very smart][1] and [accomplished][2] people are using "Reactive" as the buzzword *du jour* to refer to the loosely defined paradigm which is making the industry's leading edge like [Facebook][3], [Netflix][4], and [LinkedIn][5] so relevant.

### TL;DR:

*Reactive* is an umbrella term used to classify paradigms in both computer programming and systems architecture that are conceptually similar and offer similar benefits. It's a lot about considering inputs, including changes and events, as a series of immutable facts, referred to as a stream. Streams flow into a directed graph composed of services and functions which pass the facts along, emit new facts, or do both. The aim is to declaratively describe how to appropriately react to streams, in order to immediately produce the desired outputs.

> ![Y U No Read?](/assets/images/y-u-no-read.jpg)

## What is Reactive?  

I could tell you the history, but I don't know it. It's said that Functional Reactive Programming (FRP) was invented by Conal Elliott, in Haskell. Significant as that has been, it was really only the initial adoption of the term Reactive --- which [he regrets](http://www.haskellcast.com/episode/009-conal-elliott-on-frp-and-denotational-design/). FRP certainly does not encompass everything that is now classified as Reactive.

There is [The Reactive Manifesto][6], but it's reminiscent of the [Manifesto for Agile Software Development][7] from 2001, in that it's abstract and idealistic enough that even though people who are hungry for it get really excited, it also leaves them without a clear path of what to do about it, other than a blanket suggestion to use message queues.

> ![Message queues would be great.](/assets/images/mq-would-be-great.jpg)

Yet when most people use the term Reactive today, it's more conceptually related to this manifesto than to pure Functional Reactive Programming.

There is a practical tutorial that's appropriately titled: [The introduction to Reactive Programming you've been missing][8], but it covers only a cross-section of the bigger picture, and is limited to just one Reactive JavaScript library --- though RxJS *is* an excellent library. The tutorial is particularly useful because in addition to showing real examples, it uses visualizations to help us get our mind around it conceptually. I also agree with him:

> The hardest part is learning to think in Reactive.

RxJS is just one of the [Reactive Extensions libraries](https://github.com/Reactive-Extensions) born from Microsoft's Cloud Programmability Group -- which also helped bring the word *Reactive* into broader use.


That's really all the history I know. I wish I had a log of everything that happened in the development of Reactive so I could fill in the gaps.


  [1]: https://youtu.be/aJuo_bLSW6s
  [2]: https://youtu.be/pwpxq9-uw_0
  [3]: https://youtu.be/KVZ-P-ZI6W4?t=1m12s
  [4]: https://youtu.be/XRYN2xt11Ek
  [5]: https://youtu.be/fU9hR3kiOK0
  [6]: http://www.reactivemanifesto.org/
  [7]: http://agilemanifesto.org/
  [8]: http://xgrommx.github.io/rx-book/content/guidelines/introduction/index.html

# All your streams are belong to us.

Once you get through the learning curve of the initial paradigm shift, it's hard not to look at everything as a stream, and that's a good thing. If we can manage to handle all relevant data points within our systems as streams then we can get all the benefits of being Reactive all the time. But why?

## What is data *for*?

We all know how rapidly technology is advancing. Though we are beginning to run up against some boundaries with scaling systems vertically. Looking at the state of the art offers us some new ways we can get a surprising level of both consistency *and* availability while scaling horizontally.

Why do we store data? Fundamentally it's to persist some state we will later care about. But what *information* do we actually care about? What is normalization *for* anyway? Normalization is an extremely useful way to maintain a Single Point Of Truth, but it comes with it's own gotchas.

What makes its Truth authoritative? Using normalization as a way to manage Truth comes with the huge unspoken assumption that the stored information was derived without error. Does the information being correct have anything to do with how we store it?

Imagine if we could time-travel. Going back in time to witness what actually happened would be the ultimate in reliable authority.

> Giving new meaning to "Single Point Of Truth".

What about accounting errors? If we overwrite the data we're using as a Single Point Of Truth with incorrect information, a strategy is needed to somehow figure out what the correct values should be and fix it. For any given strategy, how many metaphorical accountants does it take to audit and repair all the incorrect data? Going back in time to correct the mistake is starting to sound pretty appealing.

## I want answers.

We store data because at some future date a question will be asked, and we want to be able to provide with the right answer. That's why queries are called queries, obviously. How can we write a query without knowing what question will be asked? And if we can see ahead and know what the question will be, why do we wait to ask it? In the past this was partly due to the limited resources and especially due to the extreme number of permutations which are caused by introducing only a few variables into the query.

> ![What if I told you stream processing can replace queries](/assets/images/what-if-i-told-you.jpg)

With today's resources, a balance is still needed between answering all possible questions and only responding to questions when they are asked. But the balance is, much more than it's ever been, in favor of answering all possible questions ahead of time.

It's 2015. You always knew that time travel was just around the corner.

## Getting back to the future.

When we record all significant changes and events as a sequence of immutable facts --- a stream --- and we derive all the answers to future questions from processing that stream, then all we need to add is persistence and high-availability, and ZAP! We have reliable time travel.

How so? Replay the same facts, get the same result. Released a bug? Got an extremely important message extremely late? Simply fix the bug, re-order the messages, then go back in time to before we made the mistake and replay the facts to get a repaired set of data. Could it be that simple?

> ![Wait a minute, Doc. Are you telling me we're going to fix bugs with time travel?](/assets/images/wait-a-minute-doc.jpg)

## An all-knowing log.

Enter [Kafka](http://kafka.apache.org/documentation.html#design). When it comes to Reactive stream-processing, the reason Kafka is significant among other message queues is that it's persistent by design, while still outperforming the rest. But, aren't disks slow?

Kafka takes advantage of the fact that *sequential* hard-disk operations can be faster than sequential SSD operations and, get this, even faster than random *memory* operations. This is surprising to most people. *You didn't read that wrong*. Sequential disk writes are thousands of times faster than random ones. It's the random writes that make us believe that disks are slow.

> [![Graph showing sequential disk operations measured as faster than in-memory operations.](http://deliveryimages.acm.org/10.1145/1570000/1563874/jacobs3.jpg)](http://queue.acm.org/detail.cfm?id=1563874)

Taking advantage of this, Kafka manages to be so fast that the filesystem is almost never the bottleneck, it's network access.

To put a nice bow on it, if you're not already using a binary serialization format like Avero to get smaller faster messages, Kafka uses end-to-end batch compression, on disk and over the network, until messages reach their final destination and are decompressed by the consumer. Batch compression takes advantage of the fact that among messages with a similar JSON schema, for example, a significant percentage of the required memory is due to the repetition of key names.

Given all this, we're talking about stream of data which can flow orders of magnitude faster.

I took this detour of diving into how Kafka is designed, because It's *very* significant to understand how fast Kafka is. Otherwise, these new approaches to thinking about data seem rather fanciful.

# Just the facts

## Streams as a collection of *immutable* facts  

Some people wouldn't consider immutability as being fundamental to being Reactive. However, if you're trying to be Reactive without taking advantage of immutability it's probably because you don't understand it.

The concept of immutability can take a little getting used to. What good is a program which can't affect state? It's counterintuitive enough that it's easy to think that it's one of those things that isn't of practical use out here in the real world, outside of academia. It's really quite simple and powerful though.

> ![immutability](/assets/images/immutability.jpg)

The first revelation is that we can achieve the same thing as mutability by simply making a copy of something immutable, and altering whatever we want as we do so, as opposed to making an identical copy. See? Simple. But is that really efficient? What's the point?

It helps, especially with Reactive, to think of immutable data as cold hard facts. We can react to the facts however we want, and even create new facts, but we can't change the facts.

If we could change the facts, then keeping distributed nodes in sync would quickly get out of control, and it would be quite easy to get confused about who's word is authoritative.

But with immutability, since we *can't* change the facts, multiple threads or even different nodes all over the galaxy can respond to them however they want, in their own context, without creating first-order dependencies.  

This concept of *facts* which are true regardless of when and where we come across them is extremely powerful all on it's own, even if it were to come at a cost to performance and efficiency. Yet, the opposite is actually true --- using immutable data can lead to *significant* performance gains.

> ![4 out of 5 algorithms prefer immutabilityy](/assets/images/4-out-of-5-algorithms.jpg)  

And just when you thought this deal couldn't get any sweeter, not only do computers and distributed systems function more efficiently with immutable data, it can actually help *computer programmers* work much more effectively! Sound to good to be true? Immutable data makes it easier to write cleanly *composable* components.

That's a pretty big deal.

> ![All the things immutable!](/assets/images/all-the-things-immutable.jpg)

## What does composable mean?

If something is composable it can be used in multiple contexts as part of a larger composition. Composable functions, for example, obviously require the right input, but they should *not* require a particular context. To be composable, components should be able to stand alone, and function just as well all by themselves as they do as part of a larger composition of multiple components nested within some other container --- given they are passed the same data.

The benefit of clean composition become apparent very quickly as we see increased productivity, re-usability, manageability, and a proper separation of concerns. It keeps code [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), and makes it natural to write components which ["Do One Thing"](http://blog.codinghorror.com/curlys-law-do-one-thing/).  

The cardinal rule of making a component cleanly composable is: Never let a function edit state outside of itself.

> ![Shared mutable state is bad, mmkay?](/assets/images/shared-mutable-state.jpg)

It *is* important to understand how state *isolation* helps us with clean composition, and in which cases immutability gives us a performance boost, with or without isolation. But once we understand those things we know enough to mutate responsibly. Lets just keep our state changes cleanly contained within a given component.

One of the many strengths of [Facebook's React JS](https://facebook.github.io/react/) library is that with a little guidance it's easy to make composable components with it. And then Facebook really drives home the power of clean composition by introducing [GraphQL and Relay](https://youtu.be/9sc8Pyc51uU?t=37s). GraphQL and Relay illustrate very well how clean composition simplifies managing the state of a complex tree of components. The result is astonishingly elegant --- elegant enough that it doesn't take being a hard-core functional programmer for it to click.

Being composable also goes hand in hand with [Compactness](http://www.faqs.org/docs/artu/ch04s02.html), allowing otherwise unwieldy problem domains to easily fit in a person's head, one nested branch at a time. One of the things that Reactive gets right, or any good programming paradigm, is that it recognizes that code will be written by human beings, and that to be successful it must thrive in the context of a human brain's workday.

# Decoupling from space and time

One of the great things about Reactive is that it achieves certain amazing things through the magic of making them part of it's definition! If it doesn't achieve them, then it's not Reactive. Well done! Well, that's sort of true. But it's really that at a fundamental level Reactive is solving the problem of being distributed and handling asynchronous processing in one swoop. This is why Reactive gets to claim such a big umbrella --- it's an elegant solution to making it easy to deal with two of the biggest problems which face modern systems. Because it solves these things at a function level, then it's only natural that the paradigm would stretch outward from software to systems architecture. The boundary between system nodes was always a fairly artificial place to make an architectural distinction between software and systems.  

> ![space-time continuum in air quotes](/assets/images/space-time-continuum.jpg)

## Distributed and elastic

When decoupling from *space* let's just make it easy and assume *all* software must work when distributed globally. If it's made easy and efficient, then why not?

### Docker? I hardly knew her.  

While we're negotiating the boundary between software and systems architecture, let's just make each service and node as cleanly composable as a well-behaved function. Container virtualization, such as you get with Docker, is exactly that. Containers are the atomic building blocks of services for constructing systems. Each container handles it's own dependencies and does only one thing. Each service lives in it's own container. But it doesn't matter if all those services run side-by-side on the same machine, or if they are spread all over the world. And in either case it doesn't change what is included in the container. This means that there is virtually no difference between a development environment and a production environment, except possibly how many nodes the containers are spread across.

Since containers carry even their OS around with them, an individual container can be run anywhere.

Am I just describing a certain use of virtual machines? Close. A big difference is that containers are extremely lightweight, using less memory and processing power than VMs, and they start up and shut down very quickly. So quick that starting up a service to run a single command and then shutdown is a reasonable thing to do. For a given service only the individual dependencies for the software are installed, including at an OS level. They act like distribution packages that take all their necessary junk with them, and don't need to be unpacked or download the dependencies.

Containers make it much easier to be elastic than with VMs. Spinning up new containers is simple and quick. Responsive just-in-time scaling is completely realistic and natural.

Gilt is an excellent example of this in action. Their business model is based around flash discount sales which are released once a day at the same time each day. Nearly all of their daily traffic hits them in less than an hour, and so they use Docker to be as elastic as they need to be. And when it's over, they snap back to handling the lower load as quickly and efficiently as they elastically stretched out. The rest of the day they are not paying for a bunch of unused nodes.

Being distributed and elastic like that means the ability to stretch out to handle increased load anywhere in the world, on-demand, as cost-effectively as possible.

## Non-blocking and asynchronous

As if time travel weren't a good enough way to decouple from time, let's also make sure our code is non-blocking and entirely at home in an asynchronous world. One of the biggest gains we get from Reactive Programming is that it makes it very easy to handle asynchronous data. In fact, it essentially lets you work with data without caring much about whether it's synchronous or not.

## Declarative vs imperative

Declarative composition is predictable and testable, and that means even new team members can make changes with confidence.

## Observers and iterators

There is a subtle difference between an enumerable type and an observable, and this subtle difference is the nugget which makes Reactive Programming *uniquely new*.

> At the core of Reactive Programming is the combination of using observables with iterators to lazily process any length collection, of any type, asynchronously.

> ![back to the future is real](/assets/images/backtofuturereal_o_158148.jpg)  

## Microservices

Compactness in the flesh. I think it's easier to think of *compactness* as the *bounded context* of the smallest reasonable problem domain.

> Everything and nothing is always new in Computer Science.

Microservices are yet another thing that's new, but not really. Just like Reactive, the concept is interesting only within the context of why it's trending again. It's interesting because of the things which are trending along with it.

There are enough benefits of microservices that the concept is compelling enough on it's own. But the concept really comes into it's own when thinking in Reactive.

It's not enough to think of microservices as services with a bounded context. To be Reactive they must also be composable. Clean composition makes complexity manageable, and when we're contemplating managing hundreds of microservices, manageability is a huge consideration.

## Immutable infrastructure and continuous delivery

Remember all those benefits of using immutable data? Some of those same benefits also apply if we don't ever change the code in any container in an infrastructure, effectively treating the code as immutable.

Pass the same immutable facts through the same immutable code, and we'll get exactly the same result.

If we find that we deployed a node with a bug in it, then we can quarantine that container, effectively forever. Because we won't ever *update* the code. Instead we will start running a new container to replace it.  

We can easily transition smoothly and cautiously to new containers, running side by side existing containers and start by handling only a small percentage of the traffic. Instead of creating new copies of the container to move from QA testing into Production, we can just start re-channeling some of the Production traffic to the exact same containers we used for testing.

And throughout this whole transition we have the confidence that comes from owning a time machine.

*"In theory, there is no difference between theory and practice. But, in practice, there is."*
--- Jan L. A. van de Snepscheut

# Failure is good. Adapt!

If you don't have the urge to rewrite the code you wrote <strike><span>2 years</span></strike> 6 months ago, you aren't keeping up. That's not to say that you *should* rewrite it, only that it should make you a little bit itchy to read it.

You will fail. There, we got that of the way. You will fail your first time, and you won't get it right the second time either. Your third attempt will be acceptable, so that your fourth time you can nail it and then go clean up after yourself a little. Plan right now to be prepared for this. It's okay. You are entering a new world -- or maybe just conquering a new challenge. Failure is more than okay --- it's the path to excellence.

What have we learned from the Agile Manifesto from 2001? We've learned that it's essence is still true, and often much more true than what consultants and certifications refer to as Agile today. Along the way, document the paths that work, but don't let you or others be constrained to only those paths.

In nature, when a species' habitat changes in a way that threatens their existence, they must migrate, adapt, or die. Watch the tactics of those who are succeeding. Learn from the mistakes of yourself and others. Adapt!

This is one of the beauties of bounded contexts, fault isolation, and time travel. Failure is an option! Establish an environment where it's safe for you and your team to fail, then fail fast and adapt in order to succeed.

# I stream. You stream...

One big reason I love Reactive is exactly what many people don't like about it: it's a very large umbrella. Words and language are for communication after all and having a word like Reactive allows a lot of information to be communicated between two people who both get it. Start a conversation with another developer by saying "Reactive?", and you quickly learn how much additional whiteboarding you'll have to do to explain the concept you're sharing. You can have a pretty good starting place to know what words and concepts you might share. Shared understanding is the basis of communication, after all.

I like that I can wrap all the concepts of this whole story in one word. The next epoch in computer science is about a lot of concepts and practices coming together at the same time to take our services to the next level. I don't think everything will be Reactive from here on out, any more than I think everything will be Functional. But for right now, while the word is *en vogue*, it's nice to be able to have a way to cut to the chase in discussing Computer Science, and to wrap all the most applicable best practices, old and newly-emerging, for our highly-distributed asynchronous world into single jumping off point.

Even if it's a fairly contrived wrapper, it's also a nice way to sneak some vegetables into very yummy discussions about fun shiny new things.

Reactive makes Computer Science more fun, but in a very pragmatic way, with big returns. Everybody happy!
