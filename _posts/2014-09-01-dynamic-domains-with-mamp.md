---
layout: post
title: "Dynamic Domains with MAMP (without MAMP Pro)"
description: "Dynamically associate web urls with file system paths on your local developement system"
category: apache
tags: [mamp, apache]
---
{% include JB/setup %}

Let's figure out how to set up MAMP so that visiting `<project>.vcap.me` will give you your local website in `~/public_html/<project>` without having to change MAMP's settings for each project.

With the free version of MAMP you can only view one project at a time.

![MAMP Config](http://i.imgur.com/YbC1LA2.png)

This can be a huge pain when you work with multiple clients/projects and frequently switch between them.  MAMP Pro has a [Virtual Server](http://www.mamp.info/en/mamp-pro/#vhosts) feature that can help with this, but not all of us are willing to shell out the extra dough.

Lucky, there's a way you can do this for free and it only takes two steps:

### Setting up the DNS

We need a domain that will always spit back `127.0.0.1` as the destination, regardless of what subdomains we give it.  We can either use an already public one, or create your own on a domain you own.

#### Using vcap.me

We're borrowing the domain that [VMware uses for development](https://github.com/cloudfoundry-attic/vcap).  No matter what subdomain you ask for on vcap.me, it will always come back with an IP of `127.0.0.1`, which your computer recognizes as the address for itself.

For instance:

    $ ping -c 1 foo.vcap.me 
    PING foo.vcap.me (127.0.0.1): 56 data bytes

    $ ping -c 1 bar.vcap.me 
    PING bar.vcap.me (127.0.0.1): 56 data bytes

We're going to use the subdomain as a folder name and configure MAMP to detect the deference.

#### Configuring your own `127.0.0.1` DNS

Say you have a domain you own, mysite.com.  We need to set up a wildcard DNS entry so that any request to certain URLs will return `127.0.0.1`.  It should look something like this:

![local setup](http://i.imgur.com/bRi1fyx.png)

Or if you really know what you're doing:

    *.local 10800 IN A 127.0.0.1

Once that propagates, we should be able to ping any domain at `*.local.mysite.com` can get back `127.0.0.1`

### Configure MAMP

Next we need to configure a VirtualHost on MAMP to dynamically server a folder based on the name in the URL.

To do this, copy and paste this to the end of your `/Applications/MAMP/conf/apache/httpd.conf` file (and replace `yourname` and `vcap.me` with your values):

    NameVirtualHost *
    <VirtualHost *>
        DocumentRoot "/Applications/MAMP/htdocs"
        ServerName localhost
    </VirtualHost>
    <VirtualHost *>
        ServerName vcap.me
        UseCanonicalName Off
        ServerAlias *.vcap.me
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

Boom!  Visit `foo.vcap.me` and MAMP will serve the document root from `~/public_html/foo`.
