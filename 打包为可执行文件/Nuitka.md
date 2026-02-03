## 使用Nuitka打包

需要在虚拟环境中使用，且虚拟环境也需要安装Nuitka

> 项目一定都要是英文路径

### 创建虚拟环境

> [!TIP] 
>
> Nuitka 当前版本（2.8.9）在 Windows 上不支持用 MinGW64 编译 Python 3.13 的程序！

```bash
# 使用python创建虚拟环境
python -m venv .venv
# 激活虚拟环境
source .venv/bin/activate  # 在 Linux 或 macOS 上
.venv\Scripts\Activate.ps1  # 在 Windows 上
```
```bash
# 使用conda创建环境
conda create -n nuitka python=3.8
# 激活环境
conda activate nuitka
```
### 安装依赖包

```bash
# 安装nuitka
pip install nuitka
# 安装依赖包
pip install -r requirements.txt
```
### 打包常用命令

```bash
--mingw64 #默认为已经安装的vs2017去编译，否则就按指定的比如mingw(官方建议)
--standalone #独立环境，这是必须的(否则拷给别人无法使用)
--windows-disable-console #没有CMD控制窗口
--output-dir=out #生成exe到out文件夹下面去
--show-progress #显示编译的进度，很直观
--show-memory #显示内存的占用
--enable-plugin=pyside6
--plugin-enable=tk-inter #打包tkinter模块的刚需
--plugin-enable=numpy #打包numpy,pandas,matplotlib模块的刚需
--plugin-enable=torch #打包pytorch的刚需
--plugin-enable=tensorflow #打包tensorflow的刚需
--windows-icon-from-ico=你的.ico #软件的图标
--windows-company-name=Windows下软件公司信息
--windows-product-name=Windows下软件名称
--windows-file-version=Windows下软件的信息
--windows-product-version=Windows下软件的产品信息
--windows-file-description=Windows下软件的作用描述
--windows-uac-admin=Windows下用户可以使用管理员权限来安装
--linux-onefile-icon=Linux下的图标位置
--onefile #像pyinstaller一样打包成单个exe文件
--include-package=复制比如numpy,PyQt5 #这些带文件夹的叫包或者轮子
--include-module=复制比如when.py #这些以.py结尾的叫模块
```

# 示例

```bash
nuitka --mingw64 --show-progress --standalone --disable-console --enable-plugin=pyside6 --plugin-enable=numpy --onefile --remove-output camera.py
```