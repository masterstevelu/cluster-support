# 文件管理

## 上传数据

### Windows

用户需要从本地上传代码或数据时，Windows用户先在[软件下载页面](cluster-login.md)下载 Xftp 或 MobaXterm。

[文章][1]展示了如何使用xftp来上传数据文件，这里要注意，IP为183.174.229.251，密码为我们提供的密码。

[文章][2]展示了如何使用MobaXterm来上传数据，IP为183.174.229.251，密码为我们提供的密码。

[1]: https://jingyan.baidu.com/article/19192ad820877be53e5707e3.html
[2]: https://jingyan.baidu.com/article/86f4a73e98c37137d65269ce.html

### Mac

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
rsync -r README.md ~your-cluster-username~@183.174.229.251:/home/~your-cluster-username~/examples
```

!!! tip "文件夹同步"
    `scp` 和 `rsync` 的 "-r" 模式可以同步整个文件夹。

## 数据管理

用户登录后将进入账户对应的家目录（home），对于某个用户，其家目录为`/home/your-cluster-username/`一般情况下，用户的个人数据都应存放在个人家目录中，包括安装的软件、代码、可执行文件和部分数据。家目录所的可用空间较小，较大的规模的数据或软件必须放置在 `/mnt/data/` 目录下，该目录可用容量为150TB。例如，用户需要创建自己的目录：

```bash
mkdir /mnt/data/your-cluster-name
```

然后将科研所需数据和软件拷贝到这个目录，拷贝时可以使用 `mv` 命令。

鉴于存储空间有限，出于数据安全的考虑，请用户务必及时备份和下载代码数据和计算结果，并定期清理存储空间。长时间不使用的用户和目录，我们将进行清理。

!!! warning "注意！"
    对于长期不使用且占用较大存储资源的文件，我们可能会删除。如不需要使用该文件，请及时清理你自己的磁盘空间。

## 下载网络上的文件

登录到集群后，使用`wget`命令下载网上的数据集或代码到当前目录，例如：

```bash
wget https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data
```

## 编辑文件

Linux 上一般使用 vi/vim 这个编辑器来编辑文件。所有的类Unix系统如 Mac OS、Ubuntu、CentOS等都会内置 vi，如果经常使用 Linux ，强烈建议学习好这个编辑器。网络上有大量的相关资料和教学视频，这里不再赘述。

如果觉得 vim 上手比较慢，也可以先使用 `nano` 来编辑文件。