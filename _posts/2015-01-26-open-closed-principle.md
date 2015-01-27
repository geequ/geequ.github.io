---
layout: post
title: Open Closed Principle
categories: oop solid
---

Recently I wrote a short note about [SRP](http://geequ.github.io/oop/solid/2015/01/21/single-responsibility-principle/ "single responsibility principle") and I ended with a principle i.e. Open Closed Principle (OCP).

In general, we should use this principle when we suspect that something will probably be changed or when we see that some changes make the necessity of modification of bigger part of the code than we expected before.

The correct implementation of this principle should create possibility to add some functions easily without any change of the code we had before. Our code must be **open for extension, but closed for modification**.

Let's see at the recent Invoice class:

<center>
![Class diagram Invoice SRP](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/invoice_srp.png)
</center>

Firstly, let's assume that our Invoice class has hardcoded `print()` method which results is PDF file. I'm certain that you see some issues with this method. In this case we don't have any possibilities to change the result of this method without changing this implementation. This method is closed for extension. We cannot add another output for this method. Let's try to change our bad situation and use the interface below.

<center>
![Class diagram Invoice SRP](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/invoice_srp_interface.png)
</center> 

In the above picture we assume that Invoice class shouldn't be responsible for print document, cause it should be simple data structure (see [SRP](http://geequ.github.io/oop/solid/2015/01/21/single-responsibility-principle/ "single responsibility principle")). In order to correct the implementation of OCP, we have to refactor Invoice class by removing `print()` method.

*InvoicePrinter* interface gives us simple method to add another way to print invoice. We can just add another implementation of printer with common interface. As you see we don't have to change anything. Now you can have `XMLPrinter`, `JSONPrinter`, `TXTPrinter` or even `MorseCodePrinter`.
