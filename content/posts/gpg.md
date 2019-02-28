+++
title = "gpg使用参考"
author = ["jcchen"]
date = 2019-02-28
publishDate = 2019-02-28
lastmod = 2019-02-28T15:50:43+08:00
tags = ["pgp"]
draft = false
toc = true
+++

PGP (Pretty Good Privacy) 是由 Phil Zimmermann （之所以提这个名字是因为这位仁兄背后有一段故事，有兴趣可以找找他的八卦）于 1991 开发的一个用于数据加密和数字签名的程序，由于被广泛应用，以至于后来形成一个开放的标准 OpenPGP，这个标准也是目前最为常用的邮件加密和签名的方式。而 GnuPG 则是实现了该标准的一个开源免费程序.


## 加密和数字签名的简单原理 {#加密和数字签名的简单原理}

首先每位用户都需要用 GnuPG 程序生成地球上惟一的一对钥匙，分别称为公钥和私钥（即 public key 和 private/secret key）。公钥用于加密、私钥用于解密。使用公钥加密过的信息只能由配对的私钥解开。这种加密方式叫做非对称加密。

非对称加密的过程是：如果A君要发送信息给B君，首先B君得把自己的公钥扔出来，A君得获取B君的公钥后用B君的公钥加密信息并发送过去，B君收到（加过密的）信息使用自己的私钥解密就可以还原信息了。

在数字签名时，私钥用于签名、公钥用于核对签名。

数字签名的过程是：如果A君要发送信息给B君，信息首先会被计算出一个指纹值（比如MD5值），然后A君使用自己的私钥加密信息和这个指纹值（这个过程称为签名）并发送出去，接受者B君收到信息之后使用A君的公钥来解密并重新核对一次信息的指纹值，这样B君就可以核对信息有无被篡改以及是否真的为A君所发出的了。


## 下载 {#下载}

-   Linux  自带的包管理工具都能下到.
-   Windows  [GPG4Win](http://www.gpg4win.org/)
-   OSX  [GPGTools](https://gpgtools.org/)


## 使用图形化工具 {#使用图形化工具}

在不同的平台下所使用的管理工具各不相同，幸运的是他们的功能大同小异，而且界面简单，相信只要你一运行则不用看教程都会使用。

1、生成钥匙对。一般点击“文件”菜单就会找到“新建PGP钥匙”的选项，跟着提示输入你的名字（建议使用你常用的网名）、email地址以及备注（可以不填）就会生成一个属于你自己的钥匙对。期间会提示你输入一个密码，这个密码是用于保护你的私钥，建议使用比较复杂的密码。

2、发布你的公钥。因为你的朋友要发送加密信息给你则必须要先获取你的公钥，所以，作为一个有礼貌的 GnuPG 用户应该先发为敬。Internet上有很多免费的公钥服务器，你可以把你的公钥发布到随便一个服务器，它们之间会自动同步（最长可能需要几小时或者几天）。在管理工具里选定你的钥匙对，然后选择类似“发布”或者“远程-同步”之类的选项即可。在发布的时候工具可能会询问你使用哪个公钥服务器，正如刚才所说的，随便选一个就可以了。

3、获取你朋友的公钥（或者让朋友获取你的公钥）。在管理工具里选择类似“搜索”或者“远程-查找钥匙”，在搜索框里填写对方的email地址或者生成钥匙时所填写的名字，然后点击搜索就可以找到对方的公钥。作为练习，你可以搜索自己的公钥，如果搜索没有结果则可能时因为服务器还没同步，可以过几分钟再试试。或者搜索我的公钥，我公钥的 email 地址为 ivarptr@126.com。点击选中搜索结果，然后再选“导入”即完成。

4、签收（sign）公钥。为了安全起见，获取你朋友的公钥之后，还需要跟对方核对一下这个公钥是否真的为他所拥有（因为可能存在假冒的情况）。核对的方法是选中你朋友的公钥，再选择类似“属性”或者“详细信息”的选项，其中你可以看到这个钥匙的指纹值，你可以通过电话、聊天工具或者email跟你的朋友核对一下，如果指纹值相同，则点击“签收这个钥匙（sign this key）”，这样就表示你正式可用这个公钥了。如果上一步你导入了我的公钥，则看看指纹值是否为“0D24 0A94 A86C 7B83 3F7C 200E 1CAF 0CB0 72E7 5B05“，这个是我钥匙的指纹值，无误的话就可以点击签收了。（补充：为了避免跟“数字签名”这个名词相冲突，这里我把“sign”书写为“签收”，在一些教程里可能会翻译为“签名钥匙”）

好了，下面我们就可以开始邮件加密和数字签名噜。


## 邮件加密和数字签名 {#邮件加密和数字签名}

实现邮件加密和数字签名一般需要使用邮件客户端软件，现在一般比较完善的邮件客户端工具都内置集成 OpenPGP 的功能，即本身就可以无缝地使用 GnuPG。比如 Evolution、KMail、Mozilla Thunderbird，对于 Windows 和 Mac OSX 系统，我比较推荐 Thunderbird，这也是一个开源免费程序。谷歌告诉我 Windows 下的 Outlook 和 Live Mail 也支持但我没有试验过，如果不行又懒得下载 Thunderbird，那么使用 GPG4Win 附带的 Claws Mail 也是可以的。因为各个邮件客户端的设置大同小异，下面我以 Evolution 为例简单介绍如何设置。

其实关键的设置只有一步，就是关联你的 email 帐号和你的钥匙id。在 Evolution 的帐号属性里，转到 Security 页，在 OpenPGP Key Id 里输入你的钥匙的id（即生成钥匙对时所填写的名字）或者email地址即可。如下图：

evolution-pgp-setting

[图2] Evolution 的邮件帐号设置窗口

设置就这么简单。然后在每次写 email 时点击菜单的 Options 勾选 OpenPGP 签名或者/以及加密，然后再发送即可。如下图：

evolution-pgp-write

[图3] Evolution 的书写邮件窗口

对方收到邮件时，Evolution 会自动检验签名或者自动解密邮件内容。需要注意的是，再发送加密邮件给你的朋友之前，你要先获取他的公钥（见本文第2节）。


## GnuPG {#gnupg}

前面说过,GnuPG是一个命令行工具,下面介绍它的简单使用.


### 生成密钥对. {#生成密钥对-dot}

```bash
gpg --gen-key
```

一路默认下去即可.然后是输入"Real name","Email address","Comment".
输完,确认Ok.依照提示,输入"O". 完成. 等待系统给你生成密钥对就好了.


### 查看本机钥匙信息 {#查看本机钥匙信息}

查看本机上的公钥.

```bash
gpg --list-keys
```

输出结果如下：

```text
/home/ivarptr/.gnupg/pubring.gpg

pub 2048R/72E75B05 2013-04-17 [expires: 2015-04-17]
uid ivarptr (ivarptr on Twitter) <ivarptr@126.com>
sub 2048R/74F0F5F9 2013-04-17 [expires: 2015-04-17]
```

   从中我们可以看到刚才生成的钥匙对的公钥部分，其中：
   72E75B05:是这个公钥的id，这个id跟uid都是用于标识这个公钥的，因为uid是用户随便输入的所以会有重复的情况，因此在某些需要明确指定公钥的命令，需要用id而不能用uid表示这个公钥。另外在使用这个id表示一个钥匙时，记得在前面加上”0x”，是零和字母XYZ的X，比如”0x72E75B05″
2013-04-17:是生成这个公钥的时间，后面括号内的是有效期。

在实际应用中，因为要发送加密信息给你的朋友就需要对方的公钥，所以在本机上可能会有多个朋友的公钥，但私钥往往只有一个，也就是自己的私钥。使用下面命令可以查看本机上的私钥。

```bash
gpg list-secret-keys
```


### 导出公钥 {#导出公钥}

为了将自己的公钥扔给他人，你需要把公钥导出成为一个文件：

```bash
gpg -a --output key.public --export UID
```

注：你要把其中的 UID 替换成你的名字或者email地址。

其中参数:

-   -a 表示输出文本文件格式。默认输出是二进制格式，因为二进制格式不太方便在网络（比如论坛或者博客）上展示，所以推荐文本格式。
-   output 指定输出文件的名字，你可以更改为其他名字。
-   export 表示执行输出公钥操作，后面的 UID 为你要输出的公钥的标识。

运行之后会在当前文件夹得到一个 key.public 文件，你可以使用文本编辑器或者 cat 命令查看里面的内容： `cat key.public`

公钥导出之后，你可以通过各种方式把它发送给你的朋友，比如email或者聊天工具。而比较方便的是把公钥发布到公钥服务器。


### 把公钥发布到公钥服务器 {#把公钥发布到公钥服务器}

公钥服务器用于储存和发布用户的公钥以便相互交流，这些服务都是免费的，GnuPG 默认的公钥服务器是 keys.gnupg.net，你可以在 这里 找到更多的服务器，一般来说不用指定公钥服务器，随便发布到一个软件默认的就可以了，因为公钥服务器之间会自动同步。你也可以使用浏览器打开它们的网站，然后把你的公钥复制粘贴上去。当然最直接的是通过命令行：

```shell
gpg --keyserver keys.gnupg.net --send-key ID
```

注：你要把其中的 ID 替换成你公钥的id。

其中参数：

-   keyserver 用于指定公钥服务器，没有特殊需求的话是可以省略这个参数的，不过有些 GnuPG 版本可能需要指定这个参数才能正常工作。
-   send-key 用于指定待发布的公钥的id。

好了，至此你已经学会生成钥匙对以及发布公钥了。


### 导入其他人的公钥到本机（可选） {#导入其他人的公钥到本机-可选}


#### 搜索/导入 {#搜索-导入}

使用如下的命令可以搜索到我的公钥：

```bash
gpg --keyserver keys.gnupg.net --search-key uid_or_emails
```

参数 search-key 用于指定搜索关键字，可以是uid的名字或者email地址部分。

如果你确切知道要导入的公钥的id，也可以跳过搜索这步而直接使用如下的命令导入:

```bash
gpg --keyserver keys.gnupg.net --recv-key 0x72E75B05
```

其中 0x72E75B05 是我公钥的id，如果把它替换成你朋友的公钥id则为导入你朋友的公钥。

导入公钥还有另一种方法：如果我把公钥文件直接发送了给你，你也可以跳过公钥服务器。下面假设我的公钥文件 key.public 已发送给你，你运行如下命令就可以导入我的公钥了：

```bash
gpg --import key.public
```


#### 核对公钥指纹 {#核对公钥指纹}

把对方的公钥导入到本机后，就已经可以用它来加密信息或者用于校验我的数字签名。不过这样每次操作时都会提示公钥不可信，因为虽然你导入了我的公钥，但存在导入冒充者的公钥的可能性。所以你需要进一步跟我核对公钥是否正确，然后签收（sign key）它。

因为公钥有可能出现冒牌货，所以每个公钥里都加入了指纹值，使用下面命令可以查看指纹值：

```bash
gpg --fingerprint
```


#### 签收公钥 {#签收公钥}

确定取得的公钥是真货之后，使用如下命令对这个公钥进行签收（sign key）：

```bash
gpg --sign-key ivarptr
```

运行上面的命令之后它提示你再确认一次指纹值，输入y并按回车确定。

提示，如果发现获取回来的是冒牌货，可以使用如下的命令删除它：

```bash
gpg --delete-keys ivarptr
```


### 使用命令行加密文件 {#使用命令行加密文件}

使用文本编辑器（比如记事本或者 vim、echo）创建一个名为 message.txt 的文件，里面写上任意一行文字，然后使用如下的命令加密它：

```bash
gpg -a --output message-ciper.txt -r ivarptr@126.com -e message.txt
```

其中参数：

-   -a 表示输出文本文件格式。
-   output 指定输出（即加密后）的文件名。
-   -r 指定信息的接收者（recipient）公钥的uid，可以是名字也可以是email地址。
-   -e 表示这次要执行的是加密（encrypt）操作。

执行完毕之后会在当前文件夹产生文件 message-ciper.txt，这个就是被加密之后的文件。

注：如果你要加密的是一个二进制文件，则可以省略 -a 参数，这样产生的加密文件的体积会小一些。


### 使用命令行解密文件 {#使用命令行解密文件}

```bash
gpg --output message-plain.txt -d message-ciper.txt
```

其中参数：

-   output 指定输出（即解密后）的文件名。
-   -d 表示这次要执行的是解密（decrypt）操作。

GnuPG 程序会自动使用我的私钥来解密信息，最后得到一个跟原始内容一样的文本文件 message-plain.txt。


### 使用命令行对文件进行数字签名 {#使用命令行对文件进行数字签名}


#### 方法1:生成单独的签名文件 {#方法1-生成单独的签名文件}

现在我有一个文本文件 message.txt，使用如下命令即可对它进行数字签名：

```bash
gpg -a -b message.txt
```

其中参数

-   -a 表示输出文本文件格式。
-   -b 表示以生成独立的签名文件的方式进行签名。

命令执行完毕之后，会在当前文件夹里产生一个 message.txt.asc 的文件，这个文件即签名。现在我应该把原信息文件 message.txt 连同签名文件 message.txt.asc 一起寄给你，然后你使用如下命令检验：

```bash
gpg --verify message.txt.asc
```


#### 方法2:内容和签名在同一个文件 {#方法2-内容和签名在同一个文件}

如果不想生成一个独立的签名文件，则还可以用如下的命令进行签名：

```bash
gpg -a --clearsign message.txt
```

跟方法A不同的地方是用参数 clearsign 替代了参数 -b。参数 clearsign 表示将签名和原信息合并在一起，并生成一个新文件。


### 编辑key {#编辑key}

```bash
gpg --edit-key <user-id>
```

会弹出如下的菜单:

```text
> passwd       # change the passphrase
> clean        # compact any user ID that is no longer usable (e.g revoked or expired)
> revkey       # revoke a key
> addkey       # add a subkey to this key
> expire       # change the key expiration time
```


### Export subkey {#export-subkey}

如果想在多个设备上用同一个Key. 那么需要把key导出来，再导到其他的机器上。

1.  找出你先导的Key  `gpg -K`
2.  只导出其中的一个. `gpg -a --export-secret-subkeys [subkey id]! > /tmp/subkey.gpg`
    注意不要漏了 **!** 号,要不然,会把所有的Key全导出来.

事实上,这样就可以了. 当然,如果你想改一个这个Key的密码的话,那你应该先导到一个临时的Key文件,再重新导出:如下:

```bash
$ gpg --homedir /tmp/gpg --import /tmp/subkey.gpg
$ gpg --homedir /tmp/gpg --edit-key <user-id>
> passwd
> save
$ gpg --homedir /tmp/gpg -a --export-secret-subkeys [subkey id]! > /tmp/subkey.altpass.gpg
```

这样,你就可以把 /tmp/subkey.altpass.gpg 拿到其他机器上使用了.


### 保存密钥 {#保存密钥}

备份:

```bash
gpg -o keyfile.pub --export "your@email.address"
gpg -o keyfile.prv --export-secret-keys "your@email.address"
```

恢复:

```bash
gpg --import keyfile.pub
gpg --import keyfile.prv
```

[参考链接](https://www.jianshu.com/p/bd4266fb4551).


## 参考链接 {#参考链接}

<http://ju.outofmemory.cn/entry/62256>
<http://juke.outofmemory.cn/entry/62257>
<https://wiki.archlinux.org/index.php/GnuPG%5F(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)>
