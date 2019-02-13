+++
title = "ox-hugo"
author = ["jcchen"]
date = 2019-02-08T13:00:00+08:00
tags = ["hugo", "ox-hugo"]
draft = false
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">Table of Contents</div>

- [Features](#features)
    - [org capture setup](#org-capture-setup)
    - [auto export](#auto-export)
    - [ox-hugo使用测试](#ox-hugo使用测试)

</div>
<!--endtoc-->


## Features {#features}

用 `org-mode` 完整写完后,再 `export` 成 `hugo` markdown.


### org capture setup {#org-capture-setup}

TO BE IMPLEMENT


### auto export {#auto-export}

主要是开启 `(org-hugo-auto-export-mode)`.
然后保存的时候会自动export.

可以在文件上加本地变量来开启,如下:

```org
# Local variables:
# eval: (org-hugo-auto-export-mode);
# End:
```


### ox-hugo使用测试 {#ox-hugo使用测试}

[跳转](/posts/ox-hugo使用测试)
