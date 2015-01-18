---
layout: post
title: Observer - the operating design pattern
categories: oop designpattern
---

### Description
Observer as a operating design pattern assume that one object (subject) has at least one observer. If subject change his state all of his aggregates observers take a notification.
This pattern is a one of possibility to decouple dependencies between class/modules/bundles etc. We can use this as a event handler system or MVC application architecture (where M layer is a subject and V is an observer).

### Class diagram
<center>
![Class diagram observer pattern](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/observer_pattern.png)
</center>

### When to use?
+ If change in one object require change another object and you don't know how many objects should change their state.
+ If your abstraction has 2 aspects and once depends on another. If you encapsulate this aspects in the separate objects you have possibility to change and reuse independent each other.

As I wrote in description this pattern is used in MVC. This solution is very elegant, because M layer may be exists whith any knowledge about V layer. View has observer role, so if in model layer was changed anything then model notify view that something was change. Now from View decision depends if this change has shown to for example end user.
As you see this is good way and one of possibilities to inverse control and loose dependencies.

### How to use?
In php we have embedded interfaces such as [SplObserver](http://php.net/manual/en/class.splobserver.php) and [SplSubject](http://php.net/manual/en/class.splsubject.php) which are a part of Standard PHP Library. For our example this interfaces are enough, but in my modest opinion sometimes they could bring some troubles (i.e. when we want expand origin SplObserver interface for add aspects/conditions).

I want to show you an example application whose goal is change exchange state when currency rate reach expected level, for example: We defined an exchange with 3.5 rate for USD currency. When selling rate reach two rates 3.45 and 3.55 we should change state this exchange (for our need we simplifing example).

<center>
![Currency rate chart](http://googledrive.com/host/0B7zLbvESXLZlclJiMW5HMTMyYVk/rate_chart.png)
</center>

Lets begin from rate deliver class which has a subject role and next I show you Exchange class with RateObserver interface.

{% highlight php %}

<?php

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
        if(in_array('RateObserver', class_implements($observer))) {
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
What about pros and coins? I guess that Observer Pattern is very useful tool for loosing coupling between different aspects/parts of application.
As you see Exchange class can be exists without any knowledge about RateDelivery. Additionally we can add another class with RateObserver interface, for example
we have to implement SMS notification:

{% highlight php %}

...

$rateDelivery = new RateDelivery;
$rateDelivery->attach($exchange);

$smsNotifier = new SmsNotifier();
$rateDelivery->attach($smsNotifier);

...

{% endhighlight %} 

This pattern is good example of one of SOLID rule (Open/Close principle). 

In other hand we have disadvanteges and for me the biggest problem with this pattern is significant impediment debug process.