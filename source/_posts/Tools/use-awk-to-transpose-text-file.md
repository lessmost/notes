title: Use Awk to transpose text file
date: 2013-06-28 22:42:59
category: Tools
tags:
---
<html xmlns="http://www.w3.org/1999/xhtml" lang="en" xml:lang="en">
<head>
<title>使用awk将文本内容行列互换</title>
<!-- 2013-06-28 五 22:36 -->
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
<h1 class="title">使用awk将文本内容行列互换</h1>
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. 前话</a></li>
<li><a href="#sec-2">2. 正文</a>
<ul>
<li><a href="#sec-2-1">2.1. 脚本conv.awk</a></li>
</ul>
</li>
</ul>
</div>
</div>

<!--more-->

<div id="outline-container-sec-1" class="outline-2">
<h2 id="sec-1"><span class="section-number-2">1</span> 前话</h2>
<div class="outline-text-2" id="text-1">
<p>
这段时间在做一些性能测试实验，经常使用bash，sed，awk 对实验结果做一
些自动化的分析。这样就顺便学了一把bash awk什么的。空间的时候就想了一
个使用awk对文本内容进行行列互换的脚本。
</p>
</div>
</div>
<div id="outline-container-sec-2" class="outline-2">
<h2 id="sec-2"><span class="section-number-2">2</span> 正文</h2>
<div class="outline-text-2" id="text-2">
<p>
比如现在有文件test.txt内容为：
</p>
<pre class="example">
11    12      13      14
21    22      23      24
31    32      33      34
</pre>
<p>
使用awk脚本转换后的内容则为：
</p>
<pre class="example">
11    21      31
12    22      32
13    23      33
14    24      34
</pre>
</div>

<div id="outline-container-sec-2-1" class="outline-3">
<h3 id="sec-2-1"><span class="section-number-3">2.1</span> 脚本conv.awk</h3>
<div class="outline-text-3" id="text-2-1">
<div class="org-src-container">

<pre class="src src-awk" id="conv.awk"><span style="color: #cd0000;">#</span><span style="color: #cd0000;">!/usr/bin/awk -f</span>

<span style="color: #00cdcd; font-weight: bold;">BEGIN</span> {
  lines=0
  cols=0
}

{
  <span style="color: #00cdcd; font-weight: bold;">if</span> (<span style="color: #cdcd00;">NF</span> &gt; cols)
    cols=<span style="color: #cdcd00;">NF</span>
  lines++
  <span style="color: #00cdcd; font-weight: bold;">for</span> (i=1; i&lt;=<span style="color: #cdcd00;">NF</span>; i++)
    data[<span style="color: #cdcd00;">NR</span>, i]=$i
}

<span style="color: #00cdcd; font-weight: bold;">END</span> {
  <span style="color: #00cdcd; font-weight: bold;">for</span> (i=1; i&lt;=cols; i++) {
    <span style="color: #00cdcd; font-weight: bold;">for</span> (j=1; j&lt;=lines; j++)
      <span style="color: #0000ee; font-weight: bold;">printf</span> data[j,1]<span style="color: #00cd00;">"\t"</span>
    <span style="color: #0000ee; font-weight: bold;">printf</span> <span style="color: #00cd00;">"\n"</span>
  }
}
</pre>
</div>
</div>
</div>
</div>
</div>
<div id="postamble" class="status">
<p class="date">Date: 2011-10-03</p>
<p class="date">Created: 2013-06-28 五 22:36</p>
<p class="creator"><a href="http://www.gnu.org/software/emacs/">Emacs</a> 24.3.1 (<a href="http://orgmode.org">Org</a> mode 8.0.2)</p>
<p class="xhtml-validation"><a href="http://validator.w3.org/check?uri=referer">Validate XHTML 1.0</a></p>
</div>
</body>
</html>
