# 语法和基础

## 语法基础部分

- [数字](语法与基础模块/数字-Number.ipynb)
- [字符串](语法与基础模块/字符串-String.ipynb)
- [列表](语法与基础模块/列表-List.ipynb)
- [集合](语法与基础模块/集合-set.ipynb)
- [基础函数](语法与基础模块/基础函数.ipynb)

## 基础模块

- [operator](语法与基础模块/operator模块.ipynb)
- [heapq](语法与基础模块/堆-heapq模块.ipynb)
- [os](语法与基础模块/文件操作-os模块.ipynb)
- [re](语法与基础模块/正则表达式-re模块.ipynb)
- [time](语法与基础模块/日期与时间-time模块.ipynb)

# 桌面程序开发

## GUI

> ~~PyQt6 由于开源协议问题不选择~~ 

### [PySide6](/GUI/PySide6.md)

相关工具配置：[designer](/GUI/designer.md)

现代化GUI美化模版 - [PyDracula](/GUI/PyDracula.md)


## 应用程序打包

[Nuitka](/打包为可执行文件/Nuitka.md)

[PyInstaller](/打包为可执行文件/PyInstaller.md)

[cx_Freeze](/打包为可执行文件/cx_Freeze.md)

[liunx下打包为deb](/打包为可执行文件/linux下打包为deb.md)

# web开发 - Django框架

## [Django教程](django/Django教程.md)

### 准备

python > 3.10

django > 5.0

### 入门

- [URL组成](django/Django教程.md#URL组成)
- [创建项目](django/Django教程.md#创建一个Django项目)
- [运行项目](django/Django教程.md#运行Django项目)
- [url与视图映射](django/Django教程.md#url与视图映射)

### 模版

#### [模版介绍](django/Django教程.md#模版介绍)

- [模版渲染](django/Django教程.md#模版渲染)
- [模板查找路径配置](django/Django教程.md#模板查找路径配置)

#### [DTL模版语法](django/Django教程.md#DTL模版语法)

| 标签                                       | 内容                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [变量](django/Django教程.md#变量)             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| [常用标签](django/Django教程.md#常用标签)     | [if标签](django/Django教程.md#if标签)`<br>`[for标签](django/Django教程.md#for标签)`<br>`[with标签](django/Django教程.md#with标签)`<br>`[url标签](django/Django教程.md#url标签)`<br>`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| [常用过滤器](django/Django教程.md#常用过滤器) | [add](django/Django教程.md#add) [cut](django/Django教程.md#cut) [default](django/Django教程.md#default) [default_if_none](django/Django教程.md#default_if_none) [date](django/Django教程.md#date) `<br>`[first](django/Django教程.md#first) [last](django/Django教程.md#last) [length](django/Django教程.md#length) [lower](django/Django教程.md#lower) [upper](django/Django教程.md#upper) `<br>`[join](django/Django教程.md#join) [floatformat](django/Django教程.md#floatformat) [random](django/Django教程.md#random) [slice](django/Django教程.md#slice) [safe](django/Django教程.md#safe)`<br>` [striptags](django/Django教程.md#striptags) [truncatechars](django/Django教程.md#truncatechars) [truncatechars_html](django/Django教程.md#truncatechars_html)`<br>` |
| [模板结构](django/Django教程.md#模板结构)     | [include模板](django/Django教程.md#include模板)`<br>`[模板继承](django/Django教程.md#模板继承)`<br>`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

### 数据库

mysql > 8.0.21

驱动：pymysql

#### [数据库操作](django/Django教程.md#数据库操作)

1. [数据库配置](django/Django教程.md#在settingspy中配置)
2. [获取数据库信息](django/Django教程.md#在app中获取数据库信息)

#### [ORM模型](django/Django教程.md#ORM模型)

1. [创建ORM模型](django/Django教程.md#创建ORM模型)
2. [数据库迁移](django/Django教程.md#映射模型到数据库中)

#### [基本CRUD操作](django/Django教程.md#基本CRUD操作)

   [增](django/Django教程.md#添加一个模型到数)、
   [查](django/Django教程.md#查找数据)、
   [改](django/Django教程.md#修改数据)、
   [删](django/Django教程.md#删除数据)、
   [排序](django/Django教程.md#数据排序)

#### [常用Field和参数](django/Django教程.md#常用field和参数)

1. [数据库字段类型](django/Django教程.md#数据库字段类型)
2. [常用参数](django/Django教程.md#常用参数)
3. [模型中Meta配置](django/Django教程.md#模型中Meta配置)

#### [外键和表关系](django/Django教程.md#外键和表关系)

1. [外键](django/Django教程.md#外键)
2. [外键删除操作](django/Django教程.md#外键删除操作)
3. [表关系](django/Django教程.md#表关系)

   [一对多](django/Django教程.md#一对多)、
   [一对一](django/Django教程.md#一对一)、
   [多对多](django/Django教程.md#多对多)

