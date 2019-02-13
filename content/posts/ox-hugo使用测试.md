+++
title = "ox-hugo使用测试"
author = ["jcchen"]
date = 2019-02-09T20:17:00+08:00
tags = ["hugo", "ox-hugo"]
draft = false
toc = true
+++

## 使用 {#使用}


### 多文件关联 {#多文件关联}

如果是同个文件的关联,直接用 `org-mode` 内部的链接方式即可.
但如果是跨 `post` 链接,那就不行了. 必须按照生成的URL来进行链接.

如下:

```org
[[/post/org-file-header][link description]]
```


### 图片,表格,代码块 {#图片-表格-代码块}

<a id="org25bf290"></a>

{{< figure src="/images/pikachu.png" caption="Figure 1: inline image, don't click" >}}

A link to view the [image](/images/pikachu.png).

下面是一个表格的例子:

<div class="ox-hugo-table collapse striped--moon-gray">
<div></div>

<div class="table-caption">
  <span class="table-number">Table 1</span>:
  demo table
</div>

| Hugo front matter       | org                           |
|-------------------------|-------------------------------|
| title = "foo"           | \* foo                        |
| date = 2019-01-01       | :EXPORT_DATE: 2019-01-01     |
| lastmod = <currentdate> | #+hugo\_auto\_set\_lastmod: t |

</div>

一段python的代码例子

{{< highlight python "linenos=table, linenostart=1, hl_lines=2" >}}
for i in range(1,10):
    print(i)
{{< /highlight >}}

下面这段代码会把输出一起打印出来:

{{< highlight python "linenos=table, linenostart=1" >}}
str = "hello-world"
print(str[1:5])
{{< /highlight >}}

```text
ello
```

下面是一段公式:

If \\(a^2=b\\) and \\( b = 2 \\), then the solution must be either
\\[ a=+\sqrt{2} \\] or \\[ a=-\sqrt{2} \\]


### TOC {#toc}

hugo会自动带toc的功能. 这个要看主题(theme)的支持情况.
正常的话,hugo会自动从markdown中提取出标题来做为toc.如果主题支持的话,那这个toc会自动显示出来.
比如,目前用的主题 [ananke](https://themes.gohugo.io/gohugo-theme-ananke/),使用时,必须在front matter中显示指定 `toc: true` 才能打开.
因此,我们在 `org-mode` 中只需要加一个自定义的变量,把这个front matter给设上即可. 如下:

```org
:EXPORT_HUGO_CUSTOM_FRONT_MATTER: :toc true
```

正因为hugo自带toc功能,因此,ox-hugo默认是没有启用org-mode的toc导出功能.

hugo的toc功能有什么不足,就是目前是没法指定toc的层级,它会全部展示出来.

如果你想使用org-mode的toc功能,则需要设置:

-   单文件,一个subtree对应一个post:  `:EXPORT_OPTIONS: toc:t`
-   单文件,一个文件对应一个post:     `#+toc: headlines 2`

org-mode的toc功能就可以设置层级.它会生成一段html到文件的开头中去.
它会有一个问题就是,hugo的内置变量 `.Summary` 取到的值,会把这个org-mode生成的toc取到.

所以,要做的好的话,需要在theme里面对这个做一个额外的处理.
另外,orgmode生成的toc的样式,通常theme也不会有样式支持,毕竟不是hugo内置的功能,需要自己写样式美化一下.


### 标签,分类 {#标签-分类}

标签就是 `org-mode` 正常的打tag即可.另外, `org-mode` 的tag不能用连接线. 因此, `ox-hugo` 会将
标签名字中的:

-   一个下划线 转成 连接线. 如 abc\_def ==> abc-def
-   两个下划线 转成 空格.  如 abc\_\_def ==> abc def
-   三个下划线 转成 一个正常的下划线. 如 abc\_\_\_def ==> abc\_def

如果,不想要这种转义,可以设置: `org-hugo-prefer-hyphen-in-tags` 为 `nil`.

分类:
tag的名字,如果以"@"开头,就认为是分类.


### 自定义css {#自定义css}


### 自定义html文件 {#自定义html文件}


### <span class="org-todo todo TODO">TODO</span> override theme to make `mathjax` support {#override-theme-to-make-mathjax-support}
