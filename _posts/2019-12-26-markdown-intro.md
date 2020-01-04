---
layout: post
title: Markdown 学习笔记
categories: Markdown
description: 学习 Markdown 的笔记与心路历程
keywords: Markdown
---

初次接触 Markdown 是在 Github，看到 .md 文件还搞不懂这是什么，后来就去了解了一下，发觉这真的是个好东西，现在只要是编辑文本文档我首选 Markdown (emmm,写论文就算了，M$还是躲不过的)，所以来吃我安利！

## 简介

照例先看下 Markdown 到底是个什么：

> Markdown 是一种轻量级标记语言，创始人为 John Gruber。它允许人们「使用易读易写的纯文本格式编写文档，然后转换成有效的 XHTML（或者 HTML）文档」。——维基百科

既然是标记语言肯定要记一些语法啦，不过好在 Markdown 语法简单，真正用起来会特别流畅舒适。

### 优点

第一个优点就是可以让你不用关心排版。定义好一套渲染之后就不需要在写作的时候关心排版问题，能够专注与内容的写作，你要做的就只有添加几个标签而已。

第二个优点是纯文本，跨平台的兼容性比较好，易于版本控制，这大概也是 Github 用它来写 README 文档的原因吧。  
不过这也导致一个问题就是插图不太方便，需要图床来引用图片。

第三呢就是它真的很简单，用起来很爽。

### 那么要怎么写呢？

纯文档文件嘛，理论上任何文本编辑器都能编辑，只是差在实时预览或者语法提醒之类的功能。Windows 上的记事本打开阅读 md 文档没什么问题，但是编辑还是算了吧。下面推荐几个好用点的。

* 在线版

    1. [dillinger](https://www.dillinger.io)
   
        界面漂亮，功能强大，支持 md, html, pdf 文件导出，还支持 html 转 Markdown。不过是国外的，可能国内访问网络不是太稳定。

    2. [StackEdit](https://stackedit.io)
   
        有拼写检查，不过对中文支持不好。

    3. [简书](https://www.jianshu.com)
   
        挺好的一个博客平台，图片支持也不错，需要注册。

* PC
    
    1. VSCode / Atom

        VSCode 当然要推荐啦，我现在就在用。插件很多很方便，还是开源的，Git 也很方便，有什么道理不用呢？

    2. Vim / Emacs / Sublime Text / Notepad++
   
        神之编辑器 Vim，这个学起来就比较复杂了，但是学会了用起来不要太爽，我在终端环境都是用 Vim。

    3. 各类 IDE

        这个不用解释了，IDE 哪个不带文本编辑器呢

    4. 专用编辑器

        Ulysses / Mou / Typora / Markpad

## 基本语法

Markdown 的不同格式的文本通过标签来标注，标签与文本之间需要一个空格。

### 段落

非常自然，一行就是一个段落，不需要任何标签。  
例如：

`这是一个段落`

等效于 HTML 中的：

`<p>这是一个段落</p>`

如果想要另起一段，两个段落之间空一行就可以了：

```markdown
这是一个段落

这是另一个段落
```
等效与 HTML 中：
```html
<p>这是一个段落</p>
<p>这是另一个段落</p>
```
**关于换行**

关于换行，各解释器的处理会有不同。有些会解释为换行，即插入 `<br/>` 标记;有的不会解释为换行，需要手动插入 `<br/>` 标签，或者在上一行结尾处加入两个空格。

比如：

**Markdown：**

```markdown
通信原理

数字电路
(后边没有空格)

信号系统

模拟电路  
(后边两个空格)
```

**效果：**

通信原理

数字电路
(后边没有空格)

信号系统

模拟电路  
(后边两个空格)

**等效 HTML：**

```html
<p>通信原理</p>

<p>数字电路
(后边没有空格)</p>

<p>信号系统</p>

<p>
  模拟电路
  <br>
  (后边两个空格)
</p>
```

### 段内格式

段内或段落的文本格式，通常用于强调，比如加粗、斜体、删除线等。

**Markdown：**

```markdown
后面俩字**加粗**

后面俩字*斜体*

后面俩字~~划掉~~
```

**效果：**

后面俩字**加粗**

后面俩字*斜体*

后面俩字~~划掉~~

**对应 HTML：**

```html
<p>
  后面俩字
  <strong>加黑</strong>
</p>
<p>
  后面俩字
  <em>斜体</em>
</p>
<p>
  后面俩字
  <del>划掉</del>
</p>
```

### 标题

markdown 支持1~6六级标题，通过在一行之前加上不同数量的井号来表示。

例如：

**Markdown：**

```markdown
# 一级标题

## 二级标题

### 三级标题 ###

###### 六级标题
```

行尾可以加上任意数量的井号字符，这些字符不会算作标题内容。

**效果预览：**

> # 一级标题
> 
> ## 二级标题
> 
> ### 三级标题
> 
> ###### 六级标题

**对应 HTML：**

```html
<h1>一级标题</h1>

<h2>二级标题</h2>

<h3>三级标题</h3>

<h6>六级标题</h6>
```

此外，H1和H2也可以采用在文本下方添加底线来实现，比如：

```markdown
一级标题
=====

二级标题
----
```

### 引用

通过在行首加上大于号 `>` 来添加引用格式。

**Markdown：**

```markdown
> 我没说过这句话
>
>         -鲁迅
```

**效果：**

> 我没说过这句话
> 
>         -鲁迅

**等效 HTML：**

```html
<blockquote>
  <p>我没说过这句话</p>
  <p>        -鲁迅</p>
</blockquote>
```

引用可以嵌套，例如：

**Markdown：**

```markdown
> 引用块段落一。
>
> 引用块段落二。
>> 内嵌引用块段落一。
>
> ### 引用块内的标题
```

**预览效果：**

> 引用块段落一。
>
> 引用块段落二。
>
> > 内嵌引用块段落一。
>
> ### 引用块内的标题

**等效 HTML：**

```html
<blockquote>
  <p>引用块段落一。</p>
  <p>引用块段落二。</p>
  <blockquote>
    <p>内嵌引用块段落一。</p>
  </blockquote>
  <h3 id="引用块内的标题">引用块内的标题</h3>
</blockquote>
```

### 列表

列表包括无序列表和有序列表，无序列表使用星号、加号或是减号作为列表标记，有序列表则使用数字接着一个英文句点：

**Markdown：**

```markdown
- 数电
- 模电
- 通信原理
- 信号系统

* 数电
* 模电
* 通信原理
* 信号系统

1. 数电
2. 模电
3. 通信原理
4. 信号系统

4. 数电
3. 模电
2. 通信原理
1. 信号系统
```

**效果：**

- 数电
- 模电
- 通信原理
- 信号系统

* 数电
* 模电
* 通信原理
* 信号系统

1. 数电
2. 模电
3. 通信原理
4. 信号系统

1. 数电
2. 模电
3. 通信原理
4. 信号系统

**等效 HTML：**

```html
<ul>
  <li>数电</li>
  <li>模电</li>
  <li>通信原理</li>
  <li>信号系统</li>
</ul>
<ol>
  <li>数电</li>
  <li>模电</li>
  <li>通信原理</li>
  <li>信号系统</li>
</ol>
```

可以看到，标记的数字并不会影响输出的 HTML 结果。

### 内联代码

用反引号 \` 来标记内联代码，它们会解释成 `<code>` 标签。如果代码的内容中有反引号，请
用两个反引号包裹。代码中的 `&` 、 `<` 、 `>` 符号都会自动转义，请放心使用。

### 代码块

有两种方式标记代码区域，原生风格是行首缩进四个空格。

另一种是github的风格，代码段的前后用三个反引号独占一行来标记。

像内联代码一样，上述三种符号也会被转义。但在代码段中，星号之类的 markdown 标记符号则不会解析。

**Markdown：**

    Android 里使用 `TextUtils` 类的 `isEmpty` 方法来判断字符串是否为空。

    ```java
    if (TextUtils.isEmpty(text)) {
        return null;
    }
    ```

**预览效果：**

Android 里使用 `TextUtils` 类的 `isEmpty` 方法来判断字符串是否为空。

```java
if (TextUtils.isEmpty(text)) {
    return null;
}
```

**对应 HTML：**

```html
<p>Android 里使用 <code>TextUtils</code> 类的 <code>isEmpty</code> 方法来判断字符串是否为空。</p>

<div class="highlight highlight-source-java"><pre><span class="pl-k">if</span> (<span class="pl-smi">TextUtils</span><span class="pl-k">.</span>isEmpty(text)) {
    <span class="pl-k">return</span> <span class="pl-c1">null</span>;
}</pre></div>
```

上例中的语言标记 `java` 可选填，可用于在编辑器和渲染后的效果里添加语法高亮。语法高亮要看编辑器/解析器的支持。

### 分割线

你可以在一行中用三个以上的星号`*`、减号`-`、底线`_`来建立一个分隔线，行内不能有其他东西。你也可以在星号或是减号中间插入空格。

**Markdown：**

```
***

-----

- - -
```

**效果：**

***

-----

- - -

**对应 HTML：**

```
<hr />

<hr />

<hr />
```

### 链接

Markdown 支持行内式链接和引用式链接。

**Markdown：**

```
行内式 [博客](https://chamboin.tk "我的个人博客") 链接，带 title。

行内式 [GitHub](https://github.com/chamboin) 链接。

引用式 [博客][1] 链接。

引用式 [GitHub][2] 链接，带 title。

[1]: https://chamboin.tk
[2]: https://github.com/chamboin "我的 GitHub 主页"
```

**预览效果：**

行内式 [博客](https://chamboin.tk "我的个人博客") 链接，带 title。

行内式 [GitHub](https://github.com/chamboin) 链接。

引用式 [博客][1] 链接。

引用式 [GitHub][2] 链接，带 title。

[1]: https://chamboin.tk
[2]: https://github.com/chamboin "我的 GitHub 主页"

**对应 HTML：**

```html
<p>行内式 <a href="https://chamboin.tk" title="我的个人博客">博客</a> 链接，带 title。</p>

<p>行内式 <a href="https://github.com/chamboin">GitHub</a> 链接。</p>

<p>引用式 <a href="https://chamboin.tk">博客</a> 链接。</p>

<p>引用式 <a href="https://github.com/chamboin" title="我的 GitHub 主页">GitHub</a> 链接，带 title。</p>
```

### 自动链接

如果链接的地址和名字重复，可以用尖括号语法将其简化。

`<https://chamboin.tk>`

相当于

`[https://chamboin.tk](https://chamboin.tk)`

### 图片

在超链接的写法前加一个 `!`，就是引用图片的方法。

**Markdown：**

```
![Alt text](https://chamboin.github.io/favicon.ico "favicon")
```

**预览效果：**

![Alt text](https://chamboin.github.io/favicon.ico "favicon")

**对应 HTML：**

```html
<img src="https://chamboin.github.io/favicon.ico" alt="Alt text" title="favicon">
```

### 转义

Markdown 支持在以下字符前面插入反斜杠

```
\ 反斜线
` 反引号
* 星号
_ 底线
{} 花括号
[] 方括号
() 括弧
# 井字号
+ 加号
- 减号
. 英文句点
! 惊叹号
```

插入之后，将不再解析这些字符，而是原样输出。

### 嵌入 HTML

Markdown 的语法简洁，但有其局限性，所以特意保留了内联 HTML 这种方式。任何 HTML 标签及其内容，都会原样输出到结果中。也就是说，标签中的星号等作为 Markdown 结构的符号，以及构成 HTML 标签和实体的符号，都不会做任何转义。

对于那些没有办法用 Markdown 语法来对应的 HTML 标签，直接使用 HTML 来写就好了。

## 扩展语法

本节的内容是介绍一些受到广泛支持的 Markdown 扩展语法。

### 表格

**Markdown：**

    | 编号  | 姓名（左） | 年龄（右） | 性别（中） |
    | ----- | :--------  | ---------: | :------:   |
    | 0     | 张三       | 28         | 男         |
    | 1     | 李四       | 29         | 男         |

**预览效果：**

| 编号  | 姓名（左） | 年龄（右） | 性别（中） |
| ----- | :--------  | ---------: | :------:   |
| 0     | 张三       | 28         | 男         |
| 1     | 李四       | 29         | 男         |

**对应 HTML：**

```html
<table>
  <thead>
    <tr>
      <th>编号</th>
      <th align="left">姓名（左）</th>
      <th align="right">年龄（右）</th>
      <th align="center">性别（中）</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td align="left">张三</td>
      <td align="right">28</td>
      <td align="center">男</td>
    </tr>
    <tr>
      <td>1</td>
      <td align="left">李四</td>
      <td align="right">29</td>
      <td align="center">男</td>
    </tr>
  </tbody>
</table>
```

要注意第二行的冒号决定了对齐是居左居右还是居中，如果你不加冒号，默认是居左的。另外可以把第一行去掉，做成没有表头的表格，但第二行始终是要有的。

### 目录

*部分编辑器/解析器支持。*通过 `[TOC]` 标记来插入目录。

### 任务列表

在 GitHub / GitLab 里有较好的支持。

**Markdown：**

```
- [x] 洗碗
- [ ] 清洗油烟机
- [ ] 拖地
```

**预览效果：**

- [x] 洗碗
- [ ] 清洗油烟机
- [ ] 拖地

**对应 HTML：**

```html
<ul class="contains-task-list">
  <li class="task-list-item"><input type="checkbox" id="" disabled="" class="task-list-item-checkbox" checked=""> 洗碗</li>
  <li class="task-list-item"><input type="checkbox" id="" disabled="" class="task-list-item-checkbox"> 清洗油烟机</li>
  <li class="task-list-item"><input type="checkbox" id="" disabled="" class="task-list-item-checkbox"> 拖地</li>
</ul>
```

如果是在 GitHub / GitLab 的 Issue 里，会附赠任务完成比例提示效果：

![task list 1](https://raw.githubusercontent.com/mzlogin/markdown-intro/master/assets/task-list-1.png)

还可以直接在网页上拖动调整顺序，勾选和取消勾选。

![task list 2](https://raw.githubusercontent.com/mzlogin/markdown-intro/master/assets/task-list-2.png)

### emoji

以 GitHub Pages 为例。

**Markdown：**

```
:camel: :blush: :smile:
```

**预览效果：**

:camel: :blush: :smile:

**对应 HTML：**

```html
<p>
  <img class="emoji" title=":camel:" alt=":camel:" src="https://github.githubassets.com/images/icons/emoji/unicode/1f42b.png" height="20" width="20">
  <img class="emoji" title=":blush:" alt=":blush:" src="https://github.githubassets.com/images/icons/emoji/unicode/1f60a.png" height="20" width="20">
  <img class="emoji" title=":smile:" alt=":smile:" src="https://github.githubassets.com/images/icons/emoji/unicode/1f604.png" height="20" width="20">
</p>
```

### TeX 公式

哦吼，这又是一个大坑，LaTeX 什么的我现在还没学还不会，以后可能会学吧，写论文什么的，等更吧。

### 更多

想象力丰富的工程师们还扩展了很多基于 Markdown 的玩法，包括但不限于：

* 自动生成 / 更新 Table of Contents
* 流程图 / 时序图
* 制作幻灯片
* 集成 PlantUML / GraphViz 的能力
* 导出 HTML / PDF / 电子书
* ...

以上功能基本都可以用 VSCode + 插件 Markdown Preview Enhanced 实现。

## 参考

* [Markdown: Syntax - DARING FIREBALL](https://daringfireball.net/projects/markdown/syntax)
* [Markdown - 维基百科](https://zh.wikipedia.org/wiki/Markdown)
* [GitHub Flavored Markdown Spec](https://github.github.com/gfm/)
* [关于 Markdown 的一些奇技淫巧](https://mazhuang.org/2017/09/01/markdown-odd-skills/)
* [一份简明的 Markdown 笔记与教程](https://mazhuang.org/2018/09/06/markdown-intro/)