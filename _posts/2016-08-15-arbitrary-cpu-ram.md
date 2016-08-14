---
author: <a href="https://github.com/ivanlomba">Iván Lomba</a>
comments: true
date: 2016-08-15 07:00:00+00:00
layout: post
slug: arbitrary-cpu-ram
title: Arbitrary CPU and RAM supported in the ComputeService
---

As part of a [Google Summer of Code](https://developers.google.com/open-source/gsoc/) project has been added a feature to allow users to set manually specific values of CPU and RAM.
<!--more-->

The previous Compute Service abstraction assumed that all providers had Hardware Profiles (some providers such as [Abiquo](http://www.abiquo.com/) or [CloudSigma](https://www.cloudsigma.com/) do not have the Hardware Profiles concept), and forced implementations of such providers to provide a fixed (hardcoded) list just to conform the interface. The new abstraction allows to use both (when supported) hardware profiles and custom hardwares.

### Providers supported
The first supported providers will be [Google Compute Engine](https://cloud.google.com/compute/) and [ProfitBricks](https://www.profitbricks.com/). To configure the new feature in other providers just add a bind() to the ArbitraryCpuRamTemplateBuilderImpl class at the provider context module:

{% highlight Java %}
bind(TemplateBuilderImpl.class).to(ArbitraryCpuRamTemplateBuilderImpl.class);
{% endhighlight %}

* Google Compute Engine
* ProfitBricks
* ...

### How to create custom hardwares?
There are two ways to create a custom hardware: setting in on the hardwareId or specifying minCores and minRam.


#### Creating a custom hardware with hardwareId
To set CPU and RAM with hardwareId you have to set the hardwareId in the template builder with the format:

`automatic:cores=2;ram=4096`

as you can see in the following example:

{% highlight Java %}
Template template = templateBuilder
    .hardwareId("automatic:cores=2;ram=4096")
    .build()
compute.createNodesInGroup("jclouds", 1, template);
{% endhighlight %}


#### Creating a custom hardware using minCores and minRam
To set up a machine with custom RAM and CPU you have to set minCores and minRam in the template builder.

As some providers support also Hardware Profiles, and using them provides hardware optimization and in some cases cheaper pricings, when a provider support both (Hardware Profiles and custom machines) Template Builder first will look if some Hardware Profile matches the specified values.

{% highlight Java %}
Template template = templateBuilder
    .minRam(4096)
    .minCores(2)
    .build();
compute.createNodesInGroup("jclouds", 1, template);
{% endhighlight %}
