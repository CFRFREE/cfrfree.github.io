---
title: wsl+vscode搭建Latex写作环境
mathjax: true
date: 2023-02-20 15:53:35
categories: Latex
description: 费劲折腾好了，在这里记录一下过程以及注意点，万一以后又忘了QAQ
---

## 在WSL下进行Latex写作的优点

和在Windows系统下相比，Linux系统的文件读写速度更快，这能使Latex的编译速度明显提升，而在WSL下进行操作又免去了安装双系统或是VM虚拟机的麻烦，同时可以很便捷地与Windows互传文件，因此才有了本文带来的wsl+vscode搭建Latex写作环境教程。

## WSL安装

WSL，准确来说是WSL2的安装难度不大，找篇博客一般都能解决，要注意的是不要把默认用户设成root，虽然方便一些但后续步骤会和网上的教程有不少出入。

安装好后可以先进行国内软件源的替换，记得完成后更新和升级。

## Latex安装

这里我们选择安装Texlive，建议在Windows环境下先下载ios镜像，再以挂载硬盘的方式安装到WSL中，而不是使用apt等工具一键安装（因为版本太老）或是在WSL下直接进行镜像下载（因为WSL下载速度比较慢）。

下载好后先进行如下安装，以减少可能出现的字体问题：

```
sudo apt install fontconfig
sudo apt install ttf-mscorefonts-installer
```

之后将Windows系统中的iso文件挂载，这里我们以挂载到E盘为例，执行如下操作：

```
sudo mkdir /mnt/E
sudo mount -t drvfs E: /mnt/E
```

如此我们便可以在WSL中进入下载好的iso文件当中，可以看到`install-lt`这个安装脚本，使用下面的操作运行，WSL系统便可以进行离线的Latex安装。

```
sudo /mnt/x/install-tl
```

接着屏幕会弹出一堆信息，默认安装的话点击`I`键就可以。

安装完成后我们需要使用如下命令更新环境变量：

```
vim ~/.bashrc
```

打开文件后在末尾添加如下信息：

```
export PATH=/usr/local/texlive/2022/bin/x86_64-linux:$PATH
export MANPATH=/usr/local/texlive/2022/texmf-dist/doc/man:$MANPATH
export INFOPATH=/usr/local/texlive/2022/texmf-dist/doc/info:$INFOPATH
```

这时我们保存并退出，输入`tex -v`就可以看到Latex的相关信息了。

## 字体问题处理

安装完成后我们开始处理字体问题，先使用如下命令将配置文件复制到系统。

```
sudo cp /usr/local/texlive/2022/texmf-var/fonts/conf/texlive-fontconfig.conf /etc/fonts/conf.d/09-texlive.conf
```

然后执行`sudo fc-cache -fsv`刷新字体缓存。

如果之后编译中发现有字体文件找不到，字体名称以FandoKai为例，我们先在Windows寻找或下载好需要的字体文件，拷贝到`/usr/share/fonts/truetype/windows-font`下（这里的`windows-font`是按自己喜好新建的一个文件夹）

之后使用`sudo chmod -R 777 /usr/share/fonts/truetype/windows-font`赋予文件权限。

接着使用如下三条命令刷新系统的字体：

```
sudo mkfontscale
sudo mkfontdir
sudo fc-cache -fv
```

## Vscode的安装

在WSL中输入`code .`就可以安装并使用Vscode打开当前文件夹。

接着我们在WSL中安装LaTeX Workshop差距，配置文件和Windows里一样，可以直接使用。

如果需要编译的文件是从Windows里直接拷贝过来的，那么这个文件会被WSL认为是root用户创建的，从而导致Vscode无法进行操作，这时我们需要使用`sudo chmod -R 777 *`来调整文件权限。

## Tlmbgr操作（可选）

Tlmbgr是一款Latex宏包管理软件，类似于pip。要想使用Tlmbgr，我们首先要进行如下操作

```
sudo visudo
```

在打开的文件中找到`secure_path`，将`/usr/local/texlive/2022/bin/x86_64-linux:`复制到后面信息的最开头，然后分别按`Ctrl+x`，`y`，`Enter`进行保存。

这时我们发现使用`sudo tlmger`命令不会显示什么错误了。

接着我们使用下方命令选择一个距离较近的tlmbgr源：

```
sudo tlmgr option repository ctan
```

然后使用如下命令查看并更新所有宏包：

```
 sudo tlmgr update --list
 sudo tlmgr update --self --all
```

速度可能比较慢，建议挂机去做点别的事。