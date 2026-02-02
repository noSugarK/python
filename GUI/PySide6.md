## PyDracula

### 模版

https://github.com/Wanderson-Magalhaes/Modern_GUI_PyDracula_PySide6_or_PyQt6

### 教程

https://www.youtube.com/watch?v=9DnaHg4M_AM

### 项目结构解析

> 为了更好地利用 PyDracula，理解其项目结构至关重要。

```
main.py: 应用程序的入口文件，负责初始化主窗口和加载基本设置。

main.ui: 使用 Qt Designer 创建的界面布局文件。您可以使用 pyside6-designer 对其进行可视化编辑。

modules/: 存放 PyDracula GUI 运行所需的核心模块。
    app_settings.py: 定义了用于配置用户界面的全局变量。
    ui_main.py: 由 main.ui 文件编译生成的 Python 代码。
    ui_functions.py: 包含了与用户界面交互相关的核心函数，例如菜单的展开与折叠、窗口的最大化与最小化等。
    app_functions.py: 您可以将自己应用的特定功能函数添加于此。

themes/: 存放了 .qss 格式的主题样式表文件。

images/: 存放应用中使用的图标和图片资源。
```

改进版本

https://github.com/zhiyiYo/PyQt-Fluent-Widgets/tree/PySide6

PyDracula-flet 是一个基于 Flet 参考 PyDracula 构建的现代化桌面应用程序模版

https://github.com/clarencejh/PyDracula-flet

[基于PyDracula的深度学习客户端](https://blog.csdn.net/chenyuan1111/category_12889172.html?fromshare=blogcolumn&sharetype=blogcolumn&sharerId=12889172&sharerefer=PC&sharesource=qq_73886781&sharefrom=from_link)

## Qt Designer 使用全攻略

python安装PySide6后,找到python路径下的qtdesigner.exe

(/path/to/python)/Lib/site-packages/PySide6/designer.exe

https://blog.csdn.net/climber1121/article/details/132311894

https://blog.l0v0.com/posts/bea01990.html

### 使用PySide6可视化Yolo结果

[YoloSide - YOLOv8 GUI By PySide6](https://github.com/Jai-wei/YOLOv8-PySide6-GUI)