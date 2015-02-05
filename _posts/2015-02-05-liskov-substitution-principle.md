---
layout: post
title: Liskov Substitution Principle
categories: oop solid
---

In the last post about SOLID principles I wrote about `Invoice` class. Now let's imagine that we have to implement another kind of document and this is **Receipt** and in both cases we should have a chance to correct these documents, but in every case we should use different approach to reach our goal. We can produce class methods like  below:

{% highlight php %}

<?php

class Document
{
	const INVOICE = 'invoice';
	const RECEIPT = 'receipt';

	/**
     * @var string
     */
	private $documentType;

	public function __construct($documentType) 
	{
		$this->documentType = $documentType;
	}
}

class DocumentCorrectionService
{
	public funtion makeInvoiceCorrection(Invoice $invoice)
	{ ... }

	public funtion makeReceiptCorrection(Receipt $receipt)
	{ ... }
}

$document = new Document(Document::INVOICE);
$correctionService = new DocumentCorrectionService();

if($document->isInvoice()) {
	$correctionService->makeInvoiceCorrection($document);
}

if($document->isReceipt()) {
	$correctionService->makeReceiptCorrection($document);
}

{% endhighlight %}

Our implementation does what it should do. We have two conditions to check what kind of document we are correcting now. But what is going on when we have to add another kind of documents, for example pro forma? We must change our implementation in two places (in this case; in real application we will have probably more complex situation). We must add another method to `Document` class like `makeProformaCorrection`. Now our code is very sad.

So, what should we do?

When you see mechanisms of type verification such as the example above, you  know that you have broken LSP. And in the next consequence you have probably broken [OCP](http://geequ.github.io/oop/solid/2015/01/26/open-closed-principle/). At first, let's begin with creating another implementantion for every kind of document and let's assume that `Document` class is a parent for everyone (attention! Inheritance isn't a tool for storing common code. We should use this approach when it's natural. If you want to avoid duplicating, you should use a [template method pattern](http://en.wikipedia.org/wiki/Template_method_pattern)):

{% highlight php %}

<?php

interface CorrectableDocument
{
	public function makeCorrection($args);
}

abstract class AbstractDocument implements CorrectableDocument
{}

class Receipt extends Document implements CorrectableDocument
{}

class Invoice extends Document implements CorrectableDocument
{}

class ProformaInvoice extends Document implements CorrectableDocument
{}

{% endhighlight %}

Now let's look how to use this approach:

{% highlight php %}

$receipt = new Receipt();
$invoice = new Invoice();
$proforma = new ProformaInvoice();

$documentsArr = [$receipt, $invoice, $proforma];

foreach($documentsArr as $document) {
	$document->makeCorrection($args);
}

{% endhighlight %}

As you see we have now common interface to make corrections. Moreover, in this case our implementation is flexible, safe and clear for clients and open to extension.

In the above example we see specific design approach and it's *design by contract*. LSP is a powerful tool to support this way of designing of our applications. While open closed principle is one of fundamental OOP design rule, Liskov Substitution Principle is one of [OCP](http://geequ.github.io/oop/solid/2015/01/26/open-closed-principle/) conditions. LSP gives us possibilities to extend our modules without changing them directly.
