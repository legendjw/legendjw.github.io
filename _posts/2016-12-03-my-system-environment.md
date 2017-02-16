---
layout: post
title:  "Ubuntu系统以及开发环境的配置"
date:   2016-12-03 20:00:00
categories: "服务器"
permalink: /archivers/ubuntu-system-and-development-environment
---

我使用 Ubuntu 作为桌面环境已经大概有两年了，从14.04一直到现在的16.04，也算是忠实粉丝了，尽管与其他发行版相比可能稳定性上还是欠缺不少，甚至有大家所讲的 Ubuntu 
有违linux精神，但是对于新手来讲 Ubuntu 的易用性还是让很多人难以忘怀的，由于后面可能要更换为 Debian 或者其他linux发行版，所以今天来分享下我的 Ubuntu 环境以及
作为开发人员的一些配置，也算是作为 Ubuntu 使用者的最终总结吧。

## 桌面系统的美化

实际上我是实用主义，对于过度的美化追求是很反感的，但是我们还是可以适当对 Ubuntu 做一些美化，比如安装一些更加漂亮扁平的主题，不废多少功夫又可以换个桌面风格，
首先我还是使用的 Ubuntu 原生的 **unity** 桌面，我自己目前安装的主题是 [Flatabulous](https://github.com/anmoljagetia/Flatabulous)，桌面效果大概如下图，如果你
也比较喜欢这个主题的话点击上面官方连接可以查看安装教程，如果你不喜欢这个主题，可以到 [Ubuntu Themes 网站](http://www.ubuntuthemes.org/)去看看，总还是有你
喜欢的一款。

![Ubuntu Flatabulous](/images/ubuntu-flatabulous.png)

## 常用软件的推荐

这里推荐一些我自己在 Ubuntu 上真实使用过觉得很好的软件给大家，当然也可能你已经在使用了。

- [**FileZilla**](https://filezilla-project.org/) 十分强大易用的 FTP 软件，在我用过之后便设为首选 FTP 连接工具了
- [**kchmviewer**](http://www.ulduzsoft.com/linux/kchmviewer/) 查看 CHM 帮助文档的软件，我试用过linux上许多查看CHM的软件，这款目前是最满意的
- [**GoldenDict**](http://goldendict.org/) 词典翻译软件，我觉得是linux平台最好的词典软件了，优质的词典包可以从
[这篇博文](https://blog.yuanbin.me/posts/2013/01/goldendictxia-san-da-you-zhi-ci-ku-shi-yong-xiao-ji.html)选择下载
- [**Variety**](https://launchpad.net/variety) 自动更换桌面壁纸的软件，可以自助设置自己喜欢的图片网站
- [**Synapse**](https://launchpad.net/synapse-project) 快捷的搜索启动器，如果你不喜欢原生的系统搜索可以试一试这款小巧的软件
- [**Cairo-Dock**](https://launchpad.net/cairo-dock) 类似苹果系统的dock软件启动器，如果你不喜欢默认的unity侧边栏
- [**Calibre**](https://calibre-ebook.com/) 电子书管理系统，如果你有大量的电子书需要管理则此软件是很好的选择
- [**TLP**](http://linrunner.de/en/tlp/docs/tlp-linux-advanced-power-management.html) linux下的电源管理器，可以帮助你更好的省电，对于thinkpad笔记本还可以设置充电阈限值

## 终端的改造

对于开发人员来说，linux的终端应该是最最常用的工具了，基本上开发的任务都需要终端来解决了，所以我们需要比默认更加美观、强大的终端，最终终端配置效果如下图，

![Ubuntu Terminal](/images/ubuntu-terminal.png)

### 配置以及字体调整

Ubuntu 默认的终端配色太过单调，编程中我最喜欢的配色方案是 [**Solarized**](http://ethanschoonover.com/solarized)，所以这里也把终端的配色改成了和编程一致的 Solarized，
所幸已经有人写了[shell脚本](https://github.com/Anthony25/gnome-terminal-colors-solarized)可以直接安装。

```bash
# 安装 dconf
sudo apt-get install dconf-cli
# 克隆安装终端配置
git clone https://github.com/Anthony25/gnome-terminal-colors-solarized.git
cd gnome-terminal-colors-solarized
./install.sh
```

编程中我比较喜欢使用斜体字来显示代码，所以这里编辑终端下 Preferences > profiles > solarized-dark > edit >
General > Text Appearance > Custom font，我使用的是 **Ubuntu Mono Italic 11号**字体，你可以根据自己的喜欢选择指定的字体，但我希望你尝试下斜体字！

### zsh 以及 oh-my-zsh

linux上的shell很多，bash、csh、ksh、sh、zsh，到底哪个shell更好呢？这就是仁者见仁智者见智的事情了，不过总有人说真正强大的shell是 **zsh**，被誉为终极shell，
不过越强大越有复杂性，令人无比感动的是有人开发了 [**oh-my-zsh**](https://github.com/robbyrussell/oh-my-zsh)，可以使我们快速上手zsh，
并且包含了200多插件、140多个主题包供大家使用，真乃上古神器啊！！！这里吐血推荐你使用！！！

```bash
# 首先安装 zsh
sudo apt-get install zsh
# 改变当前shell为 zsh，需要重启终端
chsh -s /bin/zsh
# 安装 oh-my-zsh，下面两种方式任选一种
# 通过curl
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# 通过wget
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

安装完 oh-my-zsh 后需要注意，**默认只加载了 git 插件**，更多的插件需要自己修改配置手动加载进来，如下命令

```bash
# 修改 zsh 配置
vim ~/.zshrc
# 配置文件中 plugins 参数为加载的插件，可以根据你的需求做调整
plugins=(git extract z d copydir cp tmux tmuxinator vi-mode web-search ruby bundler rails composer yii2)
```

以下推荐几款常用的插件，你可以查看
[插件列表](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins-Overview)选择自己需要的。

- [**vi-mode**](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/vi-mode) 输入命令采用与vi类似的命令模式，vim党必备啊
- [**extract**](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/extract) 强大的解压插件，一个命令解压所有压缩格式文件
- [**z**](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/z) 强大的目录自动跳转命令，用模糊匹配快速进入你曾经进入过的目录
- **d** 直接列出你曾经进入过的目录，然后选择其中之一进入
- [**copydir**](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/copydir) 复制当前的目录到剪贴板，再也不需要pwd再用鼠标复制了
- [**web-search**](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/web-search) 直接在命令行里使用不同搜索引擎搜索关键字，如`google zsh`

关于主题我使用的是 **ys**，你可以去查看[所有的主题](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)，然后选择自己喜欢的使用。

### Tmux

Ubuntu 默认的终端窗口是支持以tab的形式打开多个的，但是无法在同一个窗口像 **vim** 那样分出多个窗格，Tmux是linux下的一款管理窗口的工具，支持在一个终端窗口下
打开多个 Window 窗口，每个 Window 窗口又可以划分多个窗格，更为重要的是 Tmux 支持把一组 Window 窗口保存为 Session，即使断开终端也可以快速恢复到之前保存
的会话窗口。下面来说一下 Tmux 的安装以及注意事项。

```bash
# 安装 tmux
sudo apt-get install tmux
# 启动 tmux
tmux
```

关于 tmux 默认的快捷键以及使用方式你可以参考[官方文档](http://man.openbsd.org/OpenBSD-current/man1/tmux.1) 或者[这篇博文](http://cenalulu.github.io/linux/tmux/)，
这里需要注意的是所有的tmux的操作都需要先按出前置操作键，tmux 默认的前置操作键是 **CTRL + b**，由于 **CTRL** 键使用很频繁，而且在键盘上的位置也很不方便，这里强烈建议你
把 **CTRL** 键和 **CapsLock** 键进行交换，这样小母手指就可以很方便的按出CTRL，如果你使用Emacs编辑器这也是非常好的选择，改键可以使用 **Tweak Tool**，
安装此工具后点击 **Typing > Ctrl key position > Swap Ctrl and Caps Lock** 可以快速交换这两个键。另外默认的 tmux 的风格比较朴素，可以使用github上比较好的
[**gpakosz**](https://github.com/gpakosz/.tmux)的配置文件，这样你就有了一个更加漂亮且更易操作的 tmux，需要注意此配置文件更改前置操作键为 CTRL + a，使用和vim更为接近的
快捷键。

## vim 以及 spf13-vim

工欲善其事,必先利其器，作为一个开发人员，一定要为自己选择一个好用的编辑器，我自己现在最喜欢的是 vim，另外许多 linux 发行版都是默认安装 vim，
所以还是希望你能掌握vim的 基本操作，这样在以后维护服务器的时候也好更改文件，关于 vim 的基础使用教程可以参考[简明 VIM 练级攻略](http://coolshell.cn/articles/5426.html)，
vim 默认是很简洁的编辑器，由于有很好的扩展性和可配置性我们可以把它打造成极其完善的类似IDE的编辑器，如果你热爱倒腾可以自己搜索如何使用vim打造完整的IDE，
然后按照博文安装插件调整vim的配置来使vim更符合你的编程方式。如果你不想这么折腾或者已经折腾了好长时间想换个更好的vim配置，这里便强烈推荐你使用
终极vim配置[**spf13/spf13-vim**](https://github.com/spf13/spf13-vim)，此配置文件对vim的界面、格式化、快捷键都做了很好的设置调整， 并预安装了 
**NERDTree、NERDCommenter、neocomplete、Syntastic、AutoClose、Tagbar** 等等许多适合编程所用可以跳过效率的插件，可以说是最省事省力搭建优秀的
编程环境的最佳方式了。当然没有任何配置能完全适合每一个人，我们依然可以针对此配置进行一些调整来更符合自己的开发习惯，下面说下安装以及我自己所做的一些调整。

```bash
# 安装 spf13，你原来的vim配置将会被备份到家目录，所以以后可以随时再改回原来配置
curl https://j.mp/spf13-vim3 -L > spf13-vim.sh && sh spf13-vim.sh
```

编辑 **~/.vimrc.local** 本地配置文件，可以做一些符合自己使用习惯的设定：

```vim
" javascript、ruby、yaml文件都使用2个空格作为tab
autocmd FileType haskell,puppet,ruby,yml,yaml,javascript setlocal expandtab shiftwidth=2 softtabstop=2

" 关闭拼写检查
set nospell

" gvim运行时使用 mono 11号字体
if has("gui_running")
    set guifont=Ubuntu\ Mono\ Italic\ 11
endif

" 始终显示左侧 NERDTree 目录树
let NERDTreeQuitOnOpen=0

" buffer快速导航，使我们可以快速的切换已开 buffer
nnoremap <Leader>b :bp<CR>
nnoremap <Leader>f :bn<CR>
nnoremap <Leader>1 :1b<CR>
nnoremap <Leader>2 :2b<CR>
nnoremap <Leader>3 :3b<CR>
nnoremap <Leader>4 :4b<CR>
nnoremap <Leader>5 :5b<CR>
nnoremap <Leader>6 :6b<CR>
nnoremap <Leader>7 :7b<CR>
nnoremap <Leader>8 :8b<CR>
nnoremap <Leader>9 :9b<CR>
nnoremap <Leader>0 :10b<CR>
```
