---
layout: posts
title: 'Re: Feedback request: usage of "EmbulkEmbed" and "EmbulkService" / "BINARY" type'
date: 2018-12-28 10:01:04 +0900
author: "dmikurube"
---

(Copied from [an old post in the embulk-announce mailing list](https://groups.google.com/d/msg/embulk-announce/oqS_GsmP5IM/rTNckn0TBAAJ).)

Hi Embulk users,

As mentioned in the previous e-mail, we are going to change the usage of `EmbulkEmbed` and `EmbulkService`. `EmbulkEmbed` users, please check the following list, stay tuned on next announcements, and take care when you are upgrading `embulk-core` versions. (All the following changes will not affect Embulk CLI usage. Then, I believe most users are not affected.)

1) Remove `EmbulkServce`
=========================

`org.embulk.EmbulkService` will be removed soon. Users need to switch to `org.embulk.EmbulkEmbed`.

2) Drop support for JSR-250 LifeCycle annotations (`@PostConstruct` and `@PreDestroy`)
=======================================================================================

As mentioned in [https://github.com/embulk/embulk/issues/1047](https://github.com/embulk/embulk/issues/1047), we are going to drop support for `@PostConstruct` and `@PreDestroy`. If you're using them, please leave your comments in [https://github.com/embulk/embulk/issues/1047](https://github.com/embulk/embulk/issues/1047). `EmbulkEmbed#destroy()` will be unsupported as well.

Embulk `v0.9.12` shows warning messages for use of `@PostConstruct` and `@PreDestroy` before actual drop. If you're concerned, please try Embulk `v0.9.12`.

3) Change the usage of SLF4J `Logger` and `LoggerFactory`
==========================================================

Logback (SLF4J backend) has been included in `embulk-core`'s dependencies, Logback has been configured in the beginning of `EmbulkEmbed`. Those will be changed.

`EmbulkEmbed` users will have to include and configure some SLF4J backend library by themselves.

4) Change the way to load dependency libraries from `embulk-core`
==================================================================

Dependency libraries of `embulk-core` are currently to be located in `CLASSPATH`, and to be loaded simultaneously with `embulk-core`. But this way exposes these libraries to plugins, and it has caused complicated dependency problems with plugins.

We are going to change the way to load dependency libraries. `EmbulkEmbed` users will have to call Embulk's `static` method to specify paths to those dependency libraries before initializing `EmbulkEmbed`.
