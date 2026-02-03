# PySide6 - Qt for Python

## 输入和显示-欢迎界面QSplashScreen

```python
import sys
import time
from PySide6.QtGui import QIcon, QPixmap
from PySide6.QtCore import QMimeData, Qt
from PySide6.QtWidgets import QApplication, QMainWindow, QWidget, QSplashScreen, QLabel


class MyWindow(QWidget):
    def __init__(self,parent=None):
        super().__init__(parent)
        self.set_windows()
        self.business_demo()

    def set_windows(self):
        self.resize(300,300)# 设置窗口大小
        self.setWindowTitle("hello world")# 设置窗口标题

    def business_demo(self):
        qlabel = QLabel(self)
        qlabel.setText("hello world")# 设置label文字
        qlabel.move((self.width()- qlabel.width())// 2,(self.height()- qlabel.height())// 2)# 居中展示文本


if __name__ =='__main__':
    app = QApplication(sys.argv)

    # 欢迎界面
    pixmap = QPixmap('images/images/Python-code.jpg')
    splash = QSplashScreen(pixmap)
    splash.show()
    app.processEvents()
    splash.showMessage('Welcome to Use This PySide6 notebook~',Qt.Alignbottom | Qt.AlignCenter,Qt.white)
    # time.sleep(2) # 不能硬阻塞，否则会阻塞界面
    for i in range(1,6):
        splash.showMessage(f"Loading resource {i}/5...", Qt.AlignBottom | Qt.AlignHCenter, Qt.white)
        app.processEvents()
        time.sleep(0.5)

    # 主窗口
    demo = MyWindow()
    demo.show()
    splash.finish(demo)

    sys.exit(app.exec())
```