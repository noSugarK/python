# cx_Freeze

## 简介

cx_Freeze是一个Python库，它可以将Python程序转换为独立的可执行文件，这个文件包含了Python解释器和你的程序，使得它可以在没有Python解释器的环境中独立运行。

特点：

- 静态分发：生成的可执行文件是静态的，不依赖于主机上已安装的Python环境。

- 跨平台：支持在多个平台上运行，包括Windows、Linux和macOS。

- 配置灵活：可以通过设置选项来配置打包过程，以满足不同的需求。

使用步骤：

1. 安装cx_Freeze库。
2. 编写一个包含打包配置的setup.py文件。
3. 运行setup.py文件进行打包。


## 使用cx_Freeze打包

### 安装cx_Freeze：

```bash
pip install cx_Freeze
```

创建setup.py文件： 在你的项目目录下创建一个名为setup.py的文件，用于配置打包选项和指定入口点等信息。示例：

```python
import sys
import os
from cx_Freeze import setup, Executable

# ADD FILES
files = ['icon.ico','themes/']

# TARGET
target = Executable(
    script="main.py",
    base="Win32GUI",
    icon="icon.ico"
)

# SETUP CX FREEZE
setup(
    name = "YourAppName",
    version = "1.0",
    description = "Your application description",
    author = "YourName",
    options = {'build_exe' : {'include_files' : files}},
    executables = [target]
)
```

### 运行项目并下载源码

#### 运行打包命令

在命令行中运行以下命令，使用setup.py文件来进行打包：

```bash
python setup.py build
```

#### 获取打包结果

打包成功后，你将在build目录下找到生成的可执行文件。

## 注意事项：

- 在运行打包命令之前，确保你的Python程序能够在本地正常运行，并且所有依赖的库都已经安装。

- 如果你的程序依赖于一些第三方库，记得在setup.py或命令行参数中指定这些依赖项。

- 最好的方法是使用python虚拟环境来完成，

    ```bash
    python -m venv venv
    venv\Scripts\activate # 激活虚拟环境
    ```
    运行项目并下载源码，然后在这个虚拟环境里安装你需要的包和代码，然后再打包

当你使用cx_Freeze或PyInstaller时，它们的运行原理略有不同，但总体上都是将Python程序和依赖的库打包成一个可执行文件。下面是它们的运行原理简要解释：

### cx_Freeze：

- 分析和收集依赖：cx_Freeze首先会分析你的Python程序，找出所有导入的模块和依赖的库。然后，它会收集这些依赖，包括Python标准库和第三方库。

- 转换为C代码：接下来，cx_Freeze会将Python代码转换为C代码，包括程序入口点和所有导入的模块。这个过程将Python代码转换为C语言的可执行文件。

- 编译为可执行文件：最后，cx_Freeze将生成的C代码编译成可执行文件，这个文件包含了Python解释器以及你的程序，使得它可以在没有Python解释器的环境中运行。

### PyInstaller：

- 分析和收集依赖：PyInstaller会类似地分析你的Python程序，并收集所有的依赖项，包括Python标准库和第三方库。

- 打包成单个可执行文件：PyInstaller将Python程序和依赖的库打包成一个单独的可执行文件。这个文件包含了一个自包含的Python解释器以及你的程序，使得它可以在没有Python解释器的环境中独立运行。

- 生成启动脚本：PyInstaller还会生成一个启动脚本，它负责在运行时解压和加载打包的程序和依赖项。

- 这里面会出现有时候你使用 PyInstaller生成的exe文件打不开，点击一下就闪退，有可能是：

    1. 安全设置，你的电脑将这个exe文件识别为病毒了，解开就好

    2. 压根就没有打包成功

    > 在第二种情况下，就要改用另外一种方法打包程序了，cx_Freeze就我目前的使用体验而言还是不错的。

## 以下是`cx_Freeze`和`PyInstaller`之间的区别：

| | cx_Freeze | PyInstaller |
| --- | --- | --- |
|打包方式 | 将Python代码转换为C代码，然后编译成可执行文件。它的打包结果包含Python解释器和程序。 | 将Python代码和依赖的库打包成一个单独的可执行文件，内置了一个自包含的Python解释器。 |
|依赖处理 | 需要手动指定一些依赖项，因为它不总是能够自动识别所有的依赖。 | 通常能够自动检测并包含所有的依赖项，使得打包过程更简单。 |
|可移植性 | 生成的可执行文件在不同平台上可能不太可移植，因为它依赖于C代码的编译。 | 生成的可执行文件通常相对较小，因为它包含了一个自包含的Python解释器。 |
|打包结果 | 生成的可执行文件相对较大，因为它包含了Python解释器。 | 生成的可执行文件通常相对较小，因为它将Python解释器与程序打包成一个文件。 |
|更新和支持 | 可能不如PyInstaller那样经常更新，且社区支持可能较少。 | 经常更新，且拥有活跃的社区支持。 |
