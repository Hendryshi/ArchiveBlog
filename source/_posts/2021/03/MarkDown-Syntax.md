---
title: MarkDown Syntax
toc: true
thumbnail: /img/favicon.svg
categories:
  - HEXO
tags:
  - MarkDown
date: 2021-03-21 23:40:39
update: 2021-03-21 23:40:39
---


Records the mark down syntax

{% raw %}<div class="notification is-info">{% endraw %}
There is a [**online tool**](http://www.atoolbox.net/Tool.php?Id=715) to transfer HTML code to Mark Down syntax
{% raw %}</div>{% endraw %}


<!-- more -->

## Typography

### Add a picture

![](PictureEx.png)

```
![](YouPictureName.png)
```
### HyperLink

[This is a link](https://www.google.fr/)

```
[Link Name](link url)
```

### Show code difference

```diff diff:layout/layout.jsx
<Head site={site} config={config} helper={helper} page={page} />
-   <body class={`is-${columnCount}-column`}>
+   <body class={`is-3-column`}>
<Navbar config={config} helper={helper} page={page} />
```

```
Use keyword "diff" after the code block, and use + or - to show difference

```diff diff:layout/layout.jsx
<Head site={site} config={config} helper={helper} page={page} />
-   <body class={`is-${columnCount}-column`}>
+   <body class={`is-3-column`}>
<Navbar config={config} helper={helper} page={page} />
```


### Reference

>Use the keyword ">" before your paragraph

### Highlight text by color

{% raw %}<div class="notification is-info">{% endraw %}
This is a notification info
{% raw %}</div>{% endraw %}

```
{% raw %}<div class="notification is-info">{% endraw %}
This is a notification info
{% raw %}</div>{% endraw %}
```

{% raw %}<div class="notification is-success">{% endraw %}
This is a notification success
{% raw %}</div>{% endraw %}

```
{% raw %}<div class="notification is-success">{% endraw %}
This is a notification success
{% raw %}</div>{% endraw %}
```

{% raw %}<div class="notification is-warning">{% endraw %}
This is a notification warning
{% raw %}</div>{% endraw %}

```
{% raw %}<div class="notification is-warning">{% endraw %}
This is a notification warning
{% raw %}</div>{% endraw %}
```

{% raw %}<div class="notification is-danger">{% endraw %}
This is a notification danger
{% raw %}</div>{% endraw %}

```
{% raw %}<div class="notification is-danger">{% endraw %}
This is a notification danger
{% raw %}</div>{% endraw %}
```

{% raw %}<article class="message is-info"><div class="message-body">{% endraw %}
This is a message information
{% raw %}</div></article>{% endraw %}

```
{% raw %}<article class="message is-info"><div class="message-body">{% endraw %}
This is a message information
{% raw %}</div></article>{% endraw %}
```

{% raw %}<article class="message is-success"><div class="message-body">{% endraw %}
This is a message success
{% raw %}</div></article>{% endraw %}

```
{% raw %}<article class="message is-success"><div class="message-body">{% endraw %}
This is a message success
{% raw %}</div></article>{% endraw %}
```

{% raw %}<article class="message is-warning"><div class="message-body">{% endraw %}
This is a message warning
{% raw %}</div></article>{% endraw %}

```
{% raw %}<article class="message is-warning"><div class="message-body">{% endraw %}
This is a message warning
{% raw %}</div></article>{% endraw %}
```

{% raw %}<article class="message is-danger"><div class="message-body">{% endraw %}
This is a message danger
{% raw %}</div></article>{% endraw %}

```
{% raw %}<article class="message is-danger"><div class="message-body">{% endraw %}
This is a message danger
{% raw %}</div></article>{% endraw %}
```

{% raw %}<article class="message is-info"><div class="message-header">{% endraw %}
Your Title
{% raw %}</div><div class="message-body">{% endraw %}
Your message
{% raw %}</div></article>{% endraw %}

```
{% raw %}<article class="message is-info"><div class="message-header">{% endraw %}
Your Title
{% raw %}</div><div class="message-body">{% endraw %}
Your message
{% raw %}</div></article>{% endraw %}
```

See more bulma style in this [**link**](https://www.imaegoo.com/2020/icarus-with-bulma/)