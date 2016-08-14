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

The new abstraction allows to use directly arbitrary values of CPU and RAM specified by users or both (when supported) hardware profiles and custom hardwares. The previous Compute Service abstraction assumed that all providers had hardware profiles, providing a fixed configuration with a fixed (hardcoded) list just to conform the interface, but some providers such as  [Abiquo](http://www.abiquo.com/) or [CloudSigma](https://www.cloudsigma.com/) do not have the hardware profiles concept.

The previous Compute Service abstraction assumed that all providers had hardware profiles, but some providers such as [Abiquo](http://www.abiquo.com/) or [CloudSigma](https://www.cloudsigma.com/) do not have the hardware profiles concept and the previous implementation provides a fixed configuration with a fixed (hardcoded) list just to conform the interface. The new abstraction allows to use both (when supported) hardware profiles and custom hardwares.

### Providers supported
There are several providers that support arbitrary values of CPU and RAM like Abiquo, Cloud Sigma, Docker, Google Compute Engine, etc. The first available providers supported by the new feature are:

* [Google Compute Engine](https://cloud.google.com/compute/)
* [ProfitBricks](https://www.profitbricks.com/)

To configure the new feature in other providers just add a bind() to the ArbitraryCpuRamTemplateBuilderImpl class at the provider's context module:

{% highlight Java %}
bind(TemplateBuilderImpl.class).to(ArbitraryCpuRamTemplateBuilderImpl.class);
{% endhighlight %}


### How to create custom hardwares
There are two ways to create a custom hardware: setting in on the hardwareId or specifying cores and ram values using minCores and minRam.


#### Creating a custom hardware using hardwareId
To set CPU and RAM with hardwareId you have to set the hardwareId using template builder with the format:

`automatic:cores=2;ram=4096`

as you can see in the following example:

{% highlight Java %}
Template template = templateBuilder
    .hardwareId("automatic:cores=2;ram=4096")
    .build()
compute.createNodesInGroup("jclouds", 1, template);
{% endhighlight %}


#### Creating a custom hardware using minCores and minRam
To set up custom hardwares using minRam and minCores you have to set them using template builder.

{% highlight Java %}
Template template = templateBuilder
    .minCores(2)
    .minRam(4096)
    .build();
compute.createNodesInGroup("jclouds", 1, template);
{% endhighlight %}

As some providers support also hardware profiles, and using them provides hardware optimization and in some cases cheaper pricings, when a provider support both Template Builder first will look if some hardware profile matches the specified values.

### Special thanks

Special thanks to [Ignasi Barrera](https://github.com/nacx) for all the help, [Andrew Phillips](https://github.com/demobox) for code review and the rest of jclouds comunity.

Of course, also thanks to Google for running GSoC.
