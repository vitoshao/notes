---
title: Between
layout: default
parent: Query
nav_order: 1
description: "Between"
date: 2013-05-02
tags: [SQL,Query]
postid: "5963065886947212118"
---
It is important to remember that the BETWEEN clause is inclusive of the outer values specified in the range.   For example, BETWEEN 1 AND 5 includes 1, 1.001, 1.11, and so on through 4.9, 4.99, and 5.0.   This can sometimes be confusing when working with alphanumeric fields.   If you are querying a book title column and you search BETWEEN 'S' AND 'Z', all book titles starting with S, even if only the single letter S, are returned,   but at the other end of the range, a book with a title of simply Z is returned, but Zebras 101 is not returned.