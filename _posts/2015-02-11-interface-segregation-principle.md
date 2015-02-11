---
layout: post
title: Interface Segregation Principle
categories: oop solid
---

In current post we will analize Interface Segregation Principle on a simple Messanger UI implementation. Assume that we have to implement 3 ways to achieve our goal i.e. send message.

Let's look on the below example:

{% gist 66b99f97295a5e7824f0 %} 

And the client of above `MessangerUI` interface is:

{% gist e236fdb52fbc11c1dce4 %}

As you cetainly notice, in this example we have one interface `MessangerUI` which is common for every implementation of message sender (`SmsMessanger`, `EmailMessanger`, `CarrierPigeonMessanger`).

Now, let's imagine that we must implement **another** way to send message, for example `LetterMessanger`. In this case we have to add **another** method such as `requestLetterAmount()`. However, classes `SmsMessanger`, `EmailMessanger`, `CarrierPigeonMessanger` are connected (by `MessangerUI` interface) and they also must be rebuild. 

So, as I remember I want to add only one additional way, but something went wrong. This *something* is too thick `MessangerUI` interface. This interface has too much responsibility and some methods are absolutely not linked with current implementations! Why `SmsMessanger` has knowledge about another methods to sending? Our code is very sad.

To avoid situation like above we should create thin and cohesive interfaces. Let's try to correct our situation:

{% gist 7392a774ed8d995e950c %} 

Now `MessangerUI` interface has been splitted to 3 small and thin interfaces `SmsMessangerUI`, `EmailMessangerUI`, `CarrierPigeonMessangerUI`, but our client `SOAPMessangerUI` still use common interface `MessangerUI`. The result of this changes is reduction of coupling between messanger implementations.

Interface segregation principle say us that interface should be specific for one client. The less responsibility of interfaces, the more ease to manage our classes. To adapt our code to our needs we can use aggregation, composition, adapters or delegations. They are very powerful tools to control our code and mess in it.



