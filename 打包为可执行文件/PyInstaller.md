# Pyinstaller

PyInstaller 是一个可以将 Python 源代码打包成跨平台独立可执行文件的工具。无论是 Windows、Linux 还是 macOS，PyInstaller 都能够创建不依赖于 Python 解释器的二进制文件。

常见应用场景：

- 分发给不具备 Python 环境的用户。
- 打包包含多个模块、数据文件和资源文件的复杂项目。
- 设置自定义的图标和名称。

## 基础操作

### 1. 安装 PyInstaller

首先，确保你已经安装了 PyInstaller。通过 pip 可以轻松安装：

```bash
pip install pyinstaller
```

### 2. 准备你的项目

假设你的 Python 项目目录结构如下：

```
project/
├── main.py          # 主程序文件
├── module1.py       # 额外模块
├── data/            # 存放附件
│   └── config.json  # 配置文件
└── assets/          # 资源文件夹
    └── app.ico      # 应用程序图标
```

你需要确保 `main.py` 是你的主入口文件，而其他模块和资源也需要在打包时一起包含。

### 3. 基本打包命令

使用 PyInstaller 打包最基本的命令如下：

```bash
pyinstaller --onefile main.py
```

- --onefile 参数告诉 PyInstaller 将所有内容打包到一个独立的二进制文件中。
- 最终生成的可执行文件会放在 dist/ 目录下。

### 4. 添加图标

如果你的应用需要自定义图标，可以通过 --icon 参数指定图标文件：

```bash
pyinstaller --onefile --icon=assets/app.ico main.py
```

这将为生成的可执行文件添加自定义图标。

### 5. 打包多文件和资源

项目中往往包含一些非 Python 文件，如配置文件、图片等。这些文件需要在打包时一起捆绑，以便在运行时访问。

使用 --add-data 参数可以将外部文件打包进去。格式如下：

```bash
pyinstaller --onefile --add-data "source_path;destination_path" main.py
```

例如，如果你需要打包 data/config.json 和 assets/app.ico：

```bash
pyinstaller --onefile --add-data "data/config.json;data" --add-data "assets/app.ico;assets" --icon=assets/app.ico main.py
```

注意：

- 在 Windows 中，源路径和目标路径使用分号 ; 分隔。
- 在 Linux 和 macOS 中，使用冒号 : 分隔。

macOS 下正确的打包命令：

```bash
pyinstaller --onefile --add-data "data/config.json:data" --icon=assets/app.ico main.py
```