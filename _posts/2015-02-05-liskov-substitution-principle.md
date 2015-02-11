---
layout: post
title: Liskov Substitution Principle
categories: oop solid
---

In the last post about SOLID principles I have written about `Invoice` class. Now let's imagine that we have to implement another kind of document and this is **Receipt** and in both cases we should have a chance to correct these documents, but in every case we should use different approach to reach our goal. We can produce class methods like  below:

{% gist 75f230495ce8b27d8853 %}

{% gist fc81895d184dfbcd5342 %}

{% gist fd1678eb103a14659321 %}

Our implementation does what it should do. We have two conditions to check what kind of document we are correcting now. But what is going on when we have to add another kind of documents, for example pro forma? We must change our implementation in two places (in this case; in real application we will have probably more complex situation). We must add another method to `Document` class like `makeProformaCorrection`. Now our code is very sad.

So, what should we do?

When you see mechanisms of type verification such as the example above, you  know that you have broken LSP. And in the next consequence you have probably broken [OCP](http://geequ.github.io/oop/solid/2015/01/26/open-closed-principle/). At first, let's begin with creating another implementantion for every kind of document and let's assume that `Document` class is a parent for everyone (attention! Inheritance isn't a tool for storing common code. We should use this approach when it's natural. If you want to avoid duplicating, you should use a [template method pattern](http://en.wikipedia.org/wiki/Template_method_pattern)):

{% gist e89de72accf4de577a07 %}

Now let's look how to use this approach:

{% gist 94b2b4509c714d881284 %}

As you see we have now common interface to make corrections. Moreover, in this case our implementation is flexible, safe and clear for clients and open to extension.

In the above example we see specific design approach and it's *design by contract*. LSP is a powerful tool to support this way of designing of our applications. While open closed principle is one of fundamental OOP design rule, Liskov Substitution Principle is one of [OCP](http://geequ.github.io/oop/solid/2015/01/26/open-closed-principle/) conditions. LSP gives us possibilities to extend our modules without changing them directly.
