---
title: 'Using XSLT to view graphical poll results'
date: Thu, 21 Oct 2004 04:18:47 +0000
draft: false
tags: ['Uncategorized']
---

How I came to this is not relevant I think, so I will jump straight to the examples. The idea was to convert [this XML document](/sources/poll-data-raw.xml) to produce [this output](/sources/poll-results.xml). So I set out to do it -- and it was finally done using [this XSLT stylesheet](/sources/poll-transform.xsl). The idea is quite basic. Count the number of 'I agree' responses and store it in a variable. Count the number of 'I disagree' responses and store it into another variable, and so on... Once that's done, the count values are multiplied with the expansion factor (_variable: efactor_) and this number is set to the height of a div element. That's all. Let's all resolute to producing a standardized web!