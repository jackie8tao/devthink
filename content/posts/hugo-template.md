---
title: Hugo模板分析
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

![网站结构](/img/site-hierarchy.svg)

可以明显看到Homepage、SectionPage、ContentPage和TaxonomyPage是其核心。
简单点说就是，首页由HomepageTemplate模板渲染，SectionPage和TaxonomyPage由ListTemplage渲染，
ContentPage则由SinglePageTemplate渲染。需要注意的是，单纯看官方文档上列出的各种模板的话，
会发现有很多模板，很容易把自己绕进去，搞不清楚各个模板的作用。

![hugo模板](/img/hugo-template.png)

## 各个模板用法

Hugo文档上列出的模板和作用说明。

- Base Template，一般用于确定整个网站结构的基础，各个页面都会使用该模板。
- List Template，具有列表性质的内容的渲染基础模板，如Section、Taxonomy。
- Homepage Template，网站首页，很特殊，单独拿出来。
- Section Template，站点内容各个层级的列表页，没有提供则用List Template渲染。
- Taxonomy Template，分类、术语等的渲染模板，如果没有提供，则使用List Template进行渲染。
- Single Page Template，具体单个内容页面的渲染模板。
- Content View Template
- Data Template
- Partial Template
- Shortcode Template
- Menu Template，菜单模板，一般通过配置设置，然后在页面渲染。
- RSS Template
- Sitemap Template

Hugo文档中列出了多样的模板，深入观察，不难发现，主要就是首页、列表页和内容页。

## 自定义主题

这里我们从零开始搭建一个主题，争取把上面的每个部分都用到，加深对Hugo模板使用的理解。
新建一个demo网站，内容结构如下图所示，有posts和news两个类别，每个类别下面有3篇文章。

![示例结构](/img/hugo/demo_content_directory.png)

- 设置网站主页
  
新建layouts/_default/index.html文件，添加如下内容。

```html
<!DOCTYPE html>
  <html>
    <head>
      <title>{{ .Title }}</title>
    </head>
    <body>
      <main>
        {{ .Content }}
      </main>
    </body>
</html>
```

最终现实效果为：

![首页效果1](/img/hugo/demo_home_view1.png)

![首页效果2](/img/hugo/demo_home_view2.png)

- 设置内容目录页

新建layouts/_default/section.html文件，添加如下内容。

```html
<!DOCTYPE html>
  <html>
    <head>
      <title>{{ .Title }}</title>
    </head>
    <body>
      <main>
        {{ .Content }}
        <ul>
          {{ range .Paginator.Pages }}
            <li>{{ .Title }}</li>
          {{ end }}
        </ul>
      </main>
    </body>
</html>
```

最终实现效果为：

![目录页1](/img/hugo/demo_section_view1.png)

![目录页2](/img/hugo/demo_section_view2.png)

- 设置内容页

新建layouts/_default/single.html，添加如下内容。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>{{ .Title }}</title>
    </head>
    <body>
        <main>
            {{ .Content }}
        </main>
    </body>
</html>
```

最终实现效果为：

![内容页1](/img/hugo/demo_page_view1.png)

![内容页2](/img/hugo/demo_page_view2.png)

实现上面三个部分，整个网站基本都已经成型了，对于其他模板其中需要注意的就是分类（Taxonomy）模板，
对于分类的列表，Hugo使用列表模板进行渲染。

![文档说明](/img/hugo/demo_list_template.png)

最终整个demo网站的目录结构如下。

![demo网站](/img/hugo/demo_site_result.png)

## 总结

Hugo模板看官方的文档会觉得很复杂，但是我们只要抓住核心的Homepage、Section和List就可以了。
