---
layout: posts
title: 'Feedback request: usage of "EmbulkEmbed" and "EmbulkService" / "BINARY" type'
date: 2018-10-11 11:56:42 +0900
author: "dmikurube"
---

(Copied from [an old post in the embulk-announce mailing list](https://groups.google.com/d/msg/embulk-announce/oqS_GsmP5IM/8Em-3NMTAgAJ).)

Hi Embulk users,

Sorry for the long silent interval. Embulk `v0.9.8` is out yesterday with some fixes. We are going to start updating the Embulk core again.

Then, for future updates, we'd like to have your feedbacks on two topics about your Embulk usage.

### 1. Usage of "`EmbulkEmbed`" and "`EmbulkService`"

Embulk has a way to be embedded in your Java program by classes of "`EmbulkEmbed`" and "`EmbulkService`". We expect most users are using Embulk from CLI, but some users may be using these "`EmbulkEmbed`" or "`EmbulkService`".

We may have a big change on them for future updates. "`EmbulkService`" has been deprecated for a long time. "`EmbulkEmbed`" may be incompatibly changed (or replaced) as well.

Note that CLI won't change for this.

If you use "`EmbulkEmbed`" or "`EmbulkService`" in your environment, please let us know in this GitHub Issue. Otherwise, their compatibility can be broken suddenly!

[https://github.com/embulk/embulk/issues/1047](https://github.com/embulk/embulk/issues/1047)

### 2. "`BINARY`" type

There has been a discussion whether we need a new "`BINARY`" type, or not. At first we thought to introduce the `BINARY` type, but very few people wanted it when we asked in a meetup with 100+ people.

If you really want the `BINARY` type, please elaborate your use-cases in the GitHub Issue.

[https://github.com/embulk/embulk/issues/1046](https://github.com/embulk/embulk/issues/1046)

Thanks.
