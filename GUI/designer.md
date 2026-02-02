## PyCharm 配置 Qt Designer 完整指南

在 PyCharm 中使用 Qt Designer 可以更方便地编辑 .ui 界面文件，但默认情况下 PyCharm 并不会自动集成 Qt Designer，我们需要手动配置 External Tools(外部工具) 来调用它。

### 1. 为什么要在 PyCharm 里配置 Qt Designer？

PyCharm 是一个强大的 Python IDE，但它本身并不自带 Qt Designer。

如果你使用 PyQt6 或 PySide6 开发 GUI，通常需要手动打开 Qt Designer 设计界面，然后用 pyuic6 转换成 Python 代码。

手动操作太麻烦，我们可以：

- 直接在 PyCharm 里右键 .ui 文件，快速打开 Qt Designer。
- 使用 External Tools 让 PyCharm 直接调用 Qt Designer。

配置好后，你可以像这样使用：

- 右键 .ui 文件 → 选择 External Tools -> Qt Designer，直接打开设计界面。
- 从 PyCharm 工具栏快速启动 Qt Designer，不必手动打开它。

### 2. 配置 External Tools 调用 Qt Designer

#### 第一步：找到 Qt Designer 的安装路径

在 PyCharm 配置 External Tools 之前，我们需要找到 Qt Designer 的 designer.exe 文件。

根据你的 Qt 安装方式，它可能位于：

1. 如果你安装了 PyQt6（pip 安装）

    ```bash
    C:\Users\你的用户名\.conda\envs\<env_name>\Lib\site-packages\PySide6\designer.exe
    ```

2. 如果你安装了 Qt 官方版

    ```bash
    C:\Qt\6.x.x\mingwXX_64\bin\designer.exe
    ```
3. 如果你使用 Anaconda

    ```bash
    C:\Users\你的用户名\miniconda3\Library\bin\designer.exe
    ```

可以在 CMD 运行以下命令检查：

```bash
where designer
```

如果找不到，可以手动去 C:\Qt\ 或者 C:\Users\你的用户名\miniconda3\Lib\site-packages\ 里搜索 designer.exe。

#### 第二步：在 PyCharm 里添加 External Tool

找到 Qt Designer 后，我们就可以在 PyCharm 里配置它了。

1. 打开 PyCharm 设置

    File → Settings（快捷键 Ctrl + Alt + S）。
    在左侧选择 Tools → External Tools(外部工具)。
    点击 +（Add） 添加新工具。

2. 填写 Qt Designer 的配置信息

    |选项	|设置值|
    |-------|---------|
    |Name（名称）	|Qt Designer|
    |Program（程序）	|C:\Users\你的用户名\miniconda3\Lib\site-packages\qt6_applications\Qt\bin\designer.exe|
    |Arguments（参数）	|（留空）|
    |Working Directory（工作目录）	|（待定，后面讨论）|

    然后点击 OK 保存。

    📌 问题： PyCharm 默认会把 Working Directory 设成 bin 目录，这让我很疑惑，因为这意味着 Qt Designer 会在 bin 目录下运行，而不是我的项目目录。

3. 选择合适的 Working Directory

    在 External Tools 里，我们需要指定 Working Directory，否则 Qt Designer 可能会在错误的目录下运行。

    🔹 选项 1：使用 `$FileDir$`

    `$FileDir$` 代表 当前打开的 .ui 文件所在目录。

    ✅ 适用于：

    如果你希望右键 .ui 文件，直接用 Qt Designer 打开，并且默认进入 .ui 文件所在的目录。
    方便直接修改 UI 文件，而不必在 Qt Designer 里手动选择路径。
    ❌ 可能的问题：

    如果你直接从 PyCharm 的 External Tools 启动 Qt Designer（不针对 .ui 文件），它可能会进入空目录。
    设置方式：`$FileDir$`

    🔹 选项 2：使用 `$ProjectFileDir$`
    `$ProjectFileDir$` 代表 PyCharm 项目的根目录。

    ✅ 适用于：

    如果你的 .ui 文件存放在 PyCharm 项目的 ui/ 目录下，并且你希望 Qt Designer 启动后默认进入整个项目。
    如果你经常用 Qt Designer 打开多个 .ui 文件，而不是针对单个文件操作。

    ❌ 可能的问题：

    如果你的 .ui 文件分布在不同的子目录，你可能需要手动找到它们。

    设置方式：`$ProjectFileDir$`

4. 最终配置总结

    根据你的需求选择最优配置：

    |目标	|配置|
    |-------|---|
    |右键 .ui 文件，直接打开它	|`$FileDir$`|
    |让 Qt Designer 以 PyCharm 项目为默认目录	|`$ProjectFileDir$`|
    |不填，使用 Qt 默认目录	|PyCharm 默认 bin 目录|

    最终的 PyCharm 配置

    |选项	|推荐设置|
    |-------|---------|
    |Name	|Qt Designer|
    |Program	|C:\Users\你的用户名\miniconda3\Lib\site-packages\PySide6\designer.exe|
    |Arguments	|（留空）|
    |Working Directory	|`$FileDir$`（推荐）或 `$ProjectFileDir$`|

5. 结论

    ✅ 如果你希望 PyCharm 直接打开 .ui 文件，建议使用 `$FileDir$`
    这样，右键 .ui 文件 → External Tools → Qt Designer 就会直接打开该文件，并且 默认工作目录是该 .ui 文件所在的目录，减少手动查找文件的麻烦。

    ✅ 如果你想让 Qt Designer 以整个 PyCharm 项目为默认目录，建议使用 `$ProjectFileDir$`
    这样，每次打开 Qt Designer，它的 默认目录是 PyCharm 项目目录，方便管理多个 UI 文件。

🎯 如果你不确定，建议用 `$FileDir$`，这样 PyCharm 直接打开 .ui 文件，最直观、最高效！