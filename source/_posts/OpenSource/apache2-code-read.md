title: Apache Http Sever 代码阅读
date: 2013-06-28 22:49:18
category: OpenSource
tags:
---
<html xmlns="http://www.w3.org/1999/xhtml" lang="en" xml:lang="en">
<head>
<title>Apache HTTP Sever version 2.2 代码阅读</title>
<!-- 2013-06-28 五 22:38 -->
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
<h1 class="title">Apache HTTP Sever version 2.2 代码阅读</h1>
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. Preface</a></li>
<li><a href="#sec-2">2. Hooks</a>
<ul>
<li><a href="#sec-2-1">2.1. Hooks的实现</a></li>
<li><a href="#sec-2-2">2.2. Optional Hook</a></li>
<li><a href="#sec-2-3">2.3. Apache2中的hook</a></li>
</ul>
</li>
<li><a href="#sec-3">3. Filters</a>
<ul>
<li><a href="#sec-3-1">3.1. filter的使用</a></li>
</ul>
</li>
<li><a href="#sec-4">4. 请求的处理</a>
<ul>
<li><a href="#sec-4-1">4.1. create_connection的注册者</a></li>
<li><a href="#sec-4-2">4.2. pre_connection的注册者</a></li>
<li><a href="#sec-4-3">4.3. process_connection的注册者</a></li>
<li><a href="#sec-4-4">4.4. request_rec结构</a>
<ul>
<li><a href="#sec-4-4-1">4.4.1. request_time</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#sec-5">5. Notes</a></li>
</ul>
</div>
</div>

<!--more-->

<div id="outline-container-sec-1" class="outline-2">
<h2 id="sec-1"><span class="section-number-2">1</span> Preface</h2>
<div class="outline-text-2" id="text-1">
<p>
前一段时间，由于实验室的事情需要看一下Apache HTTP Server的源代码，在
看的过程中自己就记了一些笔记，这里整理一下传上来，不过代码没有看
完，之有其中一部分的笔记。
</p>
</div>
</div>
<div id="outline-container-sec-2" class="outline-2">
<h2 id="sec-2"><span class="section-number-2">2</span> Hooks</h2>
<div class="outline-text-2" id="text-2">
<p>
Apache2中一个模块可以输出hook，其他的模块可以使用这个hook，hook的输
出者需要声明和实现hook，提供hook函数，调用这个hook；hook的使用者则使
用hook函数注册自己的函数，这样注册的函数就会在适当的时候被调用。
</p>
</div>

<div id="outline-container-sec-2-1" class="outline-3">
<h3 id="sec-2-1"><span class="section-number-3">2.1</span> Hooks的实现</h3>
<div class="outline-text-3" id="text-2-1">
<p>
每一个hook都由宏 <code>AP_DECLARE_HOOK</code> 声明的，首先看看 <code>AP_DECLARE_HOOK</code>
这个宏是怎么定义的。
</p>
<div class="org-src-container">

<pre class="src src-c" id="AP_DECLARE_HOOK"><span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Declare a hook function</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ret The return type of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> name The hook's name (as a literal)</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args The arguments the hook function takes, in brackets</span>
<span style="color: #00cd00;"> */</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">AP_DECLARE_HOOK</span>(<span style="color: #cdcd00;">ret</span>, <span style="color: #cdcd00;">name</span>, <span style="color: #cdcd00;">args</span>) \
  APR_DECLARE_EXTERNAL_HOOK(ap, AP, ret, name, args)
</pre>
</div>

<p>
宏 <code>APR_DECLARE_EXTERNAL_HOOK</code> 是在apr-utils中定义。下面来看一下
<code>APR_DECLARE_EXTERNAL_HOOK</code> 的实现。
</p>
<div class="org-src-container">

<pre class="src src-c" id="APR_DECLARE_EXTERNAL_HOOK"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">macro to declare the hook correctly </span><span style="color: #cd0000;">*/</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">APR_DECLARE_EXTERNAL_HOOK</span>(<span style="color: #cdcd00;">ns</span>, <span style="color: #cdcd00;">link</span>, <span style="color: #cdcd00;">ret</span>, <span style="color: #cdcd00;">name</span>, <span style="color: #cdcd00;">args</span>) \
  <span style="color: #00cdcd; font-weight: bold;">typedef</span> <span style="color: #00cd00;">ret</span> <span style="color: #00cd00;">ns</span>##_HOOK_##name##_t args; \
  link##_DECLARE(<span style="color: #00cd00;">void</span>) ns##_hook_##name(ns##_HOOK_##name##_t *pf, \
                                       <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> * <span style="color: #00cdcd; font-weight: bold;">const</span> *<span style="color: #cdcd00;">aszPre</span>, \
                                       <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> * <span style="color: #00cdcd; font-weight: bold;">const</span> *<span style="color: #cdcd00;">aszSucc</span>, \
                                       <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">nOrder</span>); \
  link##_DECLARE(ret) ns##_run_##name args; \
  APR_IMPLEMENT_HOOK_GET_PROTO(ns, link, name); \
  <span style="color: #00cdcd; font-weight: bold;">typedef</span> <span style="color: #00cdcd; font-weight: bold;">struct</span> <span style="color: #00cd00;">ns</span>##_LINK_##name##_t \
  { \
  ns##_HOOK_##name_t *pFunc; \
  <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">szName</span>; \
  <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> * <span style="color: #00cdcd; font-weight: bold;">const</span> *<span style="color: #cdcd00;">aszPredecessors</span>; \
  <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> * <span style="color: #00cdcd; font-weight: bold;">const</span> *<span style="color: #cdcd00;">aszSuccessors</span>; \
  <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">nOrder</span>; \
  } ns##_LINK_##name##_t;
</pre>
</div>

<p>
宏首先定义一个函数类型 <code>ns##_HOOK_##name_t</code> （定义的方式比较特别，
和一般使用的函数指针貌似有点差别:(，）; 然后定义了一个函数
<code>ns##_hook_##name</code> ，使用这个函数就可以向这个hook注册一个函数了，注
册的函数的原型就由 <code>ns##_HOOK_##name##_t</code> 定义了。宏同样申明了函数
<code>ns##_run_##name</code> 。
</p>

<p>
下面看一下宏 <code>APR_IMPLEMENT_HOOK_GET_PROTO</code>
</p>
<div class="org-src-container">

<pre class="src src-c" id="APR_IMPLEMENT_HOOK_GET"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">macro to return the prototype of the hook function </span><span style="color: #cd0000;">*/</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">APR_IMPLEMENT_HOOK_GET_PROTO</span>(<span style="color: #cdcd00;">ns</span>, <span style="color: #cdcd00;">link</span>, <span style="color: #cdcd00;">name</span>) \
  link##_DECLARE(<span style="color: #00cd00;">apr_array_header_t</span> *) ns##_hook_get_##name(<span style="color: #00cd00;">void</span>)
</pre>
</div>

<p>
所以宏 <code>APR_IMPLEMENT_EXTERNAL_HOOK</code> 还声明了一个函数 <br/>
   <code>ns##_hook_get_##name</code> 。
</p>

<p>
最后宏 <code>APR_IMPLEMENT_EXTERNAL_HOOK</code> 定义了结构体 <br/>
   <code>ns##_LINK_##name##_t</code> 。
</p>

<p>
上面介绍了hook的声明， <code>AP_DECLARE_HOOK</code> 宏声明一个hook，宏要求三个
参数：hook函数的返回值类型 <code>ret</code> ，hook函数的名称 <code>name</code> 和hook函数
的参数列表 <code>args</code> 。该宏出现在头文件中以供其他函数使用被定义的hook。
以hook <code>header_parser</code> 为例， <code>header_parser</code> 的<a href="#coderef-header_parser"class="coderef" onmouseover="CodeHighlightOn(this, 'coderef-header_parser');" onmouseout="CodeHighlightOff(this, 'coderef-header_parser');">声明</a>在
<code>include/http_config.h</code> 文件中。 <code>AP_DECLARE_HOOK(int,
   header_parser, (request_rec *r))</code> 一共做了几件事呢：
</p>
<p>
#+ <a id="header_parser" name="header_parser"></a>
</p>
<ul class="org-ul">
<li><code>typedef int ap_HOOK_header_parser_t (request_rec *r);</code> <br/>
     定义了一个类型ap_HOOK_header_parser_t，它是一个函数原型，返回值为
int，参数为request_rec *r。
</li>
<li><code>void ap_hook_header_parser(&#x2026;)</code> <br/>
     声明了函数 <code>ap_hook_header_parser</code> ，返回类型为 <code>void</code> ，参数为 <br/>
     <code>ap_HOOK_header_parser_t *pf</code> , <br/>
     <code>const char * const *aszPre</code> , <br/>
     <code>const char * const *aszSucc</code> , <br/>
     <code>int nOrder</code> 。
</li>
<li><code>int ap_run_header_parser(&#x2026;)</code> <br/>
     声明了函数 <code>ap_run_header_parser</code> ，返回类型为 <code>int</code> ，参数为 <br/>
     <code>request_rec *r</code> 。
</li>
<li><code>apr_array_header_t * ap_hook_get_header_parser(void)</code> <br/>
     声明了函数 <code>ap_hook_get_header_parser</code> ，返回类型为 <br/>
     <code>apr_array_header_t *</code> 。
</li>
<li><code>struct ap_LINK_header_parser_t</code> <br/>
     定义了结构体 <code>ap_LINK_header_parser_t</code> ，其成员有 <br/>
     <code>ap_HOOK_header_parser_t *</code> <br/>
     <code>const char *szName</code> <br/>
     <code>const char * const *aszPredecessors</code> <br/>
     <code>const char * const *aszSuccessors</code> <br/>
     <code>int nOrder</code>
</li>
</ul>
<p>
一共是两个数据类型和三个函数原型。
</p>

<p>
下面来看一个hook的实现。同样以 <code>header_parser</code> 这个hook为例来看hook的
实现。
</p>

<p>
<code>header_parser</code> 的实现在 <code>server/config.c</code> 里面：
</p>
<div class="org-src-container">

<pre class="src src-c" id="header_parser_implement">APR_HOOK_STRUCT(
    APR_HOOK_LINK(header_parser)
    APR_HOOK_LINK(pre_config)
    APR_HOOK_LINK(post_config)
    APR_HOOK_LINK(open_logs)
    APR_HOOK_LINK(child_init)
    APR_HOOK_LINK(handler)
    APR_HOOK_LINK(quick_handler)
    APR_HOOK_LINK(optional_fn_retrieve)
    APR_HOOK_LINK(test_config)
                )
</pre>
</div>

<p>
宏 <code>APR_HOOK_STRUCT</code> 和 <code>APR_HOOK_LINK</code> 定义在 <code>apr-utils</code> 中：
</p>
<div class="org-src-container">

<pre class="src src-c" id="APR_HOOK_STRUCT"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">macro to declare the hook structure </span><span style="color: #cd0000;">*/</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">APR_HOOK_STRUCT</span>(<span style="color: #cdcd00;">members</span>) \
  <span style="color: #00cdcd; font-weight: bold;">static</span> <span style="color: #00cdcd; font-weight: bold;">struct</span> { members } <span style="color: #cdcd00;">_hooks</span>;

<span style="color: #cd0000;">/* </span><span style="color: #cd0000;">macro to link the hook structure </span><span style="color: #cd0000;">*/</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">APR_HOOK_LINK</span>(<span style="color: #cdcd00;">name</span>) \
  <span style="color: #00cd00;">apr_array_header_t</span> *<span style="color: #cdcd00;">link_</span>##name;
</pre>
</div>

<p>
可以看到宏 <code>APR_HOOK_STRUCT</code> 定义了一个 <code>static struct</code> ，即
<code>_hooks</code> 是一个 <code>static</code> 结构体，它的成员是一个或者多个
<code>apr_array_header_t *</code> 类型的变量。（注意这个这个结构体是unnamed
struct，并且是static的，这样做的好处可以想想；unamed不是
anonymous&#x2026; ）
</p>

<p>
hook  <code>header_parser</code> 在这里就对应着 <code>server/config.c</code> 里面的
<code>_hooks</code> 结构体里面的 <code>apr_array_header_t *link_header_parser</code> 变量。
</p>

<p>
通过前面的hook的声明部分，可以知道hook <code>header_paser</code> 的<a href="#header_parser">声明</a>声明了
三个函数，这三个函数在哪实现的呢？
</p>

<p>
查看Apache HTTP Server的Development Document，可以知道，httpd里面的
hook有两大类：一类是没有返回值的hook，即hook函数返回类型为void；另
一类是有返回值的hook，一般是int类型的。
</p>

<p>
如果hook函数的返回类型是void的，那么所有注册在这个hook上的函数都会
被执行，这样的hook是这样实现的：
</p>
<div class="org-src-container">

<pre class="src src-c" id="AP_IMPLEMENT_HOOK_VOID"><span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Implement an Apache core hook that has no return code, and</span>
<span style="color: #00cd00;"> * therefore runs all of the registered functions. The implementation</span>
<span style="color: #00cd00;"> * is called ap_run_&lt;i&gt;name&lt;i&gt;.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> name The name of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_decl The declaration of the arguments for the hook, for</span>
<span style="color: #00cd00;"> * example "(int x, void *y)"</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_use The arguments for the hook as used in a call, for</span>
<span style="color: #00cd00;"> * example "(x,y)"</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@note</span><span style="color: #00cd00;"> If IMPLEMENTing a hook that is not linked into the Apache</span>
<span style="color: #00cd00;"> * core, (e.g. within a dso) see APR_IMPLEMENT_EXTERNAL_HOOK_VOID.</span>
<span style="color: #00cd00;"> */</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">AP_IMPLEMENT_HOOK_VOID</span>(<span style="color: #cdcd00;">name</span>,<span style="color: #cdcd00;">args_decl</span>,<span style="color: #cdcd00;">args_use</span>) \
  APR_IMPLEMENT_EXTERNAL_HOOK_VOID(ap,AP,name,args_decl,args_use)
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c" id="APR_IMPLEMENT_EXTERNAL_HOOK_VOID"><span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Implement a hook that has no return code, and therefore runs all of</span>
<span style="color: #00cd00;"> * the registered functions</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ns The namespace prefix of the hook functions</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> link The linkage declaration prefix of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> name The name of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_decl The declaration of the arguments for the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_use The names for the argumentsw for the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@note</span><span style="color: #00cd00;"> The link prefix FOO corresponds to </span><span style="color: #cd00cd;">FOO_DECLARE()</span><span style="color: #00cd00;"> macro, which</span>
<span style="color: #00cd00;"> * provide export linkage from the module that IMPLEMENTs the hook,</span>
<span style="color: #00cd00;"> * and import linkage from external modules that link to the hook's</span>
<span style="color: #00cd00;"> * module.</span>
<span style="color: #00cd00;"> */</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">APR_IMPLEMENT_EXTERNAL_HOOK_VOID</span>(<span style="color: #cdcd00;">ns</span>,<span style="color: #cdcd00;">link</span>,<span style="color: #cdcd00;">args_decl</span>,<span style="color: #cdcd00;">args_use</span>) \
  APR_IMPLEMENT_EXTERNAL_HOOK_BASE(ns,link,name) \
  link##_DECLARE(<span style="color: #00cd00;">void</span>) ns##_run_##name args_decl \
  { \
  ns##_LINK_##name##_t *pHook; \
  <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">n</span>; \
  <span style="color: #00cdcd; font-weight: bold;">if</span> (!_hooks.link_##name) \
    <span style="color: #00cdcd; font-weight: bold;">return</span>; \
  pHook = (ns##_LINK_##name##_t *)_hooks.link_##name-&gt;elts; \
  <span style="color: #00cdcd; font-weight: bold;">for</span> (n = 0; n &lt; _hooks.link_##name-&gt;nelts; ++n) \
    pHook[n].pFunc args_use; \
  }
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c" id="APR_IMPLEMENT_EXTERNAL_HOOK_BASE"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">macro to implement the hook </span><span style="color: #cd0000;">*/</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">APR_IMPLEMENT_EXTERNAL_HOOK_BASE</span>(<span style="color: #cdcd00;">ns</span>,<span style="color: #cdcd00;">link</span>,<span style="color: #cdcd00;">name</span>) \
  link##_DECLARE(<span style="color: #00cd00;">void</span>) ns##_hook_##name(ns##_HOOK_##name##_t *pf, \
                                        <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> * <span style="color: #00cdcd; font-weight: bold;">const</span> *<span style="color: #cdcd00;">aszPre</span>, \
                                        <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> * <span style="color: #00cdcd; font-weight: bold;">const</span> *<span style="color: #cdcd00;">aszSucc</span>, \
                                        <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">nOrder</span>) \
  { \
    ns##_LINK_##name##_t *pHook; \
    <span style="color: #00cdcd; font-weight: bold;">if</span> (!_hooks.link_##name) \
    { \
    _hooks.link_##name = apr_array_make(apr_gblobal_pool, \
                                        1, \
                                        <span style="color: #00cdcd; font-weight: bold;">sizeof</span>(ns##_LINK_##name##_t)); \
    } \
    pHook = apr_array_push(_hooks.link_##name); \
    pHook-&gt;pFunc = pf; \
    pHook-&gt;aszPredecessors = aszPre; \
    pHook-&gt;aszSuccessors = aszSucc; \
    pHook-&gt;nOrder = nOrder; \
    pHook-&gt;szName = apr_hook_debug_current; \
    <span style="color: #00cdcd; font-weight: bold;">if</span> (apr_hook_debug_enabled) \
      apr_hook_debug_show(#name, aszPre, aszSucc); \
  } \
  <span style="color: #0000ee; font-weight: bold;">APR_IMPLEMENT_HOOK_GET_PROTO</span>(<span style="color: #00cd00;">ns</span>,<span style="color: #00cd00;">link</span>,<span style="color: #00cd00;">name</span>)    \
  {                                             \
    <span style="color: #00cdcd; font-weight: bold;">return</span> _hooks.link_##name;                  \
  }
</pre>
</div>

<p>
在前面的介绍中，知道 <code>_hooks.link_##name</code> 的类型是 <br/>
   <code>apr_array_header_t *</code> 类型的，在Apache APR的文档中有很好的说明。
</p>

<p>
宏 <code>APR_IMPLMENT_EXTERNAL_HOOK_VOID</code> 首先调用宏 <br/>
   <code>APR_IMPLEMENT_EXTERNAL_HOOK_BASE</code> ,HOOK_BASE宏实现了函数 <br/>
   <code>ns##_hook_##name</code> 和 函数 <code>ns##_hook_get_##name</code> 。HOOK_VOID然后实现
了函数 <code>ns##_run_##name</code> 。这样可以看到，在hook声明过程中申明的三个
函数在这里都得到了实现。
</p>

<p>
函数 <code>ns##_hook_##name</code> 主要是将参数中的hook函数添加到staitc结构体
中对应的array中；函数 <code>ns##_hook_get_##name</code> 则用于获取这个array；
最后一个函数 <code>ns##_run_##name</code> 则会运行所有这个array中所有的hook函
数。
</p>

<p>
另外一类hook则是有返回值的，这样的hook又可以分为两类，一类是所有注
册的hook函数都被执行，另一类则是运行部分注册的hook函数，在循环执行
所有注册的hook函数的过程中，第一个不返回 <code>DECLINE</code> 的hook函数终止这
个循环并记录其返回值，后面的hook函数则不会被执行，然后将返回值返回
给hook的调用者，这样的hook的实现由宏 <code>AP_IMPLEMENT_HOOK_RUN_FIRST</code>
完成：
</p>
<div class="org-src-container">

<pre class="src src-c" id="AP_IMPLEMENT_HOOK_RUN_FIRST"><span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Implement a hook that runs until a function returns something other</span>
<span style="color: #00cd00;"> * than decline. If all functions return decline, the hook runner</span>
<span style="color: #00cd00;"> * returns decline. The implemention is called ap_run_&lt;i&gt;name&lt;/i&gt;.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ret The return type of the hook (and the hook runner)</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> name The name of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_decl The declaration of the arguments for the hook, for</span>
<span style="color: #00cd00;"> * example "(int x, void *y)"</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_use The arguments for the hook as used in a call for</span>
<span style="color: #00cd00;"> * example "(x,y)"</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> decline The "decline" return value</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> decline or an error.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@note</span><span style="color: #00cd00;"> If Implementing a hook that is not linked into the Apache</span>
<span style="color: #00cd00;"> * core (e.g. within a dso) see APR_IMPLEMENT_EXTERNAL_HOOK_RUN_FIRST.</span>
<span style="color: #00cd00;"> */</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">AP_IMPLEMENT_HOOK_RUN_FIRST</span>(<span style="color: #cdcd00;">ret</span>,<span style="color: #cdcd00;">name</span>,<span style="color: #cdcd00;">args_decl</span>,<span style="color: #cdcd00;">args_use</span>,<span style="color: #cdcd00;">decline</span>) \
  APR_IMPLEMENT_EXTERNAL_HOOK_RUN_FIRST(ap,AP,ret,name,args_decl, \
                                        args_use,decline)
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c" id="APR_IMPLEMENT_EXTERNAL_HOOK_RUN_FIRST"><span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Implement a hook that runs until the first function returns</span>
<span style="color: #00cd00;"> * something other than the value of decline</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ns The namespace prefix of the hook functions</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> link The linkage declaration prefix of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> name The name of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ret Type to return</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_decl The declaration of the argument for the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_use The name for the arguments for the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> decline Decline value</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@note</span><span style="color: #00cd00;"> The link prefix FOO corresponds to </span><span style="color: #cd00cd;">FOO_DECLARE()</span><span style="color: #00cd00;"> macros,</span>
<span style="color: #00cd00;"> * which provide export linkage from the module that IMPLEMNTs the</span>
<span style="color: #00cd00;"> * hook, and import linkage from external modules that link to the</span>
<span style="color: #00cd00;"> * hook's module.</span>
<span style="color: #00cd00;"> */</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">APR_IMPLEMENT_EXTERNAL_HOOK_RUN_FIRST</span>(<span style="color: #cdcd00;">ns</span>,<span style="color: #cdcd00;">link</span>,<span style="color: #cdcd00;">ret</span>,<span style="color: #cdcd00;">name</span>, \
                                              <span style="color: #cdcd00;">args_decl</span>, <span style="color: #cdcd00;">args_use</span>, \
                                              <span style="color: #cdcd00;">decline</span>) \
  APR_IMPLEMENT_EXTERNAL_HOOK_BASE(ns,link,name) \
  link##DECLARE(ret) ns##_run_##name args_decl \
  { \
  ns##_LINK_##name##_t *pHook; \
  <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">n</span>; \
  <span style="color: #00cd00;">ret</span> <span style="color: #cdcd00;">rv</span>; \
  \
  <span style="color: #00cdcd; font-weight: bold;">if</span> (!_hooks.link_##name) \
    <span style="color: #00cdcd; font-weight: bold;">return</span> decline; \
  \
  pHook = (ns##_LINK_##name##_t *)_hooks.link_##name-&gt;elts; \
  <span style="color: #00cdcd; font-weight: bold;">for</span> (n = 0; n &lt; _hooks.link_##name-&gt;nelts; ++n) \
  { \
  rv = pHook[n].pFunc args_use; \
  <span style="color: #00cdcd; font-weight: bold;">if</span> (rv != decline) \
    <span style="color: #00cdcd; font-weight: bold;">return</span> rv; \
  } \
  <span style="color: #00cdcd; font-weight: bold;">return</span> decline; \
  }
</pre>
</div>

<p>
可以看到， <code>AP_IMPLEMNET_HOOK_RUN_FIRST</code> 宏的区别只是实现的函数
<code>ns##_run_##name</code> 不同：在循环执行所有注册hook函数的过程中，当遇到
第一个返回值不为decline的hook函数后，循环就终止了，后面的hook函数就
不会被执行了；其余的函数的实现则和void类型hook是一样的。
</p>

<p>
有返回值的Hook的另外一种是所有注册的hook函数都会被执行，直到遇到错
误或者全部执行完了。这类hook的实现是由宏 <br/>
   <code>AP_IMPLEMENT_HOOK_RUN_ALL</code> 完成的：
</p>
<div class="org-src-container">

<pre class="src src-c" id="AP_IMPLEMENT_HOOK_RUN_ALL"><span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Implement an Apache core hook that runs until one of the functions</span>
<span style="color: #00cd00;"> * returns something other than ok or decline. The return valie is</span>
<span style="color: #00cd00;"> * then returned from the hook runner. If the hooks run to completion,</span>
<span style="color: #00cd00;"> * then ok is returned. Note that if no hooks runs it would probably</span>
<span style="color: #00cd00;"> * be more correct to return decline, but this currently does not do</span>
<span style="color: #00cd00;"> * so. The implemention is called ap_run_&lt;i&gt;name&lt;/i&gt;.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ret The return type of the hook (and the hook runner)</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> name The name of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_decl The declaration of the arguments for the hook, for</span>
<span style="color: #00cd00;"> * example "(int x, void *y)"</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ok The "ok" return value</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> decline The "decline" return value</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> ok, decline, or an error</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@note</span><span style="color: #00cd00;"> If IMPLEMENTing a hook that is not linked into the Apache</span>
<span style="color: #00cd00;"> * core, (e.g. within a dso) see APR_IMPLEMENT_EXTERNAL_HOOK_RUN_ALL.</span>
<span style="color: #00cd00;"> */</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">AP_IMPLEMENT_HOOK_RUN_ALL</span>(<span style="color: #cdcd00;">ret</span>,<span style="color: #cdcd00;">name</span>,<span style="color: #cdcd00;">args_decl</span>,<span style="color: #cdcd00;">args_use</span>, \
                                  <span style="color: #cdcd00;">ok</span>,<span style="color: #cdcd00;">decline</span>) \
  APR_IMPLEMENT_EXTERNAL_HOOK_RUN_ALL(ap,AP,ret,name,args_decl, \
                                      args_use,ok,decline)
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c" id="APR_IMPLEMENT_EXTERNAL_HOOK_RUN_ALL"><span style="color: #cd0000;">/** </span><span style="color: #cd0000;">FIXME: note that this returns ok when noting is run. I suspect it</span>
<span style="color: #cd0000;"> * should really return decline, but that breaks Apache currently - Ben</span>
<span style="color: #cd0000;"> </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Implement a hook that runs until one of the functions returns</span>
<span style="color: #00cd00;"> * something other than OK or DECLINE</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ns The namespace prefix of the hook functions</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> link The linkage declaration prefix of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ret Type to return</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> name The name of the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_decl The declaration of the arguments for the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> args_use The names for the arguments for the hook</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ok Success value</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> decline Decline value</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@note</span><span style="color: #00cd00;"> The link prefix FOO corresponds to </span><span style="color: #cd00cd;">FOO_DECLARE()</span><span style="color: #00cd00;"> macros,</span>
<span style="color: #00cd00;"> * which provide export linkage from the module that IMPLEMENTs the</span>
<span style="color: #00cd00;"> * hook, and import linkage from external modules that lik to the</span>
<span style="color: #00cd00;"> * hook's module.</span>
<span style="color: #00cd00;"> */</span>
<span style="color: #0000ee; font-weight: bold;">#define</span> <span style="color: #0000ee; font-weight: bold;">APR_IMPLEMNET_EXTERNAL_HOOK_RUN_ALL</span>(<span style="color: #cdcd00;">ns</span>,<span style="color: #cdcd00;">link</span>,<span style="color: #cdcd00;">ret</span>,<span style="color: #cdcd00;">name</span>, \
                                            <span style="color: #cdcd00;">args_decl</span>,<span style="color: #cdcd00;">args_use</span>, \
                                            <span style="color: #cdcd00;">ok</span>,<span style="color: #cdcd00;">decline</span>) \
  APR_IMPLEMNET_EXTERNAL_HOOK_BASE(ns,link,name) \
  link##_DECLARE(ret) ns##_run_##name args_decl \
  { \
  ns##_LINK_##name##_t *pHook; \
  <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">n</span>; \
  <span style="color: #00cd00;">ret</span> <span style="color: #cdcd00;">rv</span>; \
  \
  <span style="color: #00cdcd; font-weight: bold;">if</span> (!_hooks.link_##name) \
    <span style="color: #00cdcd; font-weight: bold;">return</span> ok; \
  \
  pHook = (ns##_LINK_##name##_t *)_hooks.link_##name-&gt;elts; \
  <span style="color: #00cdcd; font-weight: bold;">for</span> (n = 0; n &lt; _hooks.link_##name-&gt;nelts; ++n) \
  { \
  rv = pHook[n].pFunc args_use; \
  <span style="color: #00cdcd; font-weight: bold;">if</span> (rv != ok &amp;&amp; rv != decline) \
    <span style="color: #00cdcd; font-weight: bold;">return</span> rv; \
  } \
  <span style="color: #00cdcd; font-weight: bold;">return</span> ok; \
  }
</pre>
</div>

<p>
宏 <code>APR_IMPLEMENT_EXTERNAL_HOOK_RUN_ALL</code> 和宏 <br/>
   <code>APR_IMPLEMENT_EXTERNAL_HOOK_RUN_FIRST</code> 很相似，都需要检查注册函数
执行的返回值，只不过前者是在错误情况（返回值既不为ok也不是decline）
下终止循环，而后者是在非decline情况下就终止了循环；注意，前者这里有
一个FIXME标注，在没有hook函数被执行的情况下，前者仍然是返回ok的，这
和后者的返回decline是不同的。
</p>
</div>
</div>
<div id="outline-container-sec-2-2" class="outline-3">
<h3 id="sec-2-2"><span class="section-number-3">2.2</span> Optional Hook</h3>
<div class="outline-text-3" id="text-2-2">
<p>
Apache2中除了有Hook之外，还有一类Optional hook，还没有仔细研究。
</p>
</div>
</div>
<div id="outline-container-sec-2-3" class="outline-3">
<h3 id="sec-2-3"><span class="section-number-3">2.3</span> Apache2中的hook</h3>
<div class="outline-text-3" id="text-2-3">
<ol class="org-ol">
<li>access_checker
<div class="org-src-container">

<pre class="src src-c" id="access_checker"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_request.c 331 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook is used to apply additional access control to this</span>
<span style="color: #00cd00;"> * resource. It runs *before* a user is authenticated, so this hook is</span>
<span style="color: #00cd00;"> * really to apply additional restrictions independent of a user. It</span>
<span style="color: #00cd00;"> * also runs independent of 'Require' directive usage.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> OK, DECLINE, or HTTP_...</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, access_checker, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/request.c 76 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>,access_checker,
                          (request_rec *r),
                          (r),
                          DECLINE)
</pre>
</div>
</li>
<li>auth_checker
<div class="org-src-container">

<pre class="src src-c" id="auth_checker"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_request.h 344 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook is used to check to see if the resource beging requested</span>
<span style="color: #00cd00;"> * is available for the authenticated user (r-&gt;user and</span>
<span style="color: #00cd00;"> * r-&gt;ap_auth_type). It runs after the access_checker and</span>
<span style="color: #00cd00;"> * check_user_id hooks. Note that it will *only* be called if Apache</span>
<span style="color: #00cd00;"> * determines that access control has been applied to this resource</span>
<span style="color: #00cd00;"> * (though a 'Require' directive).</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK, DECLINE, or HTTP_...</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, auth_checker, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/request.c 78 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">int</span>,auth_checker,
                            (request_rec *r),
                            (r),
                            DECLINE)
</pre>
</div>
</li>
<li>check_user_id
<div class="org-src-container">

<pre class="src src-c" id="check_user_id"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_request.h 300 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook is used to analyse the request headers, authenticate the</span>
<span style="color: #00cd00;"> * user, and set the user information in the request record (r-&gt;user</span>
<span style="color: #00cd00;"> * and r-&gt;ap_auth_type). This hook is only run when Apache determines</span>
<span style="color: #00cd00;"> * that authentication/authorization is required for this resource (as</span>
<span style="color: #00cd00;"> * determined by the 'Require' directive). It runs after the</span>
<span style="color: #00cd00;"> * access_checker hook, and before the auth_checker hook.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK, DECLINE, or HTTP_...</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>,check_user_id, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/request.c 70 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">int</span>,check_user_id,
                            (request_rec *r),
                            (r),
                            DECLINE)
</pre>
</div>
</li>
<li>child_init
<div class="org-src-container">

<pre class="src src-c" id="child_init"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_config.h 1022 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Run the child_init functions for each module</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> pchild The child pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> s The list of server_recs in this server</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">void</span>, child_init, (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pchild</span>, server_rec *s))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/config.c 153 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_VOID(child_init,
                       (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pchild</span>, server_rec *s),
                       (pchild, s))
</pre>
</div>
</li>
<li>create_connection
<div class="org-src-container">

<pre class="src src-c" id="create_connection"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_connection.h 94 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * create_connection is a RUN_FIRST hook which allows modules to</span>
<span style="color: #00cd00;"> * create connections. In general, you should not install filters with</span>
<span style="color: #00cd00;"> * the create_connection hook. If you require vhost configuration</span>
<span style="color: #00cd00;"> * information to make filter installation decisions, you must use the</span>
<span style="color: #00cd00;"> * pre_connection or install_network_transport hook. This hook should</span>
<span style="color: #00cd00;"> * close the connection if it encounters a fatal error condition.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> p The pool from which to allocate the connection record</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> server The server record to create the connection too.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> csd The socket that has been accepted</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> conn_id A unique identifier for this connection. The ID only</span>
<span style="color: #00cd00;"> * needs to be unique at that time, not forever.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> sbh A handle to scoreboard information for this connection.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> alloc The bucket allocator to use for all bucket/bridge</span>
<span style="color: #00cd00;"> * creations</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> An allocated connection record or NULL</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">conn_rec</span> *, create_connection,
                (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">p</span>, server_rec *server, <span style="color: #00cd00;">apr_socket_t</span> *<span style="color: #cdcd00;">csd</span>,
                 <span style="color: #00cd00;">long</span> <span style="color: #cdcd00;">conn_id</span>, <span style="color: #00cd00;">void</span> *<span style="color: #cdcd00;">sbh</span>, <span style="color: #00cd00;">apr_bucket_alloc_t</span> *<span style="color: #cdcd00;">alloc</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/connection.c 40 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">conn_rec</span> *,create_connection,
                            (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">p</span>, server_rec *server, <span style="color: #00cd00;">apr_socket_t</span> *<span style="color: #cdcd00;">csd</span>, <span style="color: #00cd00;">long</span> <span style="color: #cdcd00;">conn_id</span>, <span style="color: #00cd00;">void</span> *<span style="color: #cdcd00;">sbh</span>, <span style="color: #00cd00;">apr_bucket_alloc_t</span> *<span style="color: #cdcd00;">alloc</span>),
                            (p, server, csd, conn_id, sbh, alloc),
                            <span style="color: #cd00cd;">NULL</span>)
</pre>
</div>
</li>
<li>create_request
<div class="org-src-container">

<pre class="src src-c" id="create_request"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_request.h 261 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Gives modules a chance to create their request_config entry when</span>
<span style="color: #00cd00;"> * the request is created.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@ingroup</span><span style="color: #00cd00;"> hooks</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, create_request, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/request.c 81 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, create_request,
                          (request_rec *r),
                          (r),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>default_port
<div class="org-src-container">

<pre class="src src-c" id="default_port"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_protocol.h </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Return the default port from the current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> The current port</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(apr_port_t, default_port, (<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/protocol.c </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">unsigned</span> <span style="color: #00cd00;">short</span>,default_port,
                            (<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>),
                            (r),
                            0)
</pre>
</div>
</li>
<li>error_log
<div class="org-src-container">

<pre class="src src-c" id="error_log"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_log.h 350 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * hook method to log error messages</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@ingroup</span><span style="color: #00cd00;"> hooks</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> file The file in which this function is called</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> line The line number on which the function is called</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> level The level of the this error message</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> status The status code from the previous command</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> s The server which we are logging for</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The request which we are logging for</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> pool Memeory pool to allocate from</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> errstr Message to log</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">void</span>, error_log, (<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">file</span>, <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">line</span>,
                                  <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">level</span>, <span style="color: #00cd00;">apr_status_t</span> <span style="color: #cdcd00;">status</span>,
                                  <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">server_rec</span> *<span style="color: #cdcd00;">s</span>,
                                  <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>,
                                  <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pool</span>,
                                  <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">errstr</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/log.c 1116 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_VOID(error_log,
                       (<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">file</span>, <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">line</span>, <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">level</span>,
                        <span style="color: #00cd00;">apr_status_t</span> <span style="color: #cdcd00;">status</span>, <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">server_rec</span> *<span style="color: #cdcd00;">s</span>,
                        <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>, <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pool</span>,
                        <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">errstr</span>), (file, line, level,
                                              status, s, r, pool,
                                              errstr))
</pre>
</div>
</li>
<li>fatal_exception
<div class="org-src-container">

<pre class="src src-c" id="fatal_exception"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/ap_mpm.h 183 </span><span style="color: #cd0000;">*/</span>
AP_DECALRE_HOOK(<span style="color: #00cd00;">int</span>, fatal_exception, (<span style="color: #00cd00;">ap_exception_info_t</span> *<span style="color: #cdcd00;">ei</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/mpm_common.c 67 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, fatal_exception,
                          (<span style="color: #00cd00;">ap_exception_info_t</span> *<span style="color: #cdcd00;">ei</span>), (ei),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>fixups
<div class="org-src-container">

<pre class="src src-c" id="fixups"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_request.h 309 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Allows modules to perform module-sepcific fixing of header</span>
<span style="color: #00cd00;"> * fields. This is invoked just before any content-handler</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK, DECLINE, or HTTP_...</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, fixups, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/request.c 72 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>,fixups,
                          (request_rec *r), (r),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>get_mgmt_items
<div class="org-src-container">

<pre class="src src-c" id="get_mgmt_items"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_core.h 685 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook provides a way for modules to provide mterics/statics</span>
<span style="color: #00cd00;"> * about their operational status.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> p A pool to use to create entries in the hash table</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> val The name of the paramter(s) that is wanted. This is</span>
<span style="color: #00cd00;"> * tree-structured would be in the form ('*' is all the tree,</span>
<span style="color: #00cd00;"> * 'module.*' all of the module, 'module.foo.*', or 'module.foo.bar')</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ht The hash table to store the result. Keys are item names,</span>
<span style="color: #00cd00;"> * and the value point to ap_mgmt_item_t strutures.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@ingroup</span><span style="color: #00cd00;"> hooks</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, get_mgmt_items,
                (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">p</span>, <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">val</span>, <span style="color: #00cd00;">apr_hash_t</span> *<span style="color: #cdcd00;">ht</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/core.c 76 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, get_mgmt_items,
                          (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">p</span>, <span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">val</span>, <span style="color: #00cd00;">apr_hash_t</span> *<span style="color: #cdcd00;">ht</span>),
                          (p, val, ht),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>get_suexec_identity
<div class="org-src-container">

<pre class="src src-c" id="get_suexec_identity"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: os/unix/unixd.h </span><span style="color: #cd0000;">*/</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">ap_unix_identity_t</span> *, get_suexec_identiy, (<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: os/unix/unixd.c 344 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">ap_unix_identity_t</span> *, get_suexec_identity,
                            (<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>), (r),
                            <span style="color: #cd00cd;">NULL</span>)
</pre>
</div>
</li>
<li>handler
<div class="org-src-container">

<pre class="src src-c" id="handler"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_config.h 1029 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Run the handler function for each module</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The request_rec</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@remark</span><span style="color: #00cd00;"> non-wildcard handlers should HOOK_MIDDLE, wildcard HOOK_LAST</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, handler, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/config.c 157 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">int</span>, handler, (request_rec *r),
                            (r), DECLINE)
</pre>
</div>
</li>
<li>header_parser
<p>
#+ <a id="src:header_parser" name="src:header_parser"></a>
</p>
<div class="org-src-container">

<pre class="src src-c" id="header_parser"><span class="linenr">1: </span><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_config.h 975 </span><span style="color: #cd0000;">*/</span>
<span id="coderef-header_parser" class="coderef-off"><span class="linenr">2: </span><span style="color: #00cd00;">/**</span></span>
<span class="linenr">3: </span><span style="color: #00cd00;"> * Run the header parser functions for each module</span>
<span class="linenr">4: </span><span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span class="linenr">5: </span><span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK or DECLINED</span>
<span class="linenr">6: </span><span style="color: #00cd00;"> */</span>
<span class="linenr">7: </span>AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, header_parser, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/config.c 79 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_RUN_ALL(<span style="color: #00cd00;">int</span>, header_parser,
                     (request_rec *r),
                     (r),
                     OK, DECLINE)
</pre>
</div>
</li>
<li>http_scheme
<div class="org-src-container">

<pre class="src src-c" id="http_scheme"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_protocol.h 592 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook allows modules to retrieve the http scheme for a</span>
<span style="color: #00cd00;"> * request. This allows Apache modules to easily extend the schemes</span>
<span style="color: #00cd00;"> * that Apache understands</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> The http scheme from the request</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *, http_scheme, (<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/protocol.c 1698 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *, http_scheme,
                            (<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>),
                            (r),
                            <span style="color: #cd00cd;">NULL</span>)
</pre>
</div>
</li>
<li>insert_error_filter
<div class="org-src-container">

<pre class="src src-c" id="insert_error_filter"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_protocol.h 45 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook allows modules to insert filters for the current error</span>
<span style="color: #00cd00;"> * response</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@ingroup</span><span style="color: #00cd00;"> hooks</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">void</span>, insert_error_filter, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: modules/http/http_protocol.c 148 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_VOID(insert_error_filter, (request_rec *r), (r))
</pre>
</div>
</li>
<li>insert_filter
<div class="org-src-container">

<pre class="src src-c" id="insert_filter"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_request.h 351 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook allows modules to insert filters for the current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">void</span>, insert_filter, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/request.c 80 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_VOID(insert_filter, (request_rec *r), (r))
</pre>
</div>
</li>
<li>log_transaction
<div class="org-src-container">

<pre class="src src-c" id="log_transaction"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_protocol.h 584 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook allows modules to preform any module-specific logging</span>
<span style="color: #00cd00;"> * activities over and above the normal server things.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK, DECLINE, or HTTP_...</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, log_transaction, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/protocol.c 1696 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>,log_transaction,
                          (request_rec *r), (r),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>map_to_storage
<div class="org-src-container">

<pre class="src src-c" id="map_to_storage"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_request.h 286 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook allow modules to set the per_dir_config based on their</span>
<span style="color: #00cd00;"> * own context (such as "&lt;proxy&gt;" sections) and the responds to</span>
<span style="color: #00cd00;"> * contextless requests such as TRACE that need no security or</span>
<span style="color: #00cd00;"> * filesystem mapping.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> DONE or (HTTP_) if this contextless request was just</span>
<span style="color: #00cd00;"> * fulfilled (such as TRACE), OK if this is not a file, or DECLINE  if</span>
<span style="color: #00cd00;"> * this is a file.</span>
<span style="color: #00cd00;"> * The core map_to_strorage (HOOK_RUN_REALLY_FIRST) will</span>
<span style="color: #00cd00;"> * directory_walk and file_walk the r-&gt;filename.</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, map_to_strorage, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/request.c 68 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">int</span>,map_to_storage,
                            (request_rec *r), (r),
                            DECLINE)
</pre>
</div>
</li>
<li>monitor
<div class="org-src-container">

<pre class="src src-c" id="monitor"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/mpm_common.h 379 </span><span style="color: #cd0000;">*/</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, monitor, (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">p</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/mpm_common.c 74 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, monitor,
                          (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">p</span>),
                          (p),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>open_logs
<div class="org-src-container">

<pre class="src src-c" id="open_logs"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_config.h 1014 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Run the open_logs function for each module</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> pconf The config pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> plog The logging streams pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ptemp The temporary pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> s The list of server_recs</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK or DECLINED on success anything else is a error</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, open_logs, (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pconf</span>, <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">plog</span>,
                                 <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">ptemp</span>, server_rec *s))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/config.c 148 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, open_logs,
                          (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pconf</span>, <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">plog</span>,
                           <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">ptemp</span>, server_rec *s),
                          (pconf, plog, ptemp, s),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>optional_fn_retrieve
<div class="org-src-container">

<pre class="src src-c" id="optional_fn_retrieve"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_config.h 1049 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Retrieve the optional functions for each module.</span>
<span style="color: #00cd00;"> * This is run immediately before the server starts. Optional</span>
<span style="color: #00cd00;"> * functions should be registered during the hook registration phase.</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">void</span>, optional_fn_retrieve, (<span style="color: #00cd00;">void</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/config.c 163 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_VOID(optional_fn_retrieve, (<span style="color: #00cd00;">void</span>), ())
</pre>
</div>
</li>
<li>post_config
<div class="org-src-container">

<pre class="src src-c" id="post_config"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_config.h 1003 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Run the post_config function for each module</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> pconf The config pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> plog The logging streams pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ptemp The temporary pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> s The list of server_recs</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK or DECLINED on success anything else is a error</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, post_config, (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pconf</span>, <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">plog</span>
                                   apr_pool_t *ptemp, server_rec *s))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/config.c 91 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, post_config,
                          (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pconf</span>, <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">plog</span>,
                           <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">ptemp</span>, server_rec *s),
                          (pconf, plog, ptemp, s),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>post_read_request
<div class="org-src-container">

<pre class="src src-c" id="post_read_request"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_protocol.h 576 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * post_read_request --- run right after read_request or</span>
<span style="color: #00cd00;"> * internal_redirect and not run during any subrequests.</span>
<span style="color: #00cd00;"> */</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook allows modules to affect the request immediately after</span>
<span style="color: #00cd00;"> * the request has been read, and before any other phases have been</span>
<span style="color: #00cd00;"> * processes. This allows modules to make decisions based upon the</span>
<span style="color: #00cd00;"> * input header fields</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK or DECLINE</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, post_read_request, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/protocol.c 1694 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, post_read_request,
                          (request_rec *r), (r),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>pre_config
<div class="org-src-container">

<pre class="src src-c" id="pre_config"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_config.h 984 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Run the pre_config function for each module</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> pconf The config pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> plog The logging streams pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ptemp The temporary pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK or DECLINED on success anything else is a error</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, pre_config, (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pconf</span>, <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">plog</span>,
                                  <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">temp</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/config.c 82 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, pre_config,
                          (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pconf</span>, <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">plog</span>,
                           <span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">ptemp</span>),
                          (pconf, plog, ptemp),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>pre_connection
<div class="org-src-container">

<pre class="src src-c" id="pre_connection"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_connection.h 108 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook gives protocol modules an opportunity to set everything</span>
<span style="color: #00cd00;"> * up before calling the protocol handler. All pre_connection hooks</span>
<span style="color: #00cd00;"> * are run until one returns something other than ok or decline</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> c The connection on which the request has been received.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> csd The mechanism on which this connection is to be</span>
<span style="color: #00cd00;"> * read. Most times this will be a socket, but it is up to the module</span>
<span style="color: #00cd00;"> * that accepts the request to determine the exact type.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK or DECLINE</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, pre_connection, (conn_rec *C, <span style="color: #00cd00;">void</span> *<span style="color: #cdcd00;">csd</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/connection.c 44 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, pre_connection,
                          (conn_rec *c, <span style="color: #00cd00;">void</span> *<span style="color: #cdcd00;">csd</span>),
                          (c, csd),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>pre_mpm
<div class="org-src-container">

<pre class="src src-c" id="pre_mpm"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/scoreboard.h 212 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Hook for post scoreboard createion, pre mpm.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> p Apache pool to allocate from</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> sb_type</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK or DECLINE on success; anything else is a error</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, pre_mpm, (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">p</span>, <span style="color: #00cd00;">ap_scoreboard_e</span> <span style="color: #cdcd00;">sb_type</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/scoreboard.c 60 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, pre_mpm,
                          (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">p</span>, <span style="color: #00cd00;">ap_scoreboard_e</span> <span style="color: #cdcd00;">sb_type</span>),
                          (p, sb_type),
                          OK, DECLINE)
</pre>
</div>
</li>
<li>process_connection
<div class="org-src-container">

<pre class="src src-c" id="process_connection"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_connection.h 118 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook implements different protocols. After a connection has</span>
<span style="color: #00cd00;"> * been established, the protocol module must read and serve the</span>
<span style="color: #00cd00;"> * request. This function does that for each protocol module. Ths</span>
<span style="color: #00cd00;"> * first protocol module to handle the request is the last module run.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> c The connection on which the request has been received.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK or DECLINE</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, process_connection, (conn_rec *c))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/connection.c 43 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">int</span>, process_connection,
                            (conn_rec *c), (c),
                            DECLINE)
</pre>
</div>
</li>
<li>quick_handler
<div class="org-src-container">

<pre class="src src-c" id="quick_hanlder"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_config.h 1042 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Run the quick handler functions for each module. The quick_handler</span>
<span style="color: #00cd00;"> * is run before any other requests hooks are called (location_walk,</span>
<span style="color: #00cd00;"> * directory_walk, access checking, et. al.). This hook was added to</span>
<span style="color: #00cd00;"> * provide a quick way to server content from a URI keyed cache.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The request_rec</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> lookup_uri Controls whether the caller actually wants</span>
<span style="color: #00cd00;"> * content of not. look up is set when the quick_handler is called out</span>
<span style="color: #00cd00;"> * of </span><span style="color: #cd00cd;">ap_sub_req_lookup_uri()</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, quick_handler, (request_rec *r, <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">lookup_uri</span>))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/config.c 160 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">int</span>, quick_handler,
                            (request_rec *r, <span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">lookup</span>),
                            (r, lookup),
                            DECLINE)
</pre>
</div>
</li>
<li>test_config
<div class="org-src-container">

<pre class="src src-c" id="test_config"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_config.h 993 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * Run the test_config function for each module; this hook is run</span>
<span style="color: #00cd00;"> * only if the server was invoked to test the configuration syntax.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> pconf The config pool</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> s The list of server_recs</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">void</span>, test_config, (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">pconf</span>, server_rec *s))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/config.c 87 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_VOID(test_config,
                       (<span style="color: #00cd00;">apr_pool_t</span> <span style="color: #cdcd00;">pconf</span>, server_rec *s),
                       (pconf, s))
</pre>
</div>
</li>
<li>translate_name
<div class="org-src-container">

<pre class="src src-c" id="translate_name"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_request.h 271 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This hook allows modules an opportunity to translate the URI into</span>
<span style="color: #00cd00;"> * an actual filename. If no modules do anything special, the server's</span>
<span style="color: #00cd00;"> * default rules will be followed.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK, DECLINE, or HTTP_...</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, translate_name, (request_rec *r))
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/request.c 66 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">int</span>, transalte_name,
                            (request_rec *r),
                            (r), DECLINE)
</pre>
</div>
</li>
<li>type_checker
<div class="center">
<div class="org-src-container">

<pre class="src src-c" id="type_checker"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: include/http_request.h 319 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This routine is called to determine and/or set the various document</span>
<span style="color: #00cd00;"> * type information bits, link Content-type (via r-&gt;content_type),</span>
<span style="color: #00cd00;"> * language, et cetera.</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> r The current request</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@return</span><span style="color: #00cd00;"> OK, DECLINE, or HTTP_...</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE_HOOK(<span style="color: #00cd00;">int</span>, type_checker)
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/request.c 74 </span><span style="color: #cd0000;">*/</span>
AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">int</span>, type_checker,
                            (request_rec *r),
                            (r),
                            DECLINE)
</pre>
</div>
</div>
</li>
</ol>
<p>
够多吧。。
</p>
</div>
</div>
</div>
<div id="outline-container-sec-3" class="outline-2">
<h2 id="sec-3"><span class="section-number-2">3</span> Filters</h2>
<div class="outline-text-2" id="text-3">
<p>
Filters是Apache2中的另一个特点，在一般http server中，请求从客户端发
送，server接受后直接交给http server处理，http处理后的结果则直接通过
网络返回给客户端；但在Apache2中，从客户端发送来的请求，可以被Apache2
中的模块处理后，再交于http模块处理请求，http模块处理后返回的结果也不
是直接返回给客户端，其他的模块可以先处理http模块的返回结果后，再传给
客户端，这就是Apache2中的filters。
</p>

<p>
模块可以创作input_filter或者output_filter，http请求在被结果后，请求
首先被这些input_filter处理，一层层的input_filter处理完成后，在由http
模块来处理http请求，http模块的处理结果则首先被output_filter处理，所
有的output_filter处理完成后，最后通过网络发送出去。
</p>

<p>
filters的设计使得某些功能相当容易实现，比如加解密操作，压缩与解压缩
操作等等。
</p>
</div>

<div id="outline-container-sec-3-1" class="outline-3">
<h3 id="sec-3-1"><span class="section-number-3">3.1</span> filter的使用</h3>
<div class="outline-text-3" id="text-3-1">
<p>
在模块中，filters需要注册:
</p>
<div class="org-src-container">

<pre class="src src-c" id="ap_register_output_filter"><span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This function is used to register an output filter with the</span>
<span style="color: #00cd00;"> * system. After this registration is performed, then a filter may be</span>
<span style="color: #00cd00;"> * added into the filter chian by using </span><span style="color: #cd00cd;">ap_add_output_filter()</span><span style="color: #00cd00;"> and</span>
<span style="color: #00cd00;"> * simply specifying the name. It may also be used as a provider under</span>
<span style="color: #00cd00;"> * mod_filter. This is (equivalent to)</span>
<span style="color: #00cd00;"> * ap_register_output_filter_protocol with proto_flag=0, and is</span>
<span style="color: #00cd00;"> * retained for back-compatibility with 2.0 modules.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> name The name to attach to the filter function</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> filter_func The filter function to name</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> filter_init The function to call before the filter handlers</span>
<span style="color: #00cd00;"> * are invoked</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ftype The type of filter function, either ::AP_FTYPE_CONTENT</span>
<span style="color: #00cd00;"> * or ::AP_FTYPE_CONNECTION</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE(<span style="color: #00cd00;">ap_filter_rec_t</span> *) ap_register_output_filter(<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">name</span>,
                                                        <span style="color: #00cd00;">ap_out_filter_func</span> <span style="color: #cdcd00;">filter_func</span>,
                                                        <span style="color: #00cd00;">ap_init_filter_func</span> <span style="color: #cdcd00;">filter_init</span>,
                                                        <span style="color: #00cd00;">ap_filter_type</span> <span style="color: #cdcd00;">ftype</span>);
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c" id="ap_register_input_filter"><span style="color: #00cd00;">/**</span>
<span style="color: #00cd00;"> * This function is used to register an input filter with the</span>
<span style="color: #00cd00;"> * system. After this registration is performed, then a filter may be</span>
<span style="color: #00cd00;"> * added into the filter chain by using </span><span style="color: #cd00cd;">ap_add_input_filter()</span><span style="color: #00cd00;"> and</span>
<span style="color: #00cd00;"> * simply specifying the name.</span>
<span style="color: #00cd00;"> *</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> name The name to attach to the filter function</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> filter_func The function to call before the filter handlers</span>
<span style="color: #00cd00;"> * are invoked</span>
<span style="color: #00cd00;"> * </span><span style="color: #cd00cd;">@param</span><span style="color: #00cd00;"> ftype The type of filter function, either ::AP_FTYPE_CONTENT</span>
<span style="color: #00cd00;"> * or ::AP_FTYPE_CONNECTION</span>
<span style="color: #00cd00;"> */</span>
AP_DECLARE(<span style="color: #00cd00;">ap_filter_rec_t</span> *<span style="color: #cdcd00;">r</span>) ap_register_input_filter(<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">name</span>,
                                                        <span style="color: #00cd00;">ap_in_filter_func</span> <span style="color: #cdcd00;">filter_func</span>,
                                                        <span style="color: #00cd00;">ap_init_filter_func</span> <span style="color: #cdcd00;">filter_init</span>,
                                                        <span style="color: #00cd00;">ap_filter_type</span> <span style="color: #cdcd00;">ftype</span>);
</pre>
</div>

<p>
在filter被注册后，就可以将filter加入到filter chain之中了，加入的方
法有有很多。下面两个函数可以将filter添加到filter chain：
</p>
<div class="org-src-container">

<pre class="src src-c" id="ap_add_output_filter"><span style="color: #cd0000;">/*</span>
<span style="color: #cd0000;"> * Add a filter to the current request. Filters are added in a FIFO</span>
<span style="color: #cd0000;"> * manner. The first filter added will be the first filter called.</span>
<span style="color: #cd0000;"> * @param name The name of the filter to add</span>
<span style="color: #cd0000;"> * @param ctx Context data to set in the filter</span>
<span style="color: #cd0000;"> * @param r The request to add this filter for (or NULL if it isn't</span>
<span style="color: #cd0000;"> * associated with a request)</span>
<span style="color: #cd0000;"> * @param c The connection to add this filter for</span>
<span style="color: #cd0000;"> </span><span style="color: #cd0000;">*/</span>
AP_DECLARE(<span style="color: #00cd00;">ap_filter_t</span> *) ap_add_output_filter(<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">name</span>,
                                               <span style="color: #00cd00;">void</span> *<span style="color: #cdcd00;">ctx</span>,
                                               request_rec *r,
                                               conn_rec *c);
</pre>
</div>
<div class="org-src-container">

<pre class="src src-c" id="ap_add_input_filter"><span style="color: #cd0000;">/*</span>
<span style="color: #cd0000;"> * Adds a named filter into the filter chain on the specified request record.</span>
<span style="color: #cd0000;"> * The filter will be installed with the specified context pointer.</span>
<span style="color: #cd0000;"> *</span>
<span style="color: #cd0000;"> * Filters added in this way will be always be placed at the end of</span>
<span style="color: #cd0000;"> * the filters that have the same type (thus, the filters have the</span>
<span style="color: #cd0000;"> * same order as the calls to ap_add_filter). If the current filter</span>
<span style="color: #cd0000;"> * chain contains filters from another request, than this filter will</span>
<span style="color: #cd0000;"> * be added before those other filters.</span>
<span style="color: #cd0000;"> *</span>
<span style="color: #cd0000;"> * To re-iterate that last comment. This function is building a FIFO</span>
<span style="color: #cd0000;"> * list of filters. Take note of that when adding you filter to the chain.</span>
<span style="color: #cd0000;"> *</span>
<span style="color: #cd0000;"> * @param name The name of the filter to add</span>
<span style="color: #cd0000;"> * @param ctx Context data to provide to the filter</span>
<span style="color: #cd0000;"> * @param r The request to add this filter for (or NULL if it isn't</span>
<span style="color: #cd0000;"> * associated with a request)</span>
<span style="color: #cd0000;"> * @param c The connection to added the filter for</span>
<span style="color: #cd0000;"> </span><span style="color: #cd0000;">*/</span>
AP_DECLARE(<span style="color: #00cd00;">ap_filter_t</span> *) ap_add_input_filter(<span style="color: #00cdcd; font-weight: bold;">const</span> <span style="color: #00cd00;">char</span> *<span style="color: #cdcd00;">name</span>,
                                              <span style="color: #00cd00;">void</span> *<span style="color: #cdcd00;">ctx</span>,
                                              request_rec *r,
                                              conn_rec *c);
</pre>
</div>

<p>
一种方法是首先使用hook insert_filter，向这个hook注册一个hook函数，在函
数里决定是否添加filter到filter chain。
</p>

<p>
源代码modules/experimental/里面有两个最简单的filter示例：
mod_case_filter和mode_case_filter_in，这两个filter的功能就是把输出
或者输入全部大写化，一个简单的 <code>toupper</code> 操作。
</p>
</div>
</div>
</div>
<div id="outline-container-sec-4" class="outline-2">
<h2 id="sec-4"><span class="section-number-2">4</span> 请求的处理</h2>
<div class="outline-text-2" id="text-4">
<p>
这里以hook执行的顺序来解释http请求在Apache2中处理的过程。在accept到
一个连接后，首先执行的hook是 <code>create_connection</code> :
</p>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/*</span>
<span style="color: #cd0000;"> * We now have a connection, so set it up with the appropriate socket</span>
<span style="color: #cd0000;"> * options, file descriptors, and read/write buffer.</span>
<span style="color: #cd0000;"> </span><span style="color: #cd0000;">*/</span>
current_connection = ap_run_create_connection(ptrans, ap_server_conf, csd, my_child_num, sbh, bucket_alloc);
</pre>
</div>

<p>
然后是 <code>pre_connection</code> ，在函数 <code>ap_process_connection</code> 中被调用：
</p>
<div class="org-src-container">

<pre class="src src-c">rc = ap_run_pre_connection(c, csd);
</pre>
</div>

<p>
再是 <code>process_connection</code> ，同样是在函数 <code>ap_process_connection</code> 中
被调用的：
</p>
<div class="org-src-container">

<pre class="src src-c">ap_run_process_connection(c);
</pre>
</div>

<p>
这些hook运行完了，处理就处理完了。
</p>

<p>
下面看有那些模块都向这些hook注册了hook函数的呢？
</p>
</div>

<div id="outline-container-sec-4-1" class="outline-3">
<h3 id="sec-4-1"><span class="section-number-3">4.1</span> create_connection的注册者</h3>
<div class="outline-text-3" id="text-4-1">
<p>
<code>create_connection</code> 是一个RUN_FIRST类型的hook，为什么呢？因为在
server/connection.c里面的 <code>create_connection</code> 的实现是这样的:
</p>
<div class="org-src-container">

<pre class="src src-c">AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">conn_rec</span> *, create_connection,
                            (<span style="color: #00cd00;">apr_pool_t</span> *<span style="color: #cdcd00;">p</span>,
                             server_rec *server,
                             <span style="color: #00cd00;">apr_socket_t</span> *<span style="color: #cdcd00;">csd</span>,
                             <span style="color: #00cd00;">long</span> <span style="color: #cdcd00;">conn_id</span>,
                             <span style="color: #00cd00;">void</span> *<span style="color: #cdcd00;">sbh</span>,
                             <span style="color: #00cd00;">apr_bucket_alloc_t</span> *<span style="color: #cdcd00;">alloc</span>),
                            (p, server, csd, conn_id, sbh, alloc),
                            <span style="color: #cd00cd;">NULL</span>)
</pre>
</div>
<p>
这最后一个参数是NULL，代表什么意思呢，在前面的hook实现中已经知道了，
最后一个参数是 <code>decline</code> 的值，这就说明如果 <code>create_connect</code> 的注册
函数如果返回NULL就是返回了decline了，这样下一个hook将继续执行，如果
返回的是非NULL，后面的hook就不会被执行的，将直接返回这个值给
<code>create_connection</code> 的调用者。
</p>

<p>
下面看看都有谁注册了宏 <code>create_connection</code> 呢？这个只要看谁调用了
<code>ap_hook_create_connection</code> 就可以了：发现只有已有一个模块core
module注册了这个hook：
</p>
<div class="org-src-container">

<pre class="src src-c">ap_hook_create_connection(core_create_conn, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>,
                          APR_HOOK_REALLY_LAST);
</pre>
</div>
<p>
<code>core_create_conn</code> 做的事情就是创建 <code>conn_rec</code> 对象，并填好其中的各
个字段，如本地ip，远端ip等等。
</p>
</div>
</div>
<div id="outline-container-sec-4-2" class="outline-3">
<h3 id="sec-4-2"><span class="section-number-3">4.2</span> pre_connection的注册者</h3>
<div class="outline-text-3" id="text-4-2">
<p>
<code>pre_connection</code> 是一个RUN_ALL类型的hook：
</p>
<div class="org-src-container">

<pre class="src src-c">AP_IMPLEMENT_HOOK_RUN_ALL(<span style="color: #00cd00;">int</span>, pre_connection,
                          (conn_rec *c, <span style="color: #00cd00;">void</span> *<span style="color: #cdcd00;">csd</span>),
                          (c, csd),
                          OK,
                          DECLINE)
</pre>
</div>

<p>
注册hook <code>pre_connection</code> 的模块就很多了。下面列出version 2.2.21源
代码中注册了 <code>pre_connection</code> 的模块：
</p>
<ol class="org-ol">
<li>core_module
core_module(server/core.c)在register hooks的过程中注册了
<code>pre_connection</code>:
<div class="org-src-container">

<pre class="src src-c">ap_hook_pre_connection(core_pre_connection, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>,
                       APR_HOOK_REALLY_LAST);
</pre>
</div>
<p>
<code>core_pre_connection</code> 设置了连接的timeout等属性，并添加了一个
input_filter和一个output_filter：
</p>
<div class="org-src-container">

<pre class="src src-c">ap_add_input_filter_handle(ap_core_input_filter_handle, net, <span style="color: #cd00cd;">NULL</span>, net-&gt;c);
ap_add_output_filter_handle(ap_core_output_filter_handle, net, <span style="color: #cd00cd;">NULL</span>, net-&gt;c);
</pre>
</div>
</li>
<li>logio_module
logio_module(modules/loggers/mod_logio.c)注册了
<code>pre_connection</code> :
<div class="org-src-container">

<pre class="src src-c">ap_hook_pre_connection(logio_pre_conn, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, APR_HOOK_MIDDLE);
</pre>
</div>
<p>
函数 <code>logio_pre_conn</code> 同样添加了两个filter到filter chain里面：
</p>
<div class="org-src-container">

<pre class="src src-c">ap_add_input_filter(logio_filter_name, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, c);
ap_add_output_filter(logio_filter_name, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, c);
</pre>
</div>
</li>
<li>dumpio_module
dumpio_module(modules/debug/mod_dumpio.c)：
<div class="org-src-container">

<pre class="src src-c">ap_hook_pre_connection(dumpio_pre_conn, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, APR_HOOK_MIDDLE);
</pre>
</div>
<p>
函数 <code>dumpio_pre_conn</code> 根据配置选择是否添加两个filters：
</p>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #00cd00;">dumpio_conf_t</span> *<span style="color: #cdcd00;">ptr</span> =
    (<span style="color: #00cd00;">dumpio_conf_t</span> *) ap_get_module_config(c-&gt;base_server-&gt;module_config,
                                           &amp;dumpio_module);

<span style="color: #00cdcd; font-weight: bold;">if</span> (ptr-&gt;enable_input)
  ap_add_input_filter(<span style="color: #00cd00;">"DUMPIO_IN"</span>, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, c);
<span style="color: #00cdcd; font-weight: bold;">if</span> (ptr-&gt;enable_output)
  ap_add_output_filter(<span style="color: #00cd00;">"DUMPIO_OUT"</span>, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, c);
</pre>
</div>
</li>
<li>ssl_module
ssl_module(modules/ssl/mod_ssl.c)：
<div class="org-src-container">

<pre class="src src-c">ap_hook_pre_connection(ssl_hook_pre_connection, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, APR_HOOK_MIDDLE);
</pre>
</div>
</li>
<li>nwssl_module
nwssl_module(modules/arch/netware/mod_nw_ssl.c)：
<div class="org-src-container">

<pre class="src src-c">ap_hook_pre_connection(nwssl_pre_connection, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, APR_HOOK_MIDDLE);
</pre>
</div>
</li>
<li>example_module 
示例模块example_module(modules/experimental/mod_example.c)：
<div class="org-src-container">

<pre class="src src-c">ap_hook_pre_connection(x_pre_connection, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, APR_HOOK_MIDDLE);
</pre>
</div>
<p>
函数 <code>x_pre_connection</code> 什么也没做，之是简单的log trace一下。
</p>
</li>
</ol>
</div>
</div>
<div id="outline-container-sec-4-3" class="outline-3">
<h3 id="sec-4-3"><span class="section-number-3">4.3</span> process_connection的注册者</h3>
<div class="outline-text-3" id="text-4-3">
<p>
<code>process_connection</code> 是一个RUN_FIRST类型的hook，在
server/connection.c里面：
</p>
<div class="org-src-container">

<pre class="src src-c">AP_IMPLEMENT_HOOK_RUN_FIRST(<span style="color: #00cd00;">int</span>, process_connection,
                            (conn_rec *r),
                            (c),
                            DECLINE)
</pre>
</div>

<p>
注册 <code>process_connection</code> 的模块有：
</p>
<ol class="org-ol">
<li>http_module
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/*</span>
<span style="color: #cd0000;"> * If we are using an MPM that supports Async Connections,</span>
<span style="color: #cd0000;"> * use a different processing function</span>
<span style="color: #cd0000;"> </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">int</span> <span style="color: #cdcd00;">async_mpm</span> = 0;
<span style="color: #00cdcd; font-weight: bold;">if</span> (ap_mpm_query(AP_MPMQ_IS_ASYNC, &amp;async_mpm) == APR_SUCCESS
    &amp;&amp; async_mpm == 1) {
  ap_hook_process_connection(ap_process_http_async_connection,
                             <span style="color: #cd00cd;">NULL</span>,
                             <span style="color: #cd00cd;">NULL</span>,
                             APR_HOOK_REALLY_LAST);
}
<span style="color: #00cdcd; font-weight: bold;">else</span> {
  ap_hook_process_connection(ap_process_http_connection,
                             <span style="color: #cd00cd;">NULL</span>,
                             <span style="color: #cd00cd;">NULL</span>,
                             APR_HOOK_REALLY_LAST);
}
</pre>
</div>
<p>
函数 <code>ap_mpm_query</code> 是每个MPM都必须实现的函数，用于查询MPM的属性，
http_module在这里查询了 <code>AP_MPMQ_IS_ASYNC</code> 这个属性，目前在所有
的MPM中，只有event这个MPM支持 <code>AP_MPMQ_IS_ASYNC</code> 这个属性，所以
这里主要观察函数 <code>ap_process_http_connection</code> :
</p>

<p>
<code>ap_process_http_connection</code> 的主体是一个循环的调用
<code>ap_read_request</code> :
</p>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #00cdcd; font-weight: bold;">static</span> <span style="color: #00cd00;">int</span> <span style="color: #0000ee; font-weight: bold;">ap_process_http_connection</span>(<span style="color: #00cd00;">conn_rec</span> *<span style="color: #cdcd00;">c</span>)
{
  <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>;
  <span style="color: #00cd00;">apr_socket_t</span> *<span style="color: #cdcd00;">csd</span> = <span style="color: #cd00cd;">NULL</span>;
  <span style="color: #cd0000;">/*</span>
<span style="color: #cd0000;">   * Read and process each request found on our connection</span>
<span style="color: #cd0000;">   * until no request are left or we decide to close.</span>
<span style="color: #cd0000;">   </span><span style="color: #cd0000;">*/</span>
  ap_update_child_status(c-&gt;sbh, SERVER_BUSY_READ, <span style="color: #cd00cd;">NULL</span>);
  <span style="color: #00cdcd; font-weight: bold;">while</span> ((r = ap_read_request(c)) != <span style="color: #cd00cd;">NULL</span>) {
    <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">... loop body before process request ... </span><span style="color: #cd0000;">*/</span>
    <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">process the request if it was read without error </span><span style="color: #cd0000;">*/</span>
    <span style="color: #00cdcd; font-weight: bold;">if</span> (r-&gt;status == HTTP_OK)
      ap_process_request(r);
    <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">... loop body after process request ... </span><span style="color: #cd0000;">*/</span>
  }

  <span style="color: #00cdcd; font-weight: bold;">return</span> OK;
}
</pre>
</div>

<p>
下面看看函数 <code>ap_read_request</code> ：
</p>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: server/protocol.c:845 </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">request_rec</span> *<span style="color: #0000ee; font-weight: bold;">ap_read_request</span>(<span style="color: #00cd00;">conn_rec</span> *<span style="color: #cdcd00;">conn</span>)
{
  <span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>;
  <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">... construct the request_rec object ... </span><span style="color: #cd0000;">*/</span>
  r = apr_pcalloc(p, <span style="color: #00cdcd; font-weight: bold;">sizeof</span>(request_rec));
  <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">set each properties of r </span><span style="color: #cd0000;">*/</span>
  <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">Must be set before we run create request hook </span><span style="color: #cd0000;">*/</span>
  ap_run_create_request(r);

  <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">Get the request... </span><span style="color: #cd0000;">*/</span>

  ap_add_input_filter_handle(ap_http_input_filter_handle,
                             <span style="color: #cd00cd;">NULL</span>, r, r-&gt;connection);

  <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">... </span><span style="color: #cd0000;">*/</span>

  <span style="color: #00cdcd; font-weight: bold;">if</span> ((access_status = ap_run_post_read_request(r))) {
    <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">... </span><span style="color: #cd0000;">*/</span>
  }

  ap_run_log_transaction(r);

  <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">... </span><span style="color: #cd0000;">*/</span>

  <span style="color: #00cdcd; font-weight: bold;">return</span> r;
}
</pre>
</div>
<p>
函数 <code>ap_read_request</code> 又运行了hook create_request和hook
post_read_request。
</p>

<p>
在 <code>ap_read_request</code> 成功且没有发生错误后，就表示请求已经成功的
接收到了，然后继续由
<code>ap_process_request</code> 来处理了，下面接续看函数
<code>ap_process_request</code> :
</p>
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/* </span><span style="color: #cd0000;">file: modules/http/http_request.c </span><span style="color: #cd0000;">*/</span>
<span style="color: #00cd00;">void</span> <span style="color: #0000ee; font-weight: bold;">ap_process_request</span>(<span style="color: #00cd00;">request_rec</span> *<span style="color: #cdcd00;">r</span>)
{
  <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">Give quick handlers a shot at serving the request on the fast path,</span>
<span style="color: #cd0000;">   * bypassing all of the other Apache hooks.</span>
<span style="color: #cd0000;">   </span><span style="color: #cd0000;">*/</span>
  <span style="color: #00cdcd; font-weight: bold;">if</span> (ap_extended_status)
    ap_time_process_request(r-&gt;connection-&gt;sbh, START_PREQUEST);
  access_status = ap_run_quick_handler(r, 0); <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">Not a look-up request</span>
<span style="color: #cd0000;">                                               * </span><span style="color: #cd0000;">*/</span>
  <span style="color: #00cdcd; font-weight: bold;">if</span> (access_status == DECLINE) {
    access_status = ap_process_request_internal(r);
    <span style="color: #00cdcd; font-weight: bold;">if</span> (access_status == OK) {
      access_status = ap_invoke_handler(r);
    }
  }

  <span style="color: #00cdcd; font-weight: bold;">if</span> (access_status == DONE) {
    <span style="color: #cd0000;">/* </span><span style="color: #cd0000;">e.g., something not in storage like TRACE </span><span style="color: #cd0000;">*/</span>
    access_status = OK;
  }

  <span style="color: #00cdcd; font-weight: bold;">if</span> (access_status == OK) {
    ap_finalize_request_protocol(r);
  }
  <span style="color: #00cdcd; font-weight: bold;">else</span> {
    r-&gt;status = HTTP_OK;
    ap_die(access_status, r);
  }

  <span style="color: #cd0000;">/*</span>
<span style="color: #cd0000;">   * We want to flush the last packet if this isn't a pipelining</span>
<span style="color: #cd0000;">   * connection *before* we start into logging. Suppose that logging</span>
<span style="color: #cd0000;">   * cause a DNS lookup to occur, which may have a high latency. If we</span>
<span style="color: #cd0000;">   * hold off on this packet, then it'll appear like the link is</span>
<span style="color: #cd0000;">   * stalled when really it's the application that's stalled.</span>
<span style="color: #cd0000;">   </span><span style="color: #cd0000;">*/</span>
  check_pipeline_flush(r);
  ap_update_child_status(r-&gt;connection-&gt;sbh, SERVER_BUSY_LOG, r);
  ap_run_log_transaction(r);
  <span style="color: #00cdcd; font-weight: bold;">if</span> (ap_extended_status)
    ap_time_process_request(r-&gt;connection-&gt;sbh, STOP_PREQUEST);
}
</pre>
</div>
<p>
函数 <code>ap_process_request</code> 首先运行了hook quick_handler，然后主要
调用了三个函数：
<code>ap_process_request_internal</code> ， <code>ap_invoke_handler</code> 和
<code>ap_finalize_request_protocol</code> 。
</p>

<p>
从Apache2的developer文档中，可以看到函数
<code>ap_process_request_internal</code> 可以分为下面几个阶段：
</p>
<ul class="org-ul">
<li>Request Parsing Phase
这阶段的动作有URI的检查，Location Walk，translate name (hook
translate_name), map to storage (hook map_to_storage)，Header
parse (hook header_parser)。
</li>
<li>Security Phase
</li>
<li>Preparation Phase
这阶段主要运行两个hook：type_checker 和 fixups.
</li>
</ul>
<p>
上面三个阶段完成后，下面一步就是相应内容的产生，所以fixup就在产
生相应内容之前运行的最后一个hook了。
函数 <code>ap_invoke_handler</code> 继续完成下面这个阶段：
</p>
<ul class="org-ul">
<li>Handler Phase
这个阶段首先运行hook insert_filter，然后初始化filters，然后运
行hook handler：
<div class="org-src-container">

<pre class="src src-c">result = ap_run_handler(r);
</pre>
</div>
</li>
</ul>
<p>
<code>ap_process_request</code> 最后调用了函数
<code>ap_finalize_request_protocol</code> 。
</p>

<p>
这样这个请求就被处理完成了。
</p>
</li>
<li>reqtimeout_module
<div class="org-src-container">

<pre class="src src-c"><span style="color: #cd0000;">/*</span>
<span style="color: #cd0000;"> * mod_reqtimeout needs to be called before ap_process_http_request</span>
<span style="color: #cd0000;"> * (which is run at APR_HOOK_REALLY_LAST) but after all other protocol</span>
<span style="color: #cd0000;"> * modules. This ensures that it only influences normal http</span>
<span style="color: #cd0000;"> * connections and not e.g. mod_ftp. Also, if mod_reqtimeout used the</span>
<span style="color: #cd0000;"> * pre_connection hook, it would be insert on mod_proxy's backend</span>
<span style="color: #cd0000;"> * connections.</span>
<span style="color: #cd0000;"> </span><span style="color: #cd0000;">*/</span>
ap_hook_process_connection(reqtimeout_init, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>, APR_HOOK_LAST);
</pre>
</div>
<p>
reqtimeout_module是给Apache2 server提供设置超时的一个模块，提供
了很细致的超时控制，如接受http header的超时定时器，接受body的超
时定时器等。
</p>

<p>
<code>reqtimeout_init</code> 所做的就是获取配置信息等工作。
</p>
</li>
<li>echo_module
<div class="org-src-container">

<pre class="src src-c">ap_hook_process_connection(process_echo_connection, <span style="color: #cd00cd;">NULL</span>,
                           <span style="color: #cd00cd;">NULL</span>, APR_HOOK_MIDDLE);
</pre>
</div>
<p>
echo_module是什么模块呢？原来Apache的模块中有一类是协议模块，做
为http server的Apache2，http_module是必不可少的，此外还有一些协
议模块如ftp模块，ftp_module则可以使Apache2变成一个ftp server。
echo_module也是一个协议模块，只不过echo_module实现的协议相当简单：
echo server，开启echo_module后，telnet到Apache2 server上，
echo_module就简单的返回所有接收到的数据。
</p>

<p>
所以函数 <code>process_echo_connection</code> 就很简单，它首先得到配置，查
看echo是否被启动，如果没有启用就返回DECLINE，如果启用了则从接受
到的数据中一行行的读取，没读取一行就返回同样的数据给客户端。
</p>
</li>
<li>example_module
<div class="org-src-container">

<pre class="src src-c">ap_hook_process_connection(x_process_connection, <span style="color: #cd00cd;">NULL</span>, <span style="color: #cd00cd;">NULL</span>,
                           APR_HOOK_MIDDLE);
</pre>
</div>
<p>
example_module在前面提到了是Apache2的一个示例模块快，给模块开发者提供一个示
范。所以在 <code>x_process_connection</code> 函数中，example_module什么事情
也没有做，只是返回DECLINE。
</p>
</li>
</ol>
<p>
完了。
</p>
</div>
</div>
<div id="outline-container-sec-4-4" class="outline-3">
<h3 id="sec-4-4"><span class="section-number-3">4.4</span> request_rec结构</h3>
<div class="outline-text-3" id="text-4-4">
<p>
request_rec是一个庞大的结构
</p>
</div>

<div id="outline-container-sec-4-4-1" class="outline-4">
<h4 id="sec-4-4-1"><span class="section-number-4">4.4.1</span> request_time</h4>
<div class="outline-text-4" id="text-4-4-1">
<p>
request_time表示接收到这个请求的时间，在函数 <code>read_request_line</code>
里面被标记上了，它又被 <code>ap_read_request</code> 调用，在上面知道了
<code>ap_read_request</code> 是 <code>ap_process_connection</code> 的第一步。
</p>
</div>
</div>
</div>
</div>
<div id="outline-container-sec-5" class="outline-2">
<h2 id="sec-5"><span class="section-number-2">5</span> Notes</h2>
<div class="outline-text-2" id="text-5">
<ul class="org-ul">
<li>Last update: <span class="timestamp-wrapper"><span class="timestamp">[2011-11-24 四 16:46]</span></span> 
</li>
</ul>
</div>
</div>
</div>
<div id="postamble" class="status">
<p class="date">Date: 2011-12-18</p>
<p class="date">Created: 2013-06-28 五 22:38</p>
<p class="creator"><a href="http://www.gnu.org/software/emacs/">Emacs</a> 24.3.1 (<a href="http://orgmode.org">Org</a> mode 8.0.2)</p>
<p class="xhtml-validation"><a href="http://validator.w3.org/check?uri=referer">Validate XHTML 1.0</a></p>
</div>
</body>
</html>
