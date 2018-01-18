---
layout: post
title: Core Concepts Behind Node.js Non-Blocking I/O
description: "Core concepts behind Node.js non-blocking I/O"
modified: "2018-01-17 05:43:00 +0300"
category: [node.js]
tags: 
  - node.js
  - async
imagefeature: nodejs.png
location: "Tehran, Iran"
locationgps: null
mathjax: false
chart: false
comments: true
featured: true
published: true
---

Ryan Dahl in his original presentation at JSConf 2009 talked about doing the I/O differently which I’ll briefly go through it.
He mentioned that many web applications have code like this:

{% highlight javascript %}
var result = db.query('select * from T');
{% endhighlight %}

What is the software doing while it queries the database?
 - In many cases, just waiting for the response, I/O latency
 - Better software can multitask and other threads of execution can run while waiting.

Then he got through the event loop and how [Apache server](https://httpd.apache.org/) and the [Nginx](https://www.nginx.com/) are different. The Apache server uses a **thread per request** while the Nginx uses an **event loop**, and although the benchmark comparison of Apache vs Nginx in speed is not super cool, the amount of memory each server consumes differ a lot. While creating an OS new thread consumes more CPU clock cycles and memory, the single threaded event loop memory remains almost the same.

As you might have heard **Node.js is a purely evented, single-threaded non-blocking infrastructure**.
I’m going a little bit deeper through this, non-blocking I/O concept.
First of all, it should be clear that Node.js falls into the category of **concurrent computation**. 
The **concurrent** operation means that two computations can both make progress and advance regardless of the other. 

![Concurrent or Pre-emptive multithreading]({{ site.url }}/images/post/concurrent.png "Concurrent or Pre-emptive multithreading")
<figcaption>Concurrent or Pre-emptive multithreading</figcaption>

The **parallel** operation means that two computations are literally running simultaneously - at the same time.


![Parallel computation]({{ site.url }}/images/post/parallel.png "Parallel computation")
<figcaption>Parallel computation</figcaption>

In the computer world, every work is done through CPU cycles so if we consider a CPU as a salesman, Concurrency means a salesman who serves each client as fast as he can and passing the heavy loaded tasks to workers or delivery services, but Parallel means to have two salesmen at the same time. Being parallel means to take advantage of all CPU cores to process the requests.

Back to the story, Node.js is concurrent at the bare bone but can utilize all of the CPU cores capacity via [clusters](https://nodejs.org/dist/latest-v8.x/docs/api/cluster.html). But a simple node application can be very high performant because of its non-blocking I/O nature. Event loop in Node.js ([libuv](https://github.com/libuv/libuv) library) takes events and fire handlers which all of it happens sequentially in a single thread.

It might sound weird at the first glance and one of the very first questions that come to the traditional software developers mind is that a non-blocking I/O means there is a thread *waiting* for the I/O to be done but the main thread is still available to serve the rest of the requests.

But the most beautiful concept lying **there is no thread dedicated to running the task**. Actually, some sort of magic operations is happening at the OS level *(select, epoll, kqueue, IOCP)*.

Consider a typical write operation in Node.js:
{% highlight javascript %}
fs.writeFile('message.txt', 'Hello Node.js', (err) => {
  if (err) throw err;
  console.log('The file has been saved!');
});
{% endhighlight %}

In case of doing an overlapped I/O using IOCP in windows operating system, there are so many pieces in a puzzle involved to doing the job starting with the OS and triggering a device driver by constructing an IRP object, despite the fact the actual work is done through the *device* which it will eventually finish the writing and notifies the CPU via an interrupt. 

After so many magic happening the event loop will be notified and triggers the callback function while its single thread was free to process the rest of the requests. (For further explanation, I highly recommend to read the [Stephen Cleary](http://blog.stephencleary.com/2013/11/there-is-no-thread.html) post)

In the end, it worth to mention, Node.js has I/O bound considerations in mind and that’s why it is designed for and handles well using the single-threaded event loop.