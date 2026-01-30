## Ubuntu 打包成deb

一个python工程的打包过程， 分为两大步骤：

1. 将工程打包生成一个可执行文件；
2. 将可执行文件和工程依赖的所有文件制作成.deb安装包。

需要准备的

1. 源代码（前提要可执行）或者编译好的程序，这里只介绍python
2. 能用的ubuntu系统
3. 用户的密码
4. 图标（一定要有！）

### 一、 将工程打包生成一个可执行文件

1. 安装PyInstaller

    ```bash
    pip install pyinstaller
    ```

2. 生成可执行文件

    ```bash
    pyinstaller -w -F -i icon.ico main.py
    ```
pyinstaller -F main.py  打包ubuntu下的可执行文件
pyinstaller -F -w main.py  不带控制台的打包
pyinstaller -F -w -i Aplication.ico main.py  打包指定ubuntu下的可执行文件的图标打包

3. 将工程里面除了.py文件(作用:保留源码)外的所有依赖文件（如数据文件）按原来在工程中的目录 拷贝到当前目录的/test/dist/test下，在其他工作目录下运行可执行文件(如/usr/test(绝对路径)),看是否可以执行，若不能运行，可能是以下原因：

    - 路径中有汉字;
    - 你的python程序中有路径不会随文件目录变化而变化；
    - 依赖文件没有拷贝或拷贝不完整。

### 二、将可执行文件和工程依赖的所有文件制作成.deb安装包。

#### 1. 建立目录架构

程序是要放到一个系统中的各个部分去的。其实这个并没有那么严格，不一定全都要挤到/usr/lib里边去，放到/home等都没有太大的问题。这里你需要用一个文件夹模拟出linux的文件架构，只需要模拟你程序需要安放到的位置就行了。比如说我有可执行文件和工程依赖的所有文件，放到的文件夹是/usr/lib/debpacktest。那么我只需要建一个大文件夹（命名随意），cd到这个大文件夹，直接一个mkdir新建/usr /lib /debpacktest，然后把可执行文件和工程依赖的所有文件丢进debpacktest文件夹。下面罗列出来的是目录结构，多余出来的是需要手工添加的deb包配置文件（夹）。（share部分和DEBIAN部分）

```
├── debpackTotal
│   ├── DEBIAN（这个目录要添加control文件（无后缀名），可选添加postinst等）
│   └── usr
│       ├── lib
│       │   └── debpacktest（预定要安装到系统中的可执行文件和工程依赖的所有文件）
│       │       └── main等
│       └── share
│           ├── applications（在这里添加xxx.desktop，使应用中能够加载出你的应用）
│           └── icons（你的图标，这个必须有） # 踩雷一
```

在DEBIAN目录下制作control文件

cd到DEBIAN，在命令行输入（当然，你用gedit也行）

```bash
vim control
```
在其中粘贴：

```bash
Package: debpacktest
Version: 1.0.0
Architecture: amd64
Maintainer: gao
Description: just a test
```
括号后的内容 根据你自己的需要去改变，然后保存

>Package: 你的包名
Version: 版本
Architecture: 架构
Maintainer: 维护者
Description: 解释

#### 2.在/usr/share/applications下制作桌面图标 debpacktest.desktop

这一步非常重要 关系到你能否在应用列表出现你自己的程序，清一定按照下面的步骤完成。

首先在/usr/share/applications/下新建debpacktest.desktop这个文件。

里面的内容是：
```bash
[Desktop Entry]
Name=debpacktest
Comment=An example
Exec=/usr/lib/debpacktest/main
Icon=/usr/share/icons/debpacktest.ico
Terminal=false
Type=Application
X-Ubuntu-Touch=true
Categories=Development
```
剩下的东西 都不要动 都不要动
文件不能有多余的空格！ 踩雷五
desktop文件要有可执行权限！

为一个文件添加可执行权限

```bash
chmod +x filename
```

```bash
Name: 你的包名
Comment: 评论 注释
Exec: 这里非常重要！这里如果不对 会直接导致这个图标没有办法显示。对于python来说，一定要具体到执行哪个可执行文件。这里执行的是main。不要忘记chmod +x 文件名，给main加上可执行权限（用pyinstaller打包生成都有可执行权限）。 # 踩雷二
Icon: 图标，不可以忽略 # 踩雷三
Terminal: 是否在终端执行。这里是QtPy窗体，不需要。如果需要的话可以参考vim的desktop文件 # 踩雷四
X-Ubuntu-Touch: # 这个暂时我也不知道是什么用的
Categories: 分类。Categories可以取以下值，表示程序的启动快捷方式放在哪个菜单下; 应用菜Application; Network放在; 互联网Internet; 办公Office; 图形Graphics; 声音和视频AudioVideo; 系统工具System; 编程Development; 附件Utility; 影音AudioVideo; 游戏Game; 首选项Settings（GNOME/GTK/Settings/HardwareSettings）; 系统管理System
```

#### 3.打包

到这里，一个基本的deb软件包就大功告成了。

我们返回到debpackTotal的上级目录。我们执行

```bash
sudo dpkg -b debpackTotal debpacktest_1.0.0_amd64.deb
```

包的命名要符合规范(字母、数字、"-+")。

检验的话，可以输入（安装deb包）

```bash
sudo dpkg -i debpacktest_1.0.0_amd64.deb
```

在我的ubuntu机器上，运行很完美，我在应用列表里看到了我的程序。
如果要卸载，请执行

```bash
sudo dpkg -r debpacktest
```