---
layout: post
title: "Redis Management Module Released"
description: ""
category: magento
tags: [magento, plugin, redis, cache]
---
{% include JB/setup %}

## v1.2.2 Released

Good news everyone! [Steverobbins_Redismanager v1.2.2](https://github.com/steverobbins/Magento-Redismanager) is now available for download.

## What does this module do?

This module allows a Magento admin to monitor and flush their Redis services.

Typically, when using the native Cache Management, Magento will only flush specific keys that match the area that was cleared.  The problem is, not all keys can be cleared with the native system.  "Stale" keys may get stuck in rotation for some time.  Previously, the only way to clear these keys was to flush the Redis db via command line.  Now you can skip the hassle and flush them with the click of a button.

### More Features

On occasion I've worked on Magento projects with, err, complicated hosting setups.  A common practice is to set up an admin server and load balanced customer facing servers.  Usually a single caching server will be used to share page caches and share session data (so a customers don't get logged out when bouncing around servers).  However some clients have elected to have each web head run their own Redis instance, that way if the caching server goes down mayhem doesn't ensue.  In this case I wanted a way to clear all of the Redis caches for all of the servers without having to connect to each one over command line.  That was the inspiration for this module.  In the case where you may have "funky" setups like this, you can add all of the services to your configuration and flush all of them nicely with a button click.  Huzzah.

## What is Redis and why should I use it?

By default, the caching system that comes with Magento is stored on the file system.  This may be appropriate for smaller stores, but repeatedly accessing the file system on the server is slow and eats up IO.  It's better to store temporary caches in the server's memory.  It can be read and wrote to much faster than the file system.

Redis is the ideal caching system that uses memory instead of file system.  Installing Redis is relatively simple and configuring your Magento installation is easy, too.  [Magento has published documentation on how to configure Redis for your store](http://www.magentocommerce.com/knowledge-base/entry/redis-magento-ce-ee).

## I'm on Community Edition.  Why don't I have Full Page Caching?

Magento has made FPC an Enterprise only feature.  FPC adds an extra layer to Magento's caching ability.

If you're on CE though there are several free FPC solutions.  I recommend using [Lesti FPC](http://gordonlesti.com/lestifpc/).  It's free, fast, and supported by this module.

---

For more details on the module and using Redis with Magento, check out these links:

* [https://github.com/steverobbins/Magento-Redismanager](https://github.com/steverobbins/Magento-Redismanager)
* [http://www.magentocommerce.com/knowledge-base/entry/redis-magento-ce-ee](http://www.magentocommerce.com/knowledge-base/entry/redis-magento-ce-ee)
* [http://inchoo.net/ecommerce/magento/using-redis-cache-backend-and-session-storage-in-magento/](http://inchoo.net/ecommerce/magento/using-redis-cache-backend-and-session-storage-in-magento/)
* [https://github.com/colinmollenhour/Cm_RedisSession](https://github.com/colinmollenhour/Cm_RedisSession)
* [http://info.magento.com/rs/magentocommerce/images/MagentoECG-UsingRedisasaCacheBackendinMagento.pdf](http://info.magento.com/rs/magentocommerce/images/MagentoECG-UsingRedisasaCacheBackendinMagento.pdf)
