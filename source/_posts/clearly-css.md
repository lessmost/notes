title: 悦读插件是怎么让网页变得简明和易如阅读的？
date: 2015-12-29 23:32:22
tags: css
category: Frontend

---

Evernote 的 [Clearly](https://evernote.com/clearly/) 插件能够让网页简洁和易于阅读，它将网页的主要内容在新的 iframe 中显示，并使用新的不同的主题样式。

最近看了下 Clearly 的默认 newsprint 主题下 CSS 样式，可以总结为以下几点。

## 主体内容

Clearly 将网页主题居中显示，设置背景颜色和默认字体样式

```css
background-color: #F3F2EE;
font-family: "PT Serif";
font-size: 16px;
line-height: 24px;
color: #1F0909;
text-align: left;
vertical-align: baseline;
```

## 代码样式

```css
font-family: Inconsolata;
font-size: 14px;
line-height: 24px;
```

## Oracle Java Docs 网站的 Stylish

将上面的这些样式写成 [Stylish](https://userstyles.org/) 插件的一个样式，给 [Oracle Java Docs](https://docs.oracle.com/javase/tutorial/collections/interfaces/list.html) 网站使用，就可以获得与 Clearly 插件类似的阅读效果了:)

```css
#TopBar {
    display: none;
}

#LeftBar {
    display: none;
}

.header-container {
    display: none;
}

body {
    background-color: #F3F2EE;
    font-family: "PT Serif";
    font-size: 16px;
    line-height: 24px;
    color: #1F0909;
    text-align: left;
    vertical-align: baseline;
}

pre, code {
    font-family: Inconsolata;
    font-size: 14px;
    line-height: 24px;
}

#MainFlow {
    width: 750px;
    margin: auto;
}
```

