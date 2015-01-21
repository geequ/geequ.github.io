---
layout: post
title: Single Responsibility Principle
categories: oop solid
---

First principle of SOLID (SRP) say us that every class shouldn't modified for more than one reason. Assume that some class has responsibility for difference aspects of application logic i.e. we have situation like on below diagram:

<center>
![Class diagram Invoice SRP](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/invoice_srp.png)
</center>

As you see we have simple class as a data structure. Let's assume that our application print invoices to PDF. At first sight it looks fine and do what it should do.

Now lets imagine that you have to connect with external REST API and main requirement is prepare request with your invoice in json format. You can, of cource, add extra argument to print() method to solve this issue. But, if you have to print this invoice in many different formats your Invoce class probably should change name for InvoicePrinter. As you see main responsibility of Invoice class may lost in easy way. This class had too many things to do. From simple DTO evolve to something quite unexpected. 

So, I think we should refactor this class by extracting print() method to another class with common interface:

<center>
![Class diagram Invoice SRP](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/invoice_srp_interface.png)
</center> 

On above picture we have three classes which implementing common InvoicePrinter interface. Now we have clear boundaries between these two responsibilities (invoice representation and invoice printer). At the moment these part of application logic is easy to maintain and to change.

Additionally on this diagram we can see the next principle from SOLID and it's ***Open/Closed principle***. 