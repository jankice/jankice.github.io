---
layout: post
categories:
  - Android
title:  Coroutine Basics
date:   2022-04-24 14:01:35 +0300
# image:  'backpressure.jpg'
tags:   kotlin
comments: true
---

Unlike many other languages with similar capabilities, async and await are not keywords in kotlin and are not even part of its standard library. 

**Kotlinx.coroutines is a rich library for coroutines developed by jetbrains.**

Coroutines, a very efficient and complete framework to manage concurrency in a more performant and simple way.

Co means cooperation and Routines means function. It means that when functions cooperate with each other, we call it as coroutines.

Coroutines and the thread both are multitasking. But the difference is that thread are managed by the OS and coroutines by the users as it can execute a few lines of function by taking advantage of the cooperation.

Coroutines are lightweight threads. (a lightweight thread means it dosen't map on the  native thread, so it doesn't require context switching on the processor, so they are faster.)

## There are two types of coroutines:
  1. Stackless
  2. Stackfull

Kotlin implements stackless coroutines - it means that the coroutines don't have own stack, so they don't map on the native thread.

Coroutines do not replace threads, It's more like a framework to manage it.


The exact definition of coroutines: A framework to manage concurrency in a more performant and simple way with its lightweight thread which is written on top of the actual threading framework to get the most out of it by taking the advantage of cooperative nature of function.


Need of kotlin coroutines:

Some Keywords from coroutines:

	1. Dispatchers: helps coroutines in deciding the thread on which work has to be done.
	There are main three types of dispatchers
	• IO (used to do the n/w and disk related work)
	• Default (used to do the CPU intensive work)
	• Main ( is the UI thread for android)
	
	2. Suspend: function is a function that could be started, paused and resume. Suspend functions are only allowed to be called from a coroutine or another suspend function.

To start the coroutine there are two function in kotlin

	1. launch{} :launch a new coroutine in background and continue  (does not return anything)
	2. async{} : returns an instance of Deferred<T>, which has await() function that returns the result of the coroutine like we have future in java which we do future.get() to get the result.


Scopes in kotlin coroutines: use when need to cancel the background task as soon as the activity is destroyed.
Type of scopes:

	1. GlobalScope: is used to launch top-level coroutines which are operating on the whole application lifetime and are not  cancelled prematurely.
	Global scope is operators running in Dispatchers.Unconfined, (
	A coroutine dispatcher that is not confined to any specific thread. It executes the initial continuation of a coroutine in the current call-frame and lets the coroutine resume in whatever thread that is used by the corresponding suspending function, without mandating any specific threading policy)which don't have any job associated with them.
	
	2. CoroutineScope: calls the specified suspend block with this scope. The provided scope inherits its coroutinesContext (
	The context of this scope. Context is encapsulated by the scope and used for implementation of coroutine builders that are extensions on the scope. Accessing this property in general code is not recommended for any purposes except accessing the Job instance for advanced usages.) from the outer scope. But overrides the context's job
