## 安装

[下载](https://www.pypy.org/download.html)

[官方文档](https://doc.pypy.org/en/latest/)

解压安装包

#### 配置环境变量

- Windows

    在环境变量中添加`pypy3`的安装目录

- Linux

    ```bash
    export PATH=$PATH:/path/to/pypy3/bin
    ```

#### 验证安装

```bash
pypy3 -V
```

## 配置环境

### 创建虚拟环境

1. 通过`venv`模块

```bash
pypy3 -m venv .venv
```

2. 通过`virtualenv`工具

```bash
pip install virtualenv
```

```bash
virtualenv -p pypy3 .venv
source .venv/bin/activate #linux
```

### 激活环境

```bash
# Windows powershell
.venv/Scripts/Activate.ps1
# Windows cmd
.venv/Scripts/activate
# liunx
.venv/bin/activate
```

### 安装依赖包

```bash
pip install -r requirements.txt
```

## 性能优化

> [!TIP]
> 使用`JIT`编译器