---
layout: post
title: "Visitor Analytics"
description: ""
category: magento
tags: [magento, performance, mysql]
---
{% include JB/setup %}

On occation, when a new client signs, I need to get an idea of how much traffic their site gets and what sort of hosting setup would be required to host their site.

A quick and easy way is to query the log tables for visitor hits.  Here are a few MySQL queries I've used

### Unique and Total Visits

This will show total visits per month

    select
        s.name `Store View`,
        date_format(u.visit_time, "%Y-%m") Period,
        count(distinct vi.remote_addr) `Unique Visits`,
        sum(vi.http_user_agent regexp 'bot|spider|w3c|validat|crawl|monitor|php') `Visits from Bots`,
        count(*) `Total Visits` -- Warning: slow
    from log_url u
    inner join log_visitor_info vi
        on vi.visitor_id = u.visitor_id
    inner join log_url_info i
        on i.url_id = u.url_id
    inner join log_visitor v
        on v.visitor_id = vi.visitor_id
    inner join core_store s
        on s.store_id = v.store_id
    where u.visit_time > now() - interval 6 month
    group by Period, v.store_id
    order by `Store View`, Period;

### Most Viewed Pages

    select
        s.name `Store View`,
        date_format(u.visit_time, "%Y-%m-%d") Period,
        count(distinct vi.remote_addr) `Unique Visits`,
        count(*) Visits,
        replace(url, concat(substring_index(url, "/", 3), "/"), "") Page
    from log_url u
    inner join log_visitor_info vi
        on vi.visitor_id = u.visitor_id
    inner join log_url_info i
        on i.url_id = u.url_id
    inner join log_visitor v
        on v.visitor_id = vi.visitor_id
    inner join core_store s
        on s.store_id = v.store_id
    where u.visit_time > now() - interval 1 month
    group by Period, v.store_id, Page
    order by Period DESC, `Unique Visits` DESC, `Store View`;