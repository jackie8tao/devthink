---
title: Hugo搭建个人静态博客
author: Jackie Tao
date: 2023-05-07T12:31:10+08:00
draft: true
description: 解释hugo基本概念和自动化博客网站。
keywords: hugo 博客
comment: false
tags:
  - hugo
categories:
  - posts
---

技术的发展日新月异，从业人员每天都需要大量学习各种繁杂的技术知识。在这种情况下，
一个好的知识管理工具就显得格外重要了。

[Hugo](https://gohugo.io/)是一款快速、灵活的静态网站生成器，它使用Go语言编写，
可用于创建各种类型的静态网站，如博客、企业网站、个人网站等。
Hugo的主要特点是速度快、易于使用和高度可定制。
它支持Markdown和HTML格式的内容，可以使用主题来快速创建漂亮的网站，并支持多种语言。
Hugo还提供了丰富的文档和社区支持，可以帮助用户更好地使用和定制该工具。

## 基础概念

Hugo主要核心概念为*模板*和*内容*，模板用于确认网站的样式，内容则用于填充模板，
两者一起合作形成整个网站，我们在使用Hugo的过程中，主要关注的也是这两部分。
个人认为模板是其真正的核心，这里也主要介绍该部分。
Hugo的模板设计思路依据的是其对网站的结构划分，也就是结构化的拆分。

![site-hierarchy](/img/site-hierarchy.svg)

可以明显看到Homepage、SectionPage、ContentPage和Taxonomy是其核心。
