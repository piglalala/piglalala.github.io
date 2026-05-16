---
title: 「游戏/科技」如何在Linux环境下愉快的推Galgame——从基础配置运行到解决OP视频播放/没声音问题
tags:
  - 科技
  - 游戏
id: '450'
categories:
  - - 所有文章
  - - 游戏/Games
cover: https://files.catbox.moe/tq3p81.jpg
coverWidth: 1920
coverHeight: 1344
date: 2021-04-07 22:35:49
---

![](https://pigacg.files.wordpress.com/2021/04/e688aae59bbe_dde-desktop_20210407231948.jpg?w=1024)

前面几篇文章提到我现在正在用的主力系统是Deepin Linux，作为一个二次元死宅怎么可能不在电脑上推Galgame呢？不过众所周知Linux不适合作为娱乐平台，相关游戏的官方适配也较少，于是便决定把我自己的配置过程写成这篇Blog，一是用于备忘，其次若是能帮到几个人也是我的荣幸。<!--more-->

先明确我们的目标：能稳定的跑绝大多数的Galgame，能播放OP能存档保存配置，亦即获得与Windows平台差不多的Galgame游戏体验。

首先最简单的在Linux上跑Galgame的方式是直接使用Steam Linux版，例如水仙和命运石之门是可以直接在Linux上下载安装并运行的，这种方式相对傻瓜且稳定，但由于适配的游戏实在太少于是决定不采用。

第二种方式则是安装Play On Linux软件，并导入自己的Galgame.EXE文件进行安装，但还是老毛病——适配太少，许多游戏无法运行，于是也就此排除。

第三种则是使用ONSscrpter在电脑上运行手机上一样的ONS移植版本的Galgame，因为ONS模拟器适配了Linux平台，这种方法比起前两种支持的游戏也相对更多（点名万华镜和CL），得益于悠久的生态环境这种方法也十分稳定。但我们依旧不选择这种方法，原因是移植版本往往无法获得百分百完整的win版游戏体验，其次便是github上开发者貌似已跑路，后期稳定性存疑。

所以便有了这次文章的主角——wine。

wine是一个底层运行环境，能在Linux上模拟Windows操作系统环境，并通过终端命令winecfg和wine 你运行程序的名称.exe这两条命令进行操作系统设置的配置和运行软件。你可以把它理解为Linux上的一个Windows模拟器，在这个模拟器中可以运行Windows应用例如Office套件、WinRAR等。当然也包括了各式各样的游戏（网上有些大佬甚至可以在上面跑原神......）。

那么如何配置wine环境呢？

在Debian发行版中直接打开终端命令输入以下命令以开启32位支持，一般只要没报错就行了，需要root权限就先获取权限后运行命令，不过一般运行这条是不需要root权限的：

sudo dpkg --add-architecture i386

然后继续输入以下命令以获取wine的软件仓库秘钥：

wget -nc [https://dl.winehq.org/wine-builds/Release.key](https://dl.winehq.org/wine-builds/Release.key)  
sudo apt-key add Release.key

然后运行vim /etc/apt/sources.list 进入命令行文本编辑模式（i、：、wq那些基础操作这里就不说了），这个文件就是Debian软件仓库源的配置文件，之后再在末尾添加以下内容后wq保存退出到终端命令。这两行便是wine的软件源，添加后便可使用apt便捷的安装与更新软件。其中第一条是适用于Debian10系统的软件库，第二条是适用于DebianTesting系统的软件库，选择自己对应的添加就行，一般正常情况下是添加第一行。

deb [https://dl.winehq.org/wine-builds/debian/](https://dl.winehq.org/wine-builds/debian/) buster main  
deb [https://dl.winehq.org/wine-builds/debian/](https://dl.winehq.org/wine-builds/debian/) bullseye main

之后输入以下命令更新软件仓库：

sudo apt update

继续输入以下命令以安装wine稳定版：

sudo apt install --install-recommends winehq-stable

至此你的wine软件已经安装成功，可以使用以下命令查看wine版本以检测是否安装完成：

wine --version

![](https://pigacg.files.wordpress.com/2021/04/e688aae59bbe_deepin-terminal_20210407212751.jpg?w=924)

之后执行winecfg这条命令进行wine初次运行时的基础配置，一般默认就行。如果出现乱码就是字库不全问题，请自行搜索相关解决方案。然后wine的配置一般系统选择Windows7，函数库界面先在列表中添加我这些内容，如果列表中没有请手动在框内输入后添加并点击右下角的应用。其他的先暂时默认就行。

![](https://pigacg.files.wordpress.com/2021/04/e688aae59bbe_winecfg.exe_20210407164054.jpg.jpg?w=477)

然后保存退出到终端页面，此时可以使用clear清屏。如果你关了请重新在桌面打开终端后获取root权限。

之后便可开始进行游戏运行库的环境安装与配置了。

首先先明确，单纯的wine只是一个最基础的环境，往往刚设置完只是模拟了一个最简陋的Windows系统，而我们一般跑游戏例如在Steam上下载安装后，第一次运行一般都会自动安装与该游戏相关的运行库如DX9、DX11等。而Linux上配置这些环境的方式一是利用刚刚我们用到的wine里面的函数库，另一种则是利用winestricks环境通过输入代码进行配置。而一般正常情况下wine里自带的函数库是无法满足我们运行galgame的需求的，于是便需要用上winestricks。

你可以把winestricks理解成一种运行环境，也可理解成一个wine的图形化操作界面，里面可以方便的打开并安装各种软件，原理和Play On Linux类似，只需要在终端里输入winestricks这一条命令便可打开这个图形界面/应用。安装也十分简单，我这边的Deepin系统只需要下面一条命令便可安装winestricks：

sudo apt install winetricks

当然如果你安装不起也请直接搜索你的系统所对应的winestricks安装教程。当成功安装后直接在终端输入winestricks这几个字母就可以打开它，并进行相关配置。正常情况下需要等待那么几十秒。

![](https://pigacg.files.wordpress.com/2021/04/e688aae59bbe_e98089e68ba9e58cbae59f9f_20210407215527.jpg?w=1024)

之后确定打开无误后便可直接直接关闭winestricks这个窗口，并在终端中使用clear清屏，之后便是真正的运行库安装了。参考了[这个博主的方法](https://moonsekai.xyz/2020/11/26/%E5%9C%A8-Linux-%E4%B8%8B%E8%B7%91-galgame/)，可直接查看这个网页。方便起见我将其中一部分内容搬到这里来。

执行以下命令安装相关运行库，安装完一个软件就关闭一个软件以便后续软件进行安装，别直接退了终端，请等待一会直到确保所有的软件安装完毕，之后会没有任何报错显示运用于xxxx的触发器或是其他的，并可再次输入并执行命令即为安装成功：

winetricks d3dx9 quartz devenum wmp10 gdiplus dotnet40 ffdshow vcrun6 cjkfonts

到了这一步后你的电脑其实已经可以运行一些Galgame了例如Ever17、月姬之类的，这里我们使用Ever17进行测试，原因是这游戏有OP，而一般如果你的运行库没有安装全要么是OP无法播放，要么是OP和视频播放没声音。

同时保险起见如果你的Galgame需要安装，除开DLsite那种exe分卷的解压包（亲测正版的战国兰斯中文版可以安装，运行没试过），其他的保险起见请先在Windows里安装好了将整个文件夹拷贝到你的Linux系统上。

要用wine跑Galgame和其他win软件的方法也很简单：在你已经解压了的对应应用的文件夹里打开终端命令，找到你需要运行的文件并使用以下命令回车（xxxxx是你的文件名称）：

wine xxxxx.exe

当然如果你找名字嫌复杂也可直接在终端里输入ls回车，之后找到你要的软件，并替换掉上面的xxxx部分。例如我这边要运行Ever17且那个文件名是Ever17PC.exe，那命令就是下面这条：

wine Ever17PC.exe

第一次运行效果如图，OP播放只有半边，测试可在开始时选择全屏游戏OP便不会出现只有半边的问题，但命令行那边在OP播放时报错提示“你的Gstreamer缺少插件”且游戏OP没有声音。当然在我更早之前没安装上面的运行库尝试跑这个游戏时OP直接不能播放全是空白，只能右键跳过。不过正常进行游玩倒是没问题也可以存档。

![](https://pigacg.files.wordpress.com/2021/04/e688aae59bbe_deepin-terminal_20210407001754.jpg?w=924)

但正如前面所说，我想要最接近于Windows上跑Galgame的效果，这游戏内视频无法播放真的就像一根刺一样始终插在心里，于是同样是参考上文那个博主以及网络上各路大神的思路后找到了一种解决办法。

首先既然命令行中提示“您的Gstreamer缺少插件”，那应该是Gstreamer这块出了问题。Gstreamer是一个跨平台的多媒体娱乐开发环境，Linux上许多视频软件都需要用到它。它和wine一样，一般安装后只提供一个最基础、最底层的运行环境，而要实现其他功能则需要安装插件库（类比Adobe PS、PR）。也就是说会报错证明我们相关插件还没安装完成，所以我们必须安装完整的Gstreamer插件环境。

而如果你按照这篇教程走到这里就不用慌了，差不多要结束了。参考[Gstreamer官方安装帮助文档](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)，这里我只把Debian的搬过来。

首先桌面打开终端执行以下命令安装Gstreamer底层环境，如果显示没有任何软件更新或是其他没有安装过程也没有报错的情况，那就证明你的系统中已经安装了这个底层环境，不需要自己进行安装：

apt-get install libgstreamer1.0-0

之后执行以下命令安装good和ugly插件库，good是相对十分完整且稳定的插件库，而ugly是在此基础之上有一些还不那么稳定的测试功能，把这两个都安装上。

apt-get install gstreamer1.0-plugins-good

apt-get install gstreamer1.0-plugins-ugly

如果没报错那么你的两个插件库便已经成功安装。

之后按照上面那个博主的思路：浏览器前往 https://github.com/Nevcairiel/LAVFilters/releases 下载最新的 installer。  
使用之前的 wine 环境安装他；也就是像我前面所说的目录里打开终端使用wine命令运行这个软件。

这是一个Windows下的解码器，而我们Galgame如果要正常播放视频必须要得解码器依赖并正确配置。如果你的电脑无法访问github的话请参考我博客里之前的那篇Qv2ray走代理的文章，里面有讲到。

当你那个解码器安装完后就可以直接关了所有的终端以及其他软件啥的了，此时你的Linux系统已经具有一个相对完整的跑Galgame的环境了。我这边文件夹里打开终端用wine Ever17PC.exe重新运行了Ever17，开始游戏时选择全屏游戏并没有出现任何报错。不过退出的话只能选择先返回主菜单后再退出。

![](https://pigacg.files.wordpress.com/2021/04/e688aae59bbe_ever17pc.exe_20210407225744.jpg.jpg?w=800)

![](https://pigacg.files.wordpress.com/2021/04/e688aae59bbe_ever17pc.exe_20210407225857.jpg.jpg?w=800)

顺便如果你玩的是老引擎的Galgame那每次全屏运行退出后所有的桌面图标以及窗口大小都会被打乱，请注意。

当然其他新发售的Galgame我还没测试，不过视频播放这问题理论上这篇文章的思路通用。如果运行不起请活用winecfg命令在设置里调整你自己的Windows版本试试，也可调整全屏运行试试。而出现其他问题例如月姬没BGM可以在wine的音频设置那里把三个默认改成我下面这样。

![](https://pigacg.files.wordpress.com/2021/04/e688aae59bbe_winecfg.exe_20210407230857.jpg.jpg?w=477)

那么这篇Blog到这里就结束了，可以在Linux上愉快的推Galgame了！

PS.当然关于如何在Linux上配置Galgame的文章我后续还会更新的，因为这篇文章肯定还是无法满足所有需求（Steam正版、DLsite正版、日文字库以及汉化补丁、转区等），所以后面遇到了其他问题我还会继续更新后续文章的。 ：）

增补：

Ever17汉化硬盘版全屏完美运行（win10安装拷贝到Linux）

月姬汉化版完美运行

Summer pockes风笛汉化版完美运行

催眠学習 -Secret Desire-完美运行

面包房少女完美运行

VenusBlood-ABYSS-完美运行

VenusBlood-RAGNAROK-完美运行

SLEEPLESS完美运行

对魔忍全系列完美运行

陰陽騎士トワコ完美运行

人類最強性欲の嫁 工口倫子完美运行

巨乳令嬢ＭＣ学園完美运行

黒獣～気高き聖女は白濁に染まる～完美运行

多娜多娜官中汉化版 wine4.0版本运行失败（v1.1未打无码补丁），wine6.0版本无法播放视频

もっと！孕ませ！炎のおっぱい異世界超エロサキュバス学園！完美运行
