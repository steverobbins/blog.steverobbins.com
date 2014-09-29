---
layout: post
title: "Customizing Magento's Error and Maintenance Page"
description: ""
category: magento
tags: [magento, theme]
---
{% include JB/setup %}

This article will show you the correct way (no core mods) to implement your own error/maintenance page in Magento.

# The `errors` directory

Take a look inside the `errors` directory in the root of your Magento installation.  You should see the following files and folders:

    .htaccess
    404.php
    503.php
    default/
    design.xml
    local.xml.sample
    processor.php
    report.php

These are the only files loaded/used when Magento encounters an error or is in Maintenance Mode.  You'll notice things look a little different from a normal adminhtml/frontend layout and template structure.

We do see a few familiar faces, however.  Specifically `default/` and `local.xml.sample`.  We know that there's a `default/default` package/theme for the frontend, and we see `local.xml` in a few places like database configurations in `app/etc/local.xml` or as a layout update `.xml`

# The `local.xml`

If we crack open the sample XML file we see some hints that Magento Core Team left for us.

    <?xml version="1.0"?>
    <config>
        <skin>default</skin>
        <report>
            <!--
                "action" can be set to "print" to show exception on screen and "email"
                to send exception on specified email
            -->
            <action>print</action>
            <!--
                in "subject" you can set subject of email
            -->
            <subject>Store Debug Information</subject>
            <!--
                "email_address" admin email address
            -->
            <email_address></email_address>
            <!--
                "trash" is handle about trace info
                value "leave" is for store on disk
                value "delete" is for cleaning
            -->
            <trash>leave</trash>
        </report>
    </config>

It looks like we have the ability to change the skin that's used in the `\config\skin` XPATH.  We can also change what customer sees when they reach an error.

We have three options:

* `print` - This will display the error message and stack trace to the customer.  While useful in development, probably not a good idea for production
* `email` - This displays a simple contact form to the customer where they can add a comment as to what lead up to this error.  This report gets sent to the configured `\config\report\email_address` so an admin can review
* Leave it blank - If neither of these actions are set, the customer will only see a message that an error has been encountered.

To implement our configurations, all that's needed is to copy `local.xml.sample` to `local.xml` and tailor to our liking.

# Creating your own theme

So now we know that we can specify our own theme in `\config\skin`.  Let's create a new skin based off `default` to customize.

* Copy `errors/default` to `errors/custom`
* Change the value in `\config\skin` from `default` to `custom` in `errors/local.xml`

```
<?xml version="1.0"?>
<config>
    <skin>custom</skin>
</config>
```

Now we have a separate skin that we can edit and theme, and we didn't have to modify any core files.

Each skin has four `.phtml` files and one `styles.css`.  That `page.phtml` is always used.  It opens and closes the `<html>` tags.

One of `404.phtml`, `503.phtml`, or `report.phtml` will be rendered inside of `page.phtml` depending on what error has occured.

# Need more?

Check out [an example on GitHub](https://github.com/steverobbins/Magento-Errorpage.git)

