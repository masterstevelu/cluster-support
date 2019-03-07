# 登录方法

集群操作系统为CentOS Linux操作系统，需要使用可以SSH远程登录的软件来登录集群。

当用户从集群管理员获取到用户名和密码后，使用登录软件，连接登录节点rmdx-cluster（183.174.229.251）。

登录节点后，用户默认将进入账户对应的家目录（home目录），对于某个用户，其家目录为`/home/your-cluster-username/`一般情况下，用户的个人数据都应存放在个人家目录中，包括代码、可执行文件和部分数据。

## Windows用户

Windows下的登录软件有 XShell 、SecureCRT、 [Putty][0] 和 [MobaXterm][4] ，其中 SecureCRT 是付费商业软件，功能强大，简单易用，用户众多，因此也有很多破解版本。Putty 是免费软件，但功能相对简单。[文章][2]列举了使用上述几种软件连接集群的具体使用方法。

我们不建议使用盗版破解的软件，有些别有用心的人会修改软件，在其中植入木马，窃取你的用户名密码等信息。

我们推荐使用 MobaXterm ，这是一款功能强大的客户端，集成了命令行和文件上传等功能，并且提供官方免费版，足以覆盖日常需求 。此外，Xshell 和 Xftp 有[家庭学生免费版][7]，也是非常不错的选择。

用户需要从本地上传代码或数据时，Windows用户可使用 [Winscp][3] 或 xftp 等支持文件传输的软件。MobaXterm 已经集成了文件上传的功能(sftp)，已经安装 MobaXterm 的用户可不必再安装 Winscp。

为方便校内用户下载，我们提供了校内的下载链接。

* [MobaXterm](files/MobaXterm_Portable_v11.1.zip)
* [Xshell](files/Xshell-6.0.0118p.exe)
* [Xftp](files/Xftp-6.0.0111p.exe)

## Linux和Mac用户

Mac OS X下建议使用 [iTerm][1] ，Ubuntu 等 Linux 系统可以使用自带的终端软件。

用户可以直接终端中使用 `ssh` 和 `scp` 或 `rsync` 命令来登录集群和上传文件。

```bash
ssh yourusername@183.174.229.251
```

scp可以将数据同步到集群，也可以从远程复制到本地。命令的基本模式为：

```bash
scp [source_location] [destination]
```

将本地当前文件夹下 README.md 上传到集群 `/home/~your-cluster-username~/examples/` 位置下：

```bash
scp README.md ~your-cluster-username~@183.174.229.251:/home/~your-cluster-username~/examples/
```

将集群 `/home/~your-cluster-username~/examples/` 同步到本地当前目录：

```bash
scp -r ~your-cluster-username~@183.174.229.251:/home/~your-cluster-username~/examples/ ./
```

更多`scp` 命令使用方法可参考[文章][6]。

`rsync` 是一个比 `scp` 更快的命令，比起 `scp` 将整个文件进行传输，它是增量传输文件，即只传输文件中被修改的部分。`rsync` 可被用来文件夹之间以及机器之间同步数据。

```bash
rsync -r README.md linpack@183.174.229.251:/home/~your-cluster-username~/examples
```

!!! tip "文件夹同步"
    `scp` 和 `rsync` 的 "-r" 模式可以同步整个文件夹。

### 免密码登录

本节仅针对Linxu和Mac用户。

每次使用`ssh` 和 `scp` 命令时都需要用户输入密码，对于频繁操作来说有些麻烦，好在ssh协议提供了免密码登录的功能。ssh免密码登录需要一对密钥对，其中一个私钥放在用户本机，一个公钥放在集群的公钥目录：`~/.ssh/authorized_keys`。下次登录时，用户本机的私钥和远程集群的公钥通过加密协议的配对，验证成功后将不需要密码直接登录成功。所以这里需要生成公私钥，并将公钥上传到集群指定位置。

1. 生成公私钥
2. 将公钥上传到集群
3. 登录集群 将集群上的公钥添加到指定文件上

在本机生成公钥和私钥的命令为

```bash
ssh-keygen -t rsa
```

这时终端会提示

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/~your-local-username~/.ssh/id_rsa):
```

括号内为生成的公私钥的默认目录位置，直接回车就会使用这个默认位置。如果默认位置已经生成过公私钥，则终端会提示是否需要覆盖，这时可不用再次生成公私钥。

```bash
/Users/~your-local-username~/.ssh/id_rsa already exists.
Overwrite (y/n)?
```

终端会提示输入密码 passphrase，这个密码为生成私钥的密码，将来防止私钥被其他人盗用。这里可以不输入任何密码，直接回车，再次提示输入密码，再次回车。

这时公钥存储在 `/Users/~your-local-username~/.ssh/id_rsa.pub` 文件里，私钥存储在 `/Users/~your-local-username~/.ssh/id_rsa` 文件里。

生成好公私钥后，接下来需要我们将公钥上传到集群，这里使用 `scp` 命令，并将公钥追加到 `~/.ssh/authorized_keys`。

```bash
scp /Users/~your-local-username~/.ssh/id_rsa.pub ~your-cluster-username~@183.174.229.251:/home/~your-cluster-username~/id_rsa.pub
```

公钥上传到集群后，使用ssh命令登录到集群，执行命令将公钥添加到指定文件上。

```
cat /home/~your-cluster-username~/id_rsa.pub >> ~/.ssh/authorized_keys
```

接下来就可以免密码登录了。

[0]: https://putty.org/
[1]: https://www.iterm2.com/
[2]: https://blog.csdn.net/ValDC_Morning/article/details/79051615
[3]: https://winscp.net/eng/index.php
[4]: https://mobaxterm.mobatek.net/
[5]: https://winscp.net/eng/docs/introduction
[6]: http://www.runoob.com/linux/linux-comm-scp.html
[7]: https://www.netsarang.com/zh/free-for-home-school/
