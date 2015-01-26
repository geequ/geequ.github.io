---
layout: post
title: Open Closed Principle
categories: oop solid
---

Recently I wrote a short note about [SRP](http://geequ.github.io/oop/solid/2015/01/21/single-responsibility-principle/ "single responsibility principle") and I ended with nowadays principle i.e. Open Closed Principle (OCP).

In general way, we should use this principle when we suspect that something will be probably change or when we see that some changes result in necessity makes modifications in bigger part of our application than we expected.

As result of correct implementation this principle should be abbility to easy add some function without change any founded code. Our code must be **open for extension, but closed for modification**.

Let's see at the recent Invoice class:

<center>
![Class diagram Invoice SRP](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/invoice_srp.png)
</center>

Let's assume firstly that our Invoice class has hardcoded `print()` method which result is PDF file. I'm certain that you see some issue with this method. In this case we don't have any  possibility to change result of this method without change this implementation. This method is closed for extension. We cannot add another output for this method. Let's try change our bad situation and use below interface.

<center>
![Class diagram Invoice SRP](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/invoice_srp_interface.png)
</center> 

At the above picture we assume that Invoice class shouldn't responsible for print document, cause it should be simple data structure (see [SRP](http://geequ.github.io/oop/solid/2015/01/21/single-responsibility-principle/ "single responsibility principle")). In order to correct implementation of OCP we have to refactor Invoice class by removing `print()` method.

*InvoicePrinter* interface give us simple method to add another way to print invoice. We can just add another implementation of printer with common interface. As you see we mustn't change anything. Now you can have `XMLPrinter`, `JSONPrinter`, `TXTPrinter` or even `MorseCodePrinter`.
