---
layout: post
title: "Static File Versioning Module Released"
description: ""
category: magento
tags: [magento, plugin]
---
{% include JB/setup %}

## v1.0.1 Released

I've created another module, [Codingrobot_Staticversion v1.0.1](https://github.com/steverobbins/Magento-Staticversion).

<a href="https://github.com/steverobbins/Magento-Staticversion/archive/master.tar.gz" class="bigOlButton">Magento-Staticversion-master.tar.gz</a>

## What does this module do?

This module gives the admin better control of managing local caching for their static files.  For instance, if a customer visits your site the `styles.css` file is going to get cached in their browser.  You might make a change to this file, but because of their cache they won't see the update.  Unless you can convince all your users to delete their browser history, you'll have to wait a week or so for the change to take effect (depending on your server/cdn settings).

This module can help with that.  Instead the user will download a file called `styles.123.css` and cache that.  The contents of this file are the same as `styles.css`.  However, once an update is made, the admin can clear the cache and now the user will need to download `styles.124.css`.  Since this is a completely new file, the browser will have no choice but to retrieve the updated version. 

## What files are supported?

At this point CSS, JS, and image files in `skin` and `media` have been tested and are supported by the module.  The exception is files in the `js` dir (e.g. `example.com/js/prototype/prototype.js`).  These files shouldn't be changing too often anyway.  The reason for this is there isn't a non-intrusive way to add this feature without aggressively rewriting the `Mage_Page_Block_Html_Head::_prepareStaticAndSkinElements()` method and running into conflicts with other modules/Magento versions.  This may change in the future however.

## Inspiration

The idea for this module came after seeing the [Bubble_Queryfier](https://github.com/jreinke/magento-suffix-static-files) module.  This module is similar except it adds the unique identifier as a query string (e.g. `styles.css?q=123`).  This will work however Google recommends that you [remove query strings from static resources to improve page speed](http://www.htpcbeginner.com/remove-query-strings-from-static-resources/).

So I decided to employ a similar approach to a [personal site](http://permissiondenied.net/) of mine.

I originally planned to use a controller and get the [`filemtime`](http://php.net/manual/en/function.filemtime.php) for each file, however I was met with some challenges.

First off a controller wasn't ideal do to how it uses query params.  For instance

    $this->getUrl(
        'staticversion/file/skin',
        array('name' => 'base/default/styles.123.css')
    );

results in a url of `example.com/staticversion/file/skin/name/base/default/styles.123.css/`.  This means the `$_GET` params in the controller would have values of

    array(
        'base'          => 'default',
	    'styles123.css' => ''
    );

which isn't ideal (though I guess they could be parsed with some trickery... hmm).  There's also a trailing slash added to the URL which might act funny in different browsers.  Logically I would also have to use `file_get_contents` and set headers in the response which I like to avoid when possible.

This lead me to an `.htaccess` Rewrite solution, which means Apache only for now but I plan on coming up with Nginx support.

---

For more details on the module and using Redis with Magento, check out these links:

* [https://github.com/steverobbins/Magento-Redismanager](https://github.com/steverobbins/Magento-Redismanager)
* [http://www.magentocommerce.com/knowledge-base/entry/redis-magento-ce-ee](http://www.magentocommerce.com/knowledge-base/entry/redis-magento-ce-ee)
* [http://inchoo.net/ecommerce/magento/using-redis-cache-backend-and-session-storage-in-magento/](http://inchoo.net/ecommerce/magento/using-redis-cache-backend-and-session-storage-in-magento/)
* [https://github.com/colinmollenhour/Cm_RedisSession](https://github.com/colinmollenhour/Cm_RedisSession)
* [http://info.magento.com/rs/magentocommerce/images/MagentoECG-UsingRedisasaCacheBackendinMagento.pdf](http://info.magento.com/rs/magentocommerce/images/MagentoECG-UsingRedisasaCacheBackendinMagento.pdf)
