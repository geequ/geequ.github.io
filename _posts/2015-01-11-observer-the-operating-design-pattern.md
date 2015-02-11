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

{% highlight php %}

<?php

class RateDelivery implements \SplSubject
{
    /**
     * @var array
     */
    private $ratesArr = [];

    /**
     * @var array
     */
    private $observers = [];

    /**
     * @param string $currency
     * @param float $rate
     */
    public function refresh($currency, $rate)
    {
        $this->ratesArr[$currency] = $rate;
        $this->notify();
    }

    /**
     * @param SplObserver $observer
     */
    public function attach(SplObserver $observer)
    {
        if($observer instanceof RateObserver) {
            $this->observers[] = $observer;
            return;
        }

        throw new \InvalidArgumentException(sprintf('Observer must implement RateObserver interface.'));
    }

    /**
     * @param SplObserver $observer
     */
    public function detach(SplObserver $observer)
    {
        $index = array_search($observer, $this->observers);

        if (false !== $index) {
            unset($this->observers[$index]);
        }
    }

    public function notify()
    {
        foreach ($this->observers as $observer) {
            if(!$observer->isExpectedRate($this->ratesArr[$observer->currency()])) {
                continue;
            }

            $observer->update($this);
        }
    }
}

interface RateObserver extends \SplObserver
{
    /**
     * @param $rate
     *
     * @return bool
     */
    public function isExpectedRate($rate);

    /**
     * @return string
     */
    public function currency();
}

class Exchange implements RateObserver
{
    const STATE_OPEN = true;
    const STATE_CLOSE = false;

    /**
     * @var float
     */
    private $expectedRate = 0.0;

    /**
     * @var string
     */
    private $currency;

    /**
     * @var string
     */
    private $state;

    /**
     * @param string $currency
     * @param float $expectedRate
     */
    public function __construct($currency, $expectedRate)
    {
        $this->currency = $currency;
        $this->expectedRate = (float) $expectedRate;
        $this->state = self::STATE_OPEN;
    }

    /**
     * @param $rate
     *
     * @return bool
     */
    public function isExpectedRate($rate)
    {
        if(bcsub($this->expectedRate, $rate, 4) == 0) {
            return true;
        }

        return false;
    }

    /**
     * @return bool
     */
    public function isClosed()
    {
        return !$this->state;
    }

    /**
     * @return string
     */
    public function currency()
    {
        return $this->currency;
    }

    /**
     * @param SplSubject $subject
     */
    public function update(SplSubject $subject)
    {
        $this->state = self::STATE_CLOSE;
    }
}

$exchange = new Exchange('USD', 3.5000);

$exchangeState = $exchange->isClosed() ? 'closed' : 'open';
print(sprintf("Current exchange state:\t%s\n", $exchangeState));

$rateDelivery = new RateDelivery();
$rateDelivery->attach($exchange);

$rateDelivery->refresh('USD', 3.5000);

$exchangeState = $exchange->isClosed() ? 'closed' : 'open';
print(sprintf("Current exchange state:\t%s\n", $exchangeState));

{% endhighlight %} 

### Summary
What about pros and cons? I guess that Observer Pattern is very useful tool for loosing coupling between different aspects/parts of an application.
As you see Exchange class can exist without any knowledge about RateDelivery. Additionally, we can add another class with RateObserver interface, for example
we have to implement SMS notification:

{% highlight php %}

...

$rateDelivery = new RateDelivery;
$rateDelivery->attach($exchange);

$smsNotifier = new SmsNotifier();
$rateDelivery->attach($smsNotifier);

...

{% endhighlight %} 

This pattern is a good example of one of SOLID rules (Open/Closed principle). 

On the other hand, we have some disadvanteges and for me the biggest one is a significant impediment debug process.