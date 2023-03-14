---
layout: posts
title: "Embulk maintenance goes open"
date: 2023-03-10
description: Embulk has been an open-source project since its beginning. The license of its core is Apache-2.0, then it is definitely &quot;open source&quot;. But, the maintenance and the design decisions had been made exclusively by Treasure Data so far practically. We decided to change Embulk's maintenance to be open. This article explains the new maintenance methods. Some of things are yet under preparation, though.
author: "dmikurube"
---

Embulk has been an open-source project since its beginning. The license of its core is [Apache-2.0](https://www.apache.org/licenses/LICENSE-2.0), then it is definitely "[open source](https://opensource.org/osd)". But, the maintenance and the design decisions had been made exclusively by Treasure Data so far practically. [^pull-requests]

[^pull-requests]: We accepted open Pull Requests via GitHub, though.

We decided to change Embulk's maintenance to be open. This article explains the new maintenance methods. Some of things are yet under preparation, though.

What is open, in particular?
=============================

The two below is the major updates.

1. Establish a decision-making process, especially for big changes that impact plugin compatibilities
2. Involve contributors out of Treasure Data in Embulk's decision making

EEP (Embulk Enhancement Proposal)
----------------------------------

We established a decision-making process for big changes at first. More precisely speaking, we defined processes for writing and reviewing the [Embulk Enhancement Proposal (EEP)](https://github.com/embulk/embulk/blob/master/docs/eeps/eep-0001.md) documents.

Such documents, reviews, and processes may seem like rigidly formal for some of you. However, we have to take special notice of the fact that Embulk does not stand alone. Embulk is an "eco-system" that needs plugins to do any practical work. Plugins have been maintained not only by Treasure Data. Embulk has been supported also by plugins from many open-source developers. Therefore, we need measured decisions about plugin compatibilities. The main purpose of EEP is to secure the bottomline for such decision making.

We tried to relax the process even as of now, but we still wanted to relax furthermore as far as possible.

By the way, some of you might have heard a similar name. EEP is inspired by Python's [PEP](https://peps.python.org/pep-0001/) at large. EEP started from borrowing the major parts from PEP, and simplified it. [^pep-cc0]

[^pep-cc0]: PEP says "placed in the public domain or under [the CC0-1.0-Universal license](https://creativecommons.org/publicdomain/zero/1.0/deed.en), whichever is more permissive", then we started it based on PEP. EEP is also under CC0.

On the other hand, Embulk has recently been performing the "v0.10" major revisions with some incompatibilities. We plan to publish retrospective EEPs about the design decisions of those revisions.

See also: ["For Embulk users: What will change in v0.11 and v1.0?"](https://www.embulk.org/articles/2021/04/27/changes-in-v0.11.html)

Embulk core team
-----------------

The EEP-1 above defines the "Embulk core team". This core team would be the decision maker, which consist of 3 types of members below.

1. core committers (who has permission to commit directly in the core)
2. key plugin developers
3. key user representatives

The core team includes non-core committers who have permissions to commit directly in the core because plugin compatibilities are sensitive as written above. The key point is getting checked by non-core committers like: "Is this change really safe to introduce?"

The core team also aims to get them promoted to be core committers from #2 and #3 above. We at first thought to invite them as core committers from the beginning, but we heard some voices that it would be so hard to be committers immediately. It is true because inside the Embulk core is messy now, then we made the steps.

The aurhor ([@dmikurube](https://github.com/dmikurube)) is expected to make final decisions practically yet for a while as the author has maintained Embulk alone so far recent years.

However, we wanted to have more committers, and to make decisions by the entire core team.

CLA (Contributor License Agreement)
------------------------------------

We consider the necessity of so-called [Contributor License Agreement (CLA)](https://en.wikipedia.org/wiki/Contributor_License_Agreement), but not yet. It may be needed in the future although we don't want to make it too much formal.

Who are the first core team members?
=====================================

Listed in the order of their responses to join the core team.

* [@dmikurube](https://github.com/dmikurube)
  * The author of this article from Treasure Data
  * As the maintainer for these recent years
* [@hiroyuki-sato](https://github.com/hiroyuki-sato)
  * As the maintainer of embulk-parser-jsonpath and else
  * As the key user representative who has actively listened to users' voice
* [@civitaspo](https://github.com/civitaspo)
  * As the maintainer of embulk-input/output-hdfs, embulk-filter-copy, embulk-output-sftp, and else
* [@hito4t](https://github.com/hito4t)
  * As the maintainer of JDBC-related plugins
* [@joker1007](https://github.com/joker1007)
  * As the maintainer of embulk-input/output-cassandra, embulk-input/output-kafka, embulk-parser-avro, and else
* [@giwa](https://github.com/giwa)
  * As the maintainer of embulk-output-bigquery(_java), and else
  * From [trocco](https://trocco.io/) that provides SaaS using Embulk, and maintains many plugins
* [@kekekenta](https://github.com/kekekenta)
  * From [trocco](https://trocco.io/) that provides SaaS using Embulk, and maintains many plugins
* [@d-hrs](https://github.com/d-hrs)
  * From [trocco](https://trocco.io/) that provides SaaS using Embulk, and maintains many plugins

Why to get open?
=================

By maintaining the Embulk core exclusively by Treasure Data, we sometimes had biased viewpoints about compatibility checks and reviews, and suspended development for a while for some in-house reasons. We sometimes broke the compatibility unintentionally. We had a half year interval between Embulk v0.10.35 to v0.10.36, and 4 months between Embulk v0.10.36 and v0.10.37.

We concluded that it is not healthy as an open-source project, especially like Embulk which needs "the eco-system of plugins by other developers", after discussing further in Treasure Data.

Then, we called for folks who contributed in the Embulk eco-system through plugins and else to join "inside" the project officially, so that wen can keep Embulk maintained more healthy.

Stay tuned for further Embulk improvements!
