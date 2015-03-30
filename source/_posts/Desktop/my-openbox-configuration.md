title: My Openbox Configuration
date: 2013-06-28 22:47:41
category: Desktop
tags:
---
<html xmlns="http://www.w3.org/1999/xhtml" lang="en" xml:lang="en">
<head>
<title>Openbox配置</title>
<!-- 2013-06-28 五 22:37 -->
<meta http-equiv="Content-Type" content="text/html;charset=utf-8"/>
<meta name="generator" content="Org-mode"/>
<style type="text/css">
 <!--/*--><![CDATA[/*><!--*/
  .title  { text-align: center; }
  .todo   { font-family: monospace; color: red; }
  .done   { color: green; }
  .tag    { background-color: #eee; font-family: monospace;
            padding: 2px; font-size: 80%; font-weight: normal; }
  .timestamp { color: #bebebe; }
  .timestamp-kwd { color: #5f9ea0; }
  .right  { margin-left: auto; margin-right: 0px;  text-align: right; }
  .left   { margin-left: 0px;  margin-right: auto; text-align: left; }
  .center { margin-left: auto; margin-right: auto; text-align: center; }
  .underline { text-decoration: underline; }
  #postamble p, #preamble p { font-size: 90%; margin: .2em; }
  p.verse { margin-left: 3%; }
  pre {
    border: 1px solid #ccc;
    box-shadow: 3px 3px 3px #eee;
    padding: 8pt;
    font-family: monospace;
    overflow: auto;
    margin: 1.2em;
  }
  pre.src {
    position: relative;
    overflow: visible;
    padding-top: 1.2em;
  }
  pre.src:before {
    display: none;
    position: absolute;
    background-color: white;
    top: -10px;
    right: 10px;
    padding: 3px;
    border: 1px solid black;
  }
  pre.src:hover:before { display: inline;}
  pre.src-sh:before    { content: 'sh'; }
  pre.src-bash:before  { content: 'sh'; }
  pre.src-emacs-lisp:before { content: 'Emacs Lisp'; }
  pre.src-R:before     { content: 'R'; }
  pre.src-perl:before  { content: 'Perl'; }
  pre.src-java:before  { content: 'Java'; }
  pre.src-sql:before   { content: 'SQL'; }

  table { border-collapse:collapse; }
  td, th { vertical-align:top;  }
  th.right  { text-align: center;  }
  th.left   { text-align: center;   }
  th.center { text-align: center; }
  td.right  { text-align: right;  }
  td.left   { text-align: left;   }
  td.center { text-align: center; }
  dt { font-weight: bold; }
  .footpara:nth-child(2) { display: inline; }
  .footpara { display: block; }
  .footdef  { margin-bottom: 1em; }
  .figure { padding: 1em; }
  .figure p { text-align: center; }
  .inlinetask {
    padding: 10px;
    border: 2px solid gray;
    margin: 10px;
    background: #ffffcc;
  }
  #org-div-home-and-up
   { text-align: right; font-size: 70%; white-space: nowrap; }
  textarea { overflow-x: auto; }
  .linenr { font-size: smaller }
  .code-highlighted { background-color: #ffff00; }
  .org-info-js_info-navigation { border-style: none; }
  #org-info-js_console-label
    { font-size: 10px; font-weight: bold; white-space: nowrap; }
  .org-info-js_search-highlight
    { background-color: #ffff00; color: #000000; font-weight: bold; }
  /*]]>*/-->
</style>
<script type="text/javascript">
/*
@licstart  The following is the entire license notice for the
JavaScript code in this tag.

Copyright (C) 2012  Free Software Foundation, Inc.

The JavaScript code in this tag is free software: you can
redistribute it and/or modify it under the terms of the GNU
General Public License (GNU GPL) as published by the Free Software
Foundation, either version 3 of the License, or (at your option)
any later version.  The code is distributed WITHOUT ANY WARRANTY;
without even the implied warranty of MERCHANTABILITY or FITNESS
FOR A PARTICULAR PURPOSE.  See the GNU GPL for more details.

As additional permission under GNU GPL version 3 section 7, you
may distribute non-source (e.g., minimized or compacted) forms of
that code without the copy of the GNU GPL normally required by
section 4, provided you include this license notice and a URL
through which recipients can access the Corresponding Source.


@licend  The above is the entire license notice
for the JavaScript code in this tag.
*/
<!--/*--><![CDATA[/*><!--*/
 function CodeHighlightOn(elem, id)
 {
   var target = document.getElementById(id);
   if(null != target) {
     elem.cacheClassElem = elem.className;
     elem.cacheClassTarget = target.className;
     target.className = "code-highlighted";
     elem.className   = "code-highlighted";
   }
 }
 function CodeHighlightOff(elem, id)
 {
   var target = document.getElementById(id);
   if(elem.cacheClassElem)
     elem.className = elem.cacheClassElem;
   if(elem.cacheClassTarget)
     target.className = elem.cacheClassTarget;
 }
/*]]>*///-->
</script>
</head>
<body>
<div id="content">
<h1 class="title">Openbox配置</h1>
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. 前话</a></li>
<li><a href="#sec-2">2. 窗口的移动</a></li>
<li><a href="#sec-3">3. 窗口的拉伸</a></li>
<li><a href="#sec-4">4. 窗口的其他操作</a></li>
<li><a href="#sec-5">5. 程序启动快捷键</a></li>
</ul>
</div>
</div>

<!--more-->

<div id="outline-container-sec-1" class="outline-2">
<h2 id="sec-1"><span class="section-number-2">1</span> 前话</h2>
<div class="outline-text-2" id="text-1">
<p>
在折腾了Gnome, KDE, XFCE, LXDE后，现在还是用着LXDE, 其实主要原因是
LXDE使用Openbox作为它的WM，和LXDE的简单。
</p>

<p>
Openbox的高度可配置性比GDM，KDM都好些，对窗口的管理更有力一些，当然
如果你是最大化流，这个就没多大意义了。
</p>
</div>
</div>
<div id="outline-container-sec-2" class="outline-2">
<h2 id="sec-2"><span class="section-number-2">2</span> 窗口的移动</h2>
<div class="outline-text-2" id="text-2">
<p>
首先我喜欢的配置就是对新建窗口的移动，如将窗口移动到屏幕中央，移动到
屏幕左，右，上，下侧：
</p>
<div class="org-src-container">

<pre class="src src-xml" id="openbox-wmove">&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-c</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">MoveToCenter</span><span style="color: #00cd00;">"</span>/&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-w</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">MoveToEdge</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;north&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-s</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">MoveToEdge</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;south&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-a</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">MoveToEdge</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;west&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-d</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">MoveToEdge</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;east&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
</pre>
</div>

<p>
这样就可以使用Windows+c将窗口移动到屏幕中央，
Windows+w将窗口移动到屏幕上侧，
Windows+s将窗口移动到屏幕下侧，
Windows+a将窗口移动到屏幕左侧，
Windows+d将窗口移动到屏幕右侧。
</p>
</div>
</div>
<div id="outline-container-sec-3" class="outline-2">
<h2 id="sec-3"><span class="section-number-2">3</span> 窗口的拉伸</h2>
<div class="outline-text-2" id="text-3">
<p>
除了将窗口移动到屏幕的各个方位外，还可以将窗口拉伸到某一边缘：
</p>
<div class="org-src-container">

<pre class="src src-xml" id="openbox-wgrow">&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-Up</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">GrowToEdge</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;north&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-Down</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">GrowToEdge</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;south&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-Left</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">GrowToEdge</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;west&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-Right</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">GrowToEdge</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;east&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
</pre>
</div>

<p>
这样就可以使用Windows+Left/Right/Up/Down将窗口拉伸到一定方向的边
缘了。
</p>

<p>
当然，上面的这些特定的移动在都不能满足需要的时候就需要自己手动调整窗
口的位置了：
</p>
<div class="org-src-container">

<pre class="src src-xml" id="openbox-wselfmove">&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-m</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Move</span><span style="color: #00cd00;">"</span>/&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
</pre>
</div>

<p>
这样就可以先按Windows+m然后使用方向键或者鼠标来移动窗口的位置了。
</p>
</div>
</div>
<div id="outline-container-sec-4" class="outline-2">
<h2 id="sec-4"><span class="section-number-2">4</span> 窗口的其他操作</h2>
<div class="outline-text-2" id="text-4">
<p>
当然对窗口的一般化的操作都是支持的如最大化，最小化，全屏什么的：
</p>
<div class="org-src-container">

<pre class="src src-xml" id="openbox-wcommon">&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-F4</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Close</span><span style="color: #00cd00;">"</span>/&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-F8</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Resize</span><span style="color: #00cd00;">"</span>/&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-F9</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Iconify</span><span style="color: #00cd00;">"</span>/&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-F10</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">ToggleMaximizeFull</span><span style="color: #00cd00;">"</span>/&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-F11</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">ToggleDecorations</span><span style="color: #00cd00;">"</span>/&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">ToggleMaximizeFull</span><span style="color: #00cd00;">"</span>/&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
</pre>
</div>

<p>
不过一般我很少使用最大化，常常使用的是水平方向或者垂直方向的单向最大
化窗口：
</p>
<div class="org-src-container">

<pre class="src src-xml" id="openbox-wmax">&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-h</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">ToggleMaximize</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;horizontal&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-v</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">ToggleMaximize</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;vertical&lt;/<span style="color: #0000ee; font-weight: bold;">direction</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
</pre>
</div>

<p>
这样就可以使用Windows+h和Windows+v来将窗口水平最大化和垂直最大化。
</p>
</div>
</div>
<div id="outline-container-sec-5" class="outline-2">
<h2 id="sec-5"><span class="section-number-2">5</span> 程序启动快捷键</h2>
<div class="outline-text-2" id="text-5">
<p>
下面是一些常用的的程序启动快捷键，包括启动XTerm, 锁屏，截屏等操作：
</p>
<div class="org-src-container">

<pre class="src src-xml" id="openbox-hotkey">&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-q</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Execute</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">startupnotify</span>&gt;
      &lt;<span style="color: #0000ee; font-weight: bold;">enabled</span>&gt;true&lt;/<span style="color: #0000ee; font-weight: bold;">enabled</span>&gt;
      &lt;<span style="color: #0000ee; font-weight: bold;">name</span>&gt;XTerm&lt;/<span style="color: #0000ee; font-weight: bold;">name</span>&gt;
    &lt;/<span style="color: #0000ee; font-weight: bold;">startupnotify</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">command</span>&gt;xterm&lt;/<span style="color: #0000ee; font-weight: bold;">command</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-t</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Execute</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">startupnotify</span>&gt;
      &lt;<span style="color: #0000ee; font-weight: bold;">enabled</span>&gt;true&lt;/<span style="color: #0000ee; font-weight: bold;">enabled</span>&gt;
      &lt;<span style="color: #0000ee; font-weight: bold;">name</span>&gt;XTerm&lt;/<span style="color: #0000ee; font-weight: bold;">name</span>&gt;
    &lt;/<span style="color: #0000ee; font-weight: bold;">startupnotify</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">command</span>&gt;xterm -e screen&lt;/<span style="color: #0000ee; font-weight: bold;">command</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-l</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Execute</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">startupnotify</span>&gt;
      &lt;<span style="color: #0000ee; font-weight: bold;">enabled</span>&gt;true&lt;/<span style="color: #0000ee; font-weight: bold;">enabled</span>&gt;
      &lt;<span style="color: #0000ee; font-weight: bold;">name</span>&gt;XScreensaver&lt;/<span style="color: #0000ee; font-weight: bold;">name</span>&gt;
    &lt;/<span style="color: #0000ee; font-weight: bold;">startupnotify</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">command</span>&gt;xscreensaver-command -lock&lt;/<span style="color: #0000ee; font-weight: bold;">command</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Print</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Execute</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">command</span>&gt;scrot -e 'mv $f ~/screenshot/'&lt;/<span style="color: #0000ee; font-weight: bold;">command</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
&lt;<span style="color: #0000ee; font-weight: bold;">keybind</span> <span style="color: #cdcd00;">key</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">W-Print</span><span style="color: #00cd00;">"</span>&gt;
  &lt;<span style="color: #0000ee; font-weight: bold;">action</span> <span style="color: #cdcd00;">name</span>=<span style="color: #00cd00;">"</span><span style="color: #00cd00;">Execute</span><span style="color: #00cd00;">"</span>&gt;
    &lt;<span style="color: #0000ee; font-weight: bold;">command</span>&gt;scrot -b -s -e 'mv $f ~/screenshot/'&lt;/<span style="color: #0000ee; font-weight: bold;">command</span>&gt;
  &lt;/<span style="color: #0000ee; font-weight: bold;">action</span>&gt;
&lt;/<span style="color: #0000ee; font-weight: bold;">keybind</span>&gt;
</pre>
</div>
</div>
</div>
</div>
<div id="postamble" class="status">
<p class="date">Date: 2011-11-03</p>
<p class="date">Created: 2013-06-28 五 22:37</p>
<p class="creator"><a href="http://www.gnu.org/software/emacs/">Emacs</a> 24.3.1 (<a href="http://orgmode.org">Org</a> mode 8.0.2)</p>
<p class="xhtml-validation"><a href="http://validator.w3.org/check?uri=referer">Validate XHTML 1.0</a></p>
</div>
</body>
</html>
