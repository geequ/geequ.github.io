---
layout: post
title: Observer - the operating design pattern
categories: oop designpattern
---

### Description
Observer as an operating design pattern assumes that one object (subject) has at least one observer. If subject changes his state, all of his aggregated observers take a notification.
This pattern is one of possibilities to decouple dependencies between class/modules/bundles etc. We can use this as an event handler system or MVC application architecture (where M layer is a subject and V is an observer).

### Class diagram
<center>
![Class diagram observer pattern](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/observer_pattern.png)
</center>

### When use?
+ If a change in one object requires another object change and you don't know how many objects should change their state.
+ If your abstraction has 2 aspects and one depends on another. If you encapsulate these aspects in the separate objects, you have possibility to change and reuse each other independently.

As I wrote in description this pattern is used in MVC. This solution is very elegant, because M layer may exist whithout any knowledge about V layer. View has an observer role, so if there is a change in model layer then model notifies View that something was changed. It's up to the View decision whether this change will be shown to end user. As you see this is a good way and one of the possibilities to inverse control and loose dependencies.

### How to use?
In php we have interfaces which are embedded such as [SplObserver](http://php.net/manual/en/class.splobserver.php) and [SplSubject](http://php.net/manual/en/class.splsubject.php) which are a part of Standard PHP Library. For our example these interfaces are enough, but in my modest opinion sometimes they could bring some troubles (i.e. when we want to expand the original SplObserver interface for add aspects/conditions).

I want to show you an example application which goal is to change the exchange state when currency rate reaches  an expected level, for example: We assume that we have an exchange with 3.5 rate for USD currency. When selling rate reaches required level, we should change the state of this exchange (for our need we simplify example).

<center>
![Currency rate chart](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/rate_chart.png)
</center>

Lets begin with the rate deliver class which has a subject role and next I'll show you Exchange class with RateObserver interface.

{% gist 0f3e92842b9c962b4ace %}

{% gist 7966f7c56439d6051c7b %}

{% gist 8e3d349e73a442b4b2d0 %}

{% gist 0b8b2fbb44b3e3bf3649 %}

### Summary
What about pros and cons? I guess that Observer Pattern is very useful tool for loosing coupling between different aspects/parts of an application.
As you see Exchange class can exist without any knowledge about RateDelivery. Additionally, we can add another class with RateObserver interface, for example
we have to implement SMS notification:

{% gist 8918a40dc76b367ef977 %}

This pattern is a good example of one of SOLID rules (Open/Closed principle). 

On the other hand, we have some disadvanteges and for me the biggest one is a significant impediment debug process.