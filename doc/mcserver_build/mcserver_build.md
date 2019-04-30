# Mcserver Build

## Mcserver 编译

## 0 预备知识
Autotools工具链
- autoscan
- aclocal
- autoconf
- autoheader
- automake

Linux系统默认会安装相关工具，安装包为automake

    常规操作步骤：
    1 编写源代码项目
    2 执行autoscan, 扫描工作目录, 生成configure.scan文件. 此文件需要重新命名为configure.ac, 并且依据项目构建逻辑, 编辑此配置文件, 执行后续步骤
    3 执行aclocal, 会扫描编辑后的configure.ac, 生成aclocal.m4文件. 该文件主要处理本地的宏定义, 它根据已经安装的宏、用户宏定义和acinclude.m4文件中的宏将configure.ac文件需要的宏集中定义到aclocal.m4中
    4 执行autoheader，生成config.h.in
    5 编写Makefile.am，执行automake，生成Makefile.in
    6 执行autoconf，生成configure
    7 make

## 1 交叉编译环境设置
- 将linda编译环境解压到/opt下
- 执行 . /opt/linda/0.2/environment-setup-cortexa9hf-vfp-neon-linda-linux-gnueabi
- ./build-yocto.sh
- 将所有依赖的动态库放在src/plugin下

## 2 编译方法
    ./autogen.sh
    ./build_yocto.sh
    make

## 3 Makefile.am解析
### 3.1 主目录Makefile.am

    1. 通过配置para_define.in，调节项目的编译
    - 扫描para_define.in，提取变量信息，结合include/para_define.h.in，生成include/para_define.h
    - 扫描para_define.in，提取变量取值等信息，结合src/share/para_define.c.in，生成src/share/para_define.c

    2. 依据Git信息，生成version.h

### 3.2 分目录Makefile.am
    控制分目录编译