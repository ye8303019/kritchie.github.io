---
layout: post
title:  "Decoupling your systems with message queues"
date:   2017-07-17 20:15:00 +0800
categories: jekyll update
---

Lately I had a discussion with a colleague and I was explaining to him how one of our systems was built using message queues. I decided to make a blog post out of this discussion with the hope that some of you could benefit from that.
 
# We need this ASAP !
So many times, I hear about imaginary latency issues. Let me tell you something, not everything needs to be “real-time” or be accessed right now at this very nanosecond. Even if your system is not real-time, your system can still be fast to respond. It’s not a zero-sum game.

<img src="{{ site.github.url }}/assets/decoupled.png"/> 
 
Decoupling your system gives you time to re-adjust in cases something goes wrong with your back-end while your ingestion can still do it’s processing without interruption. It also deals with the spikes by stacking the processing tasks in the message queue. Your data processing will keep running nominally and will process those in due time.  What happens if you run out of space in your message queue and your data processing can’t keep up? Decouple more!
 
<img src="{{ site.github.url }}/assets/decoupled_more.png"/>

If you database back-end supports it, you can even launch a fleet of asynchronous workers that will be responsible to perform the I/O operations on your database and also empty the queue. This can be easily achieved using Lambda functions that you will launch concurrently in async mode.
 
That was a short post, but I think we sometimes go too complicated in our systems’ design trying to deal with issues we just don't have. 

Keep it simple, keep it decoupled.

Thanks guys,

Karl  

