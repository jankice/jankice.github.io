---
layout: post
categories:
  - Android
title:  Backpressure
date:   2020-04-28 14:01:35 +0300
# image:  'backpressure.jpg'
tags:   RX
comments: true
---

## What is Backpressure?
Some times an observable produces event so quickly that an observer downstream can't keep up with them. When this happens, you'll often experience a ```MissingBackpressureException```.

**So we can say that, Backpressure is simply the process of handling a fast item producer.**

Take a quick look of basics to continue with this topic.

**Observable**: It emits the values.

**Observer**: It gets the values from observable. How ? - observer gets the emitted values from observable by subscribing on it.

 **difference between subscriber and observer.**

1. In Rx Java 1  Subscriber allows to subscribe and unsubscribe, where an Observer only allows to subscribe.

2. In Rx Java 2 Observer is used to subscribe to an Observable, and Subscriber is used to subscribe to a Flowable. And if you want to be able to unsubscribe, you need to use ResourceObserver and ResourceSubscriber respectively.

So combining all the things, Backpressure is

When you have an observable which emits items so fast that consumer can’t keep up with the flow leading to the existence of emitted but unconsumed items.
How are the unconsumed items, emitted by observables but not consumed by subscribers, managed and controlled is what backpressure strategy deals with.
Since, it requires system resources to handle backpressure, you **need to choose right backpressure strategy that suits your requirement.**

**In Other words:**
Backpressure relates to a feedback mechanism through which the subscriber can signal to the producer how much data it can consume and so to produce only that amount.

There are some techniques to manage backpressure. I will demonstrate those. 
But before that lets create ```MissingBackpressureException```.

Which kind of observable creates a situation of backpressure?

There are two kind of observables that we can create


**1. Cold observable:** Cold observables begin emitting sequence of items as and when the observer demands from it. Without disrupting the integrity of the sequence. Cold Observables do pull model of processing. 
Examples: database query, file retrieval, or web request.

**2. Hot observable:** A hot Observable begins generating items and emits them immediately when they are created. Hot Observable emits items at its own speed, and it is up to its observers to keep up.
When the Observer is not able to consume items as quickly as they are produced by an Observable they need to buffered or handled in some other way, as they will fill up the memory, finally causing ```OutOfMemoryException```.
Example: mouse & keyboard events, system events, or stock prices.


First create a print function for simple printing argument.
```java
public static void print(Integer v) 
{
    try {
        System.out.println("print integer v: " + v);
        //to emulate some long running task that will 
        //cause observable to fill up with items quicker that 
        //observer can consume them
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

Now, Lets start with cold observable example
the observer is taking elements only when it is ready to process that item and items do not need to be buffered in an observable because they are requested in a pull fashion. So, if you create an observable based on static range of elements from one to million, that observable would emit the same sequence of items no matter how frequently  those  items are observed.
```java
 public static void main(String[] args) throws InterruptedException 
 {

	Observable.range(1, 1000000)
  		.observeOn(Schedulers.computation()) //  run observer within computation thread pool 
  		.subscribe(ComputeFunction::print);
		
        Thread.sleep(10000); //  thread will sleep for 10 sec.
}
```
```text
Output: 
print integer: 1
print integer: 2
print integer: 3
print integer: 4
..
..
..
print integer: 10
```

In above cold observables do not need to have any form of backpressure because they working in a pull fashion.

Let's consider an example of hot observable that is producing a million items to a consumer that is processing those items at a slower rate, the Observable is starting to fill up a memory with items, causing a program to fail:

```java
`public static void main(String[] args) throws InterruptedException 
{
    		
    PublishProcessor<Integer> source = PublishProcessor.create();
    source.observeOn(Schedulers.computation())
          .subscribe(BackpressureExample::print,Throwable::printStackTrace);
    
    for (int i = 0; i < 1000000; i++) 
    {
        source.onNext(i);
    }
    
    Thread.sleep(10000); //  thread will sleep for 10 sec.
}`
```

  Running this program will fail with a MissingBackpressureException because we didn't define a way of handling fast producing Observable.

	> print integer: 0
	io.reactivex.exceptions.MissingBackpressureException: Could not emit value due to lack of requests
		at io.reactivex.processors.PublishProcessor$PublishSubscription.onNext(PublishProcessor.java:364)
		at io.reactivex.processors.PublishProcessor.onNext(PublishProcessor.java:243)
		at com.task.rxdemo.Backpressure.main(Backpressure.java:18)

Here are some technique to overcome with this kind of exception. Detail explanation in next section.
	
**1. Collecting Items:** In this technique items are collected and emitted as a collection using operators.
			This way consumer (subscriber/observer) receives collection of items which are emitted by source Observable, and it is up to consumer to decide which items to process out of the collection.

**2. Reactive pull:** In this strategy Subscriber request required number of items from Observable. by calling `request()`.

**3. Backpressure mode:** 

**4. Reducing no of items:** Sometimes App dosn't need all the items emitted by observable. In this case reduce no of items which are emitted by observable using `take()`, `debounce()`, `range()`.

