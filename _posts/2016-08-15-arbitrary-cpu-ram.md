---
author: <a href="https://twitter.com/ivanlomba">Iván Lomba</a>
comments: true
date: 2016-08-15 07:00:00+00:00
layout: post
slug: arbitrary-cpu-ram
title: Arbitrary cpu ram supported
---

This new feature allows users to set manually arbitrary CPU and RAM values. The previous Compute Service abstraction assumed
that all providers had Hardware Profiles (some providers such as Abiquo or CloudSigma do not have the Hardware Profiles concept), and forced implementations of such providers to provide a fixed (hardcoded) list just to conform the interface.
<!--more-->

### How to use it?

There are two ways to create a custom hardware: setting the hardwareId or specifying minCores and minRam.

#### Creating a custom hardware with hardwareId

To set CPU and RAM with hardwareId you have to set the hardwareId in the template builder with the format: 

{% highlight Java %}
automatic:cores=numcores;ram=amountOfRam
{% endhighlight %}

as you can see in the following example:

{% highlight Java %}
Template template = templateBuilder.hardwareId("automatic:cores=2;ram=1024").build()
{% endhighlight %}

#### Creating a custom hardware using minCores and minRam

To set up a machine with custom RAM and CPU you have to set minCores and minRam in the template builder. As some providers support Hardware Profiles and using the hardware configured on them provides hardware optimization and in some cases cheaper pricings, when a provider support both (Hardware Profiles and custom machines) Template Builder first will look if some Hardware Profile matches the specified values.

{% highlight Java %}
Template template = templateBuilder.minRam(2048).minCores(2).build();
{% endhighlight %}

#### Providers supported 

* Google Compute Engine
* Amazon Web Services
* ...
