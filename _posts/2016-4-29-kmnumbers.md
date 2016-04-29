---
layout: post
title: KMNumbers
---

Android library to shorten long numbers. For example: 10.000 -> 10.0K, 10.000.000 -> 10.0M.

The sample:

{% highlight java %}

KMNumbers.formatNumbers(1000L); // should return 1000,
KMNumbers.formatNumbers(10000L); // should return 10.0K,
KMNumbers.formatNumbers(1000000L); // should return 1.0M,
KMNumbers.formatNumbers(10000000L); // should return 10.0M.

{% endhighlight %}

This project is maintained at https://github.com/ShootrNetwork/kmnumbers

Contributors
============

* Development: _Arturo Jiménez_ ([@artjimlop](https://github.com/artjimlop)), _Jorge Martínez_ ([@jmartinez360](https://github.com/jmartinez360))