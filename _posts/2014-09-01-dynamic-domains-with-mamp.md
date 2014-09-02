---
layout: post
title: "Dynamic Domains with MAMP (the free way)"
description: "Dynamically associate web urls with file system paths on your local developement system"
category: apache
tags: [mamp, apache]
---
{% include JB/setup %}

Let's figure out how to set up MAMP so that visiting `<project>.local.mysite.com` will give you the website in `~/public_html/<project>` without having to change MAMP's settings for each project.

With the free version of MAMP you can only view one project at a time.

![MAMP Config](http://i.imgur.com/YbC1LA2.png)

This can be a huge pain when you work with multiple clients/projects and frequently switch between them.  MAMP Pro has a [Virtual Server](http://www.mamp.info/en/mamp-pro/#vhosts) feature that can help with this, but not all of us are willing to shell out the extra dough.

Lucky, there's a way you can do this for free[^1] and it only takes two steps:

### Configure your DNS

Say you have a domain you own, mysite.com.  We need to set up a wildcard DNS entry so that any request to certain URLs will return `127.0.0.1`.  It should look something like this:

![local setup](http://i.imgur.com/bRi1fyx.png)

Or if you really know what you're doing:

    *.local 10800 IN A 127.0.0.1

Once that propagates, we should be able to ping any domain at `*.local.mysite.com` can get back `127.0.0.1`

    $ ping -c 1 foo.local.mysite.com 
    PING foo.local.mysite.com (127.0.0.1): 56 data bytes

    $ ping -c 1 bar.local.mysite.com 
    PING bar.local.mysite.com (127.0.0.1): 56 data bytes

### Configure MAMP

Next we need to configure a VirtualHost on MAMP to dynamically server a folder based on the name in the URL.

To do this, copy and paste this to the end of your `/Applications/MAMP/conf/apache/httpd.conf` file (and replace `yourname` and `mysite.com` with your actual values):

    NameVirtualHost *
    <VirtualHost *>
        DocumentRoot "/Applications/MAMP/htdocs"
        ServerName localhost
    </VirtualHost>
    <VirtualHost *>
        ServerName local.mysite.com
        UseCanonicalName Off
        ServerAlias *.local.mysite.com
        <Directory />
            Options FollowSymLinks
            AllowOverride None
        </Directory>
        <Directory /Users/yourname/public_html/*>
            Options Indexes FollowSymLinks MultiViews
            AllowOverride All
            Order allow,deny
            allow from all
        </Directory>
        VirtualDocumentRoot /Users/yourname/public_html/%1
    </VirtualHost>

Now stop and start MAMP's servers.

---

Boom!  Visit `foo.local.mysite.com` and MAMP will serve the document root from `~/public_html/foo`.

---

[^1]: Assuming you already own a domain name and have access to it's DNS zone file