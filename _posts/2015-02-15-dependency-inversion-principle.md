---
layout: post
title: Dependency Inversion Principle
categories: oop solid
---

Dependency Inversion Principle (DIP) describes that high level modules should be independent of low level modules. Applications, which don't keep this rule, base on weak fundations and are hard to maintain or change.

How to perceive this situation in our applications? Just try to imagine what will be the result of changing? What would happen when you have to change your database, your mailer module or even framework?

Let's look on some very sad piece of code:

{% gist 4694285bad1088f5dbac %}

In this example we should see 2 issues:

- `Notifier` class depends on `SadMailer` class,
- `Notifier` class can use only `SadMailer` objects.

As you see high level logic in `Notifier` class can't exist without current low level mailer implementation. If you want to change `SadMailer` to external library you just can't because `Notifier` class requires this class. You can't change *mailer* class, because `Notifier` was created to send messages only by `SadMailer`. According to our intuition `Notifier` should have an influence to lower level of application's architecture. `Notifier` should be able to use another implementation of *mailer* tool. One more time, in other words: our business logic should be independent of our tools or databases or **anything from lower layer**.

Let's try to correct above incorrect example:

{% gist ccfc210091b22cd1859d %}

Now our `Notifier` class depends on interface (abstraction). In the above example you can change mailer to another implementation in a very easy and safe way. You can use `Notifier` in other project. Our `Notifier` now is a common tool and this is one of the best consequence of programming in according to object-oriented programming paradigm.



