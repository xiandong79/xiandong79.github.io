---
layout: post
title:  Readability of large integer literals
date:   2017-02-28 22:17:00
categories: beginner
---

Sometimes we write programs which contain literals numeric. Large numbers can be very difficult to read.

How many zeroes are there?

{% highlight ruby %}
  billion = 1000000000
  ten_billion = 10000000000
{% endhighlight %}

In Ruby we can add underscore to large numeric literals to improve their readability.

{% highlight ruby %}
  billion = 1_000_000_000
  ten_billion = 10_000_000_000
{% endhighlight %}

As you can see it was much easier to read.