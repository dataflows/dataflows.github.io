---
layout: post
title:  "Blazing fast app development with formular database"
subtitle:  "Part I"
author: filip
date:   2015-08-25 21:00:00
categories: vision
tags: business software formular database formula formulas Excel applications Dataflows
image: /assets/article_images/2015-08-25-blazing-fast-apps/header.jpg
---
The way we program has to change. Too often we spend much more time on setting up the development environment, deployment, handling authorization and data updates than on the actual problem.

We’re not the first ones saying this. There has been much said starting from [Alan Kay](http://blog.moryton.net/2007/12/computer-revolution-hasnt-happened-yet.html) in 1997.  A strong recent voice is [Chris Granger](http://www.chris-granger.com/2014/03/27/toward-a-better-programming/).

Many applications are essentially a database, some calculations on the data and an interface. It should take just a few minutes or hours to build such tool. But it still takes days or weeks.

#Flawed workarounds
So lets take a minute and think about the alternatives.

Sometimes there is a dedicated startup that does exactly what you want.

Many people, even programmers, resort to spreadsheets when they need a tool to collect and process data. On the first day it is okay. After one or two weeks you get irritated and after a few months it is completely unuseable. Spreadsheets mix logic with data and are unstructured. Therefore, they are unmaintainable, hard to reason about and store hierarchical data.

Of course you can always build an app from scratch if you’re programmer, but often you don’t want to spend your time on that.

#Solution.
We need a new way of creating applications that collect, analyze and present data. It has to be as easy as creating a spreadsheet, but provide a robust interface and be capable of storing any data structure. The key for simplicity in spreadsheets are reactive formulas. Results are recalculated without programmer having to even think about that.

The solution is a one place for defining both the base data structure and anything we want to calculate based on that data.

Output from one calculation is usually an input to the next one. That’s why there is not much difference between base data and computed results. Computations and base data should live in the same place.

This is what a formular database provides. Data schema and computations are defined together, making things much cleaner. The database takes care of updating the results when dependencies change.

Let’s say we want to collect and analyze data about our sales. Usually we would keep the base data in some database, and define the calculations in the application code. With a formular database, we can express entire data model and business logic in a few lines:

{% highlight coffee %}
Salesmen = Table(name: Text)
Clients = Table(name: Text, longitude: Number, latitude: Number)
Deals = Table(salesman: ref Salesmen, client: ref Client, date: Date,
              value: Number)

DealsByMonth = GROUP_BY(Deals,
    (deal) -> (deal.date.year, deal.date.month))
SalesPerMonth = MAP(DealsByMonth,
    (month, deals) -> (month, SUM(MAP(deals, (deal) -> deal.value))))
{% endhighlight %}

This snippet defines a fully functional application. Formular database generates a dedicated API for this model. This API lets you change each of the base values, recalculates dependent fields and reactively pushes updates to all clients.

<center>
    <img src="/assets/article_images/2015-08-25-blazing-fast-apps/database.png" />
</center>

The true power of tying computations to data is composition. It lets you build models based on simpler models. This is how people think, because real-world data is hierarchical by nature. We will elaborate on how hierarchical models are handled in Part 2 of the series.

One of the core features is that this approach provides natural way of adding unit tests. No one argues with the fact that software has to be automatically tested. When model is fully defined in one place, it gets incredibly easy to create unit tests.

#The Big Thing.
We have built this. We want that technology to exist because we are kind of lazy and kind of paranoid about solid, extendable software.

Currently it’s still at an early stage. There’s a lot of work to do, but we have several models of different sizes implemented and used by companies on their data. And after our initial feedback we want to share it with you as well.

It turns out that this approach simplifies a lot not only for programmers. The cool thing that happened is that the model language is understandable to some non-programmers.

One of our clients has rebuilt their huge insurance model built in Excel into solid software. During that time we’ve seen amazing things! And by saying amazing I really mean that. Non-programmers have started to do peer review of their formulas on Github. Short after that they’ve adapted to use issues and labels. Oh, and did I mention each model is under continuous integration? As it can be tested automatically, setting this up is same as for developers. It saves hours of manual work on testing spreadsheets.

#There is more.
Do we need another database? Probably we don’t, and we don’t aim at building one. We believe in declarative, truly reactive modelling though.

And we believe in spreadsheet’s formulas. There is some magic in how easy it is to grasp Excel for 12 years old kid and a guy with an MBA. Part of that magic is instant feedback and that’s why we want much more.

While working with our users we found out it’s possible to automatically generate visual interface for each model. In fact it emerges quite naturally. In short, we generate a web app which is an interface to the database. It reflects the structure of the model. It enables editing any contained data, at the same time presenting all calculated values in a read-only mode.

[fotki]

The video below shows an early preview of how this looks. Each app is synchronized with database and other clients in real-time. There are different elements that can be binded to data like charts, maps, calendars etc. The app can be extended with custom components as well.

{% youtube u8O1gBehZOc %}

#What’s new here?
Formular database lets you build applications for collecting, processing and presenting data several times faster.

By keeping the schema and calculations in the same place, formular database is a next step after databases such as Firebase and RethinkDB, which also push updates to clients. Oracle APEX and MS Access are somewhat similar in that they streamline building applications on top of databases, but are heavy and not hackable.

There are other projects exploring the same direction, including Eve and Unison. They are incredibly promising and we keep our fingers crossed for their success. They try to solve somehow overlapping problems but in different ways.

#Great, now what?
We want our technology to be open source. We are going to publish the whole thing as soon as it is ready for the preview.

We look for your feedback and support. Please subscribe to our waitlist to make sure you’ll be first to hear about our progress.


<form class="ui form" action="http://excelpanel.us11.list-manage.com/subscribe/post?u=7931d786a3e0a59b9dffd9d10&amp;id=74dd56c069" method="post">
  <div style="position: absolute; left: -5000px;">
    <input type="text" name="b_7931d786a3e0a59b9dffd9d10_74dd56c069" tabindex="-1" value="">
  </div>
  <div>
      <div style="float:left;">
        <input type="email" name="EMAIL" required="" placeholder="Enter your e-mail" id="mce-EMAIL" class="required email emailinput">
      </div>
      <div style="float:left">
        <input type="submit" value="Sign up" class="submitbutton">
      </div>
  </div>
</form>
<div style="clear:both">

If you want to get in touch you can reach me via [email](mailto:filip@dataflows.io) or [twitter](https://twitter.com/filipstachura).
