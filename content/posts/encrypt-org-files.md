+++
title = "Encrypt org files"
author = ["jcchen"]
date = 2019-02-28
publishDate = 2019-02-28
lastmod = 2019-02-28T15:50:39+08:00
tags = ["emacs", "org-mode", "gpg"]
draft = false
toc = "truef"
+++

## 使用Gpg对整个文件加密 {#使用gpg对整个文件加密}


### 概念 {#概念}


#### GPG,OpenGPG,GnuPG {#gpg-opengpg-gnupg}

Gpg(Pretty Good Privacy)是一个数据加密和数字签名的程序.
由于其广泛应用,后来形成一个开放的标准 OpenGPG.
GnuPG是实现了该标准的一个免费开源程序.


#### EasyPG {#easypg}

EasyPG is an all-in-one GnuPG interface for Emacs. It has two aspects: convenient tools which allow to use GnuPG from Emacs (EasyPG Assistant), and a fully functional interface library to GnuPG (EasyPG Library).

-   epa-file:


### 配置 {#配置}

参考链接:

1.  [org-mode document for encrypting files](https://orgmode.org/worg/org-tutorials/encrypting-files.html)
2.


### 约束 {#约束}

emacs编辑 `.gpg` 后缀的文件,保存时,会自动使用 `gpg` 对文件进行加密.当然,加密时,会提示你输入密钥的密码.
同样的,当你用emacs打开 gpg 后缀的文件时,也会提示你输入密码,并自动帮你把文件解密开来.


### 测试 {#测试}


#### 最简单的测试 {#最简单的测试}

1.  选中一小段文本
2.  `M-x epa-sign-region`

看能不能对这段文本进行正确的签名.


#### 文件加密测试 {#文件加密测试}

`C-x C-f` 新建一个文件,以 `gpg` 为后缀,任意输入一些文件后,保存.
配置正确的话,emacs会提示你输入密码,并对文件内容进行加密保存.


## 只加密Org文件某个层级 {#只加密org文件某个层级}


### 约束 {#约束}

org文件中,对于 tag 为 "crypt" 的层级,在保存时,默认会对该层级的内容进行加密.
当你想查看加密内容时, `M-x org-decrypt-entry`.


### 配置 {#配置}

```elisp
(require 'org-crypt)

;; 當被加密的部份要存入硬碟時，自動加密回去
(org-crypt-use-before-save-magic)

;; 默认加密的 tag 是 "crypt", 可以使用下面的设置,修改它.
;;; (setq org-crypt-tag-matcher "secret")

;; 避免加密的tag被子项目继承,造成子项目重复加密;
(setq org-tags-exclude-from-inheritance (quote ("crypt")))

;; GPG key to use for encryption
;; 设置加密时,要使用的 GPG key. 设置为nil时,会按系统提示进行选择.
;; 也可以直接设置相应的 key id. 通常是"邮箱地址".
(setq org-crypt-key nil)

```


### 问题 {#问题}


#### 密码提示框in minibuffer {#密码提示框in-minibuffer}

-   org-crypt-key为nil时,提示选择Key
-   org-decrypt-entry提示输入key的密码

上面这两种情况,提示都不会通过 minibuffer 提示. 这是个问题.

目前,可以通过 `M-x epa-sign-region` 先调用一下,这样会输入一遍密码.
密码记住之后,再调用 `org-decrypt-entry` 时,就不会再提示输入密码了.


## 一些问题 {#一些问题}


### (method 1)gpg password entry in minibuffer {#method-1--gpg-password-entry-in-minibuffer}

emacs配置使用EasyPG来加密文件的时候,当会弹出X窗口,来提示输入加密密钥的密码.
这样用起来很不方便.如何才能直接在emacs的minibuffer中,直接输入密码呢?

当我们输入GnuPG密码时,弹出的图形窗口(X窗口),纯终端下可能是curse窗口,称作 `pinentry`.

下面讲如何设置.


#### 设置 gpg-agent {#设置-gpg-agent}

设定: `~/.gnupg/gpg-agent.conf`. 内容如下:

```conf
# Emacs support
allow-emacs-pinentry
allow-loopback-pinentry

# (optional) if you want to set timeout (second)
pinentry-timeout 3
```

完成后,需要通过 `gpgconf` 重新加载 `gpg-agent`.

```shell
$ gpgconf --reload gpg-agent
```


#### 设置 emacs {#设置-emacs}

把下面的配置加入你的 emacs 配置中.

```elisp
;; GnuPG 2.1 or later has an option to control the behavior of
;; Pinentry invocation.  When set this to `loopback', which redirects
;; all Pinentry queries to the caller, so Emacs can query passphrase
;; through the minibuffer instead of external Pinentry program.

(require 'epa)
(setq epa-pinentry-mode 'loopback)

(use-package pinentry
  :ensure t
  :config
  (pinentry-start))
```


#### 结论 {#结论}

设置完成后,

1.  [测试](#测试)?
2.  出现错误 [error in process filter: epg--status-GET\_HIDDEN: Process epg not running](#error-in-process-filter-epg-status-get-hidden-process-epg-not-running) ?
3.  [参考链接](https://coldnew.github.io/e7fdea95/)
4.  org-crypt 时,密码提示仍然不是通过minibuffer.不知道是为什么?
    至少在docker环境中测试是这样子.
    非 docker 环境呢? osx下? Linux下?


### (method 2)gpg password entry in minibuffer {#method-2--gpg-password-entry-in-minibuffer}

method 1设置的时候,在docker环境下,对org-crypt的设置不起作用,需要输入密码时,还是不能通过minibuffer来交互.
这个需要验证.

method 2目前在这种环境下测试,是正常工作的. 需要用到 [pinentry-emacs](https://github.com/ecraven/pinentry-emacs).


#### 下载 `pinentry-emacs` 到本地 {#下载-pinentry-emacs-到本地}

假设,下载到 `~/.emacs.d/pinentry-emacs` 目录下.

```shell
git clone https://github.com/ecraven/pinentry-emacs.git ~/.emacs.d/pinentry-emacs
```


#### 设置 gpg-agent {#设置-gpg-agent}

设定: `~/.gnupg/gpg-agent.conf`. 内容如下:

```conf
pinentry-program ~/.emacs.d/pinentry-emacs/pinentry-emacs
```

pinentry-program 指向刚刚下载的"pinentry-emacs"执行脚本的位置.
同时保证脚本"pinentry-emacs"是可执行的.


#### 设置 emacs {#设置-emacs}

把一面这一段配置,加到 emacs 的启动脚本中去:

```elisp
(defun pinentry-emacs (desc prompt ok error)
  (let ((str (read-passwd (concat (replace-regexp-in-string "%22" "\"" (replace-regexp-in-string "%0A" "\n" desc)) prompt ": "))))
    str))
```


#### 结论 {#结论}

目前,在docker环境下,这个测试是能工作Ok的.

如果在 `gpg-agent.conf` 中加了那个设置后,如果你调用 `gpg --import` 或者 `gpg --gen-key` 可能会报错.
因为,系统可能找不到 pinentry,或者,弹出提示密码时,会跑到emacs中去.
因此,如果加了这个设置,遇到这个问题,可以看下是不是要到emacs中去输入密码.

对于import,可以加一个参数 `pinentry-mode` 参数,如下:

`gpg --pinentry-mode loopback --import some_key_file`

这样,则,密码提示,会在当前shell环境下进行提示.

实在不行的时候,你也可以考虑把"gpg-agent.conf"中的配置注释掉,再重启下"gpg-agent".
操作完成后,再把那一行加回去.

如何重启?

`gpg-connect-agent reloadagent /bye`

[参考链接](https://emacs.stackexchange.com/questions/20824/how-to-use-minibuffer-instead-of-pop-up-window-for-gpg-files)


### error in process filter: epg--status-GET\_HIDDEN: Process epg not running {#error-in-process-filter-epg-status-get-hidden-process-epg-not-running}

```elisp
(setq epg-gpg-minimum-version "100")
```

[参考解决办法](http://emacs.1067599.n8.nabble.com/23-0-60-EasyPG-and-OpenPGP-smartcard-process-epg-not-running-td181029.html)


### docker环境下测试 {#docker环境下测试}

docker环境下测试,由于terminal也不见了,所以如果配置不正确,默认会弹出输入密码的提示框,而docker环境下弹不出来.
这种情况下,emacs只是提示加密失败,或者解密失败.

在这种情况下,最好是通过 `docker exec -it <container-id> bash` 进入到docker环境下的终端,然后再 `emacs` 启动.
这样,启动完成后,至少终端还在,如果密码在终端下提示,还可以看到.


## 一些参考文档 {#一些参考文档}

-   [emacs 26 gpg 变化](https://github.com/emacs-mirror/emacs/blob/7d9fa89fb3f6db0bdc3960bbbf6c0cf34c98d1ca/etc/NEWS.26#L1422)
-   [emacs 27 gpg 变化](https://github.com/emacs-mirror/emacs/blob/7d9fa89fb3f6db0bdc3960bbbf6c0cf34c98d1ca/etc/NEWS#L910)
