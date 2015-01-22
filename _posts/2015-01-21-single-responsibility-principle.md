---
layout: post
title: Single Responsibility Principle
categories: oop solid
---

First principle of SOLID (SRP) says to us that every class shouldn't be modified for more than one reason. Assuming that some class has responsibility for difference aspects of logic application i.e. we have situation like in the diagram below:

<center>
![Class diagram Invoice SRP](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/invoice_srp.png)
</center>

As you see we have simple class as a data structure. Let's say that our application prints invoices to PDF. At first sight it looks fine and does what it should do.

Now lets imagine that you have to connect with external REST API and main requirement is preparing request with your invoice in json format. You can, of cource, add extra argument to print() method to solve this issue. But, if you have to print this invoice in many different formats your Invoce class probably should change its name for InvoicePrinter. As you see the main responsibility of Invoice class may be lost in easy way. This class had too many things to do. From simple DTO, it evolves to something quite unexpected. 

So, I think we should refactor this class by extracting print() method to another class with common interface:

<center>
![Class diagram Invoice SRP](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/invoice_srp_interface.png)
</center> 

In the above picture we have three classes which implement common InvoicePrinter interface. Now we have clear boundaries between these two responsibilities (invoice representation and invoice printer). At the moment this part of logic application is easy to maintain and to change.

Additionally in this diagram we can see the next principle from SOLID and it's called ***Open/Closed principle***. 