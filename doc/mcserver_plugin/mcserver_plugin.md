# Mcserver_plugin

## 0 预备知识
<b>Linux动态库操作集合</b>

### - dlopen
```cpp
#include <dlfcn.h>
void* dlopen (const char * pathname, int mode);
```
    使用指定的模式打开指定的链接库文件，并返回一个句柄给调用程序

    编译时需要加入-ldl(指定dl.so)

    Args:
    - pathname // 动态库路径
    - mode
        RTLD_LAZY // 暂缓决定，等有需要时再解出符号
        RTLD_NOW // 立即决定，返回前解除所有未决定的符号

    Return:
    - 打开错误返回NULL
    - 成功返回库引用

    dlopen将打开一个新库，并把它装入内存。该函数主要用来用加载库中的符号，这些符号在编译的时候是不知道的。

### - dlsym
```cpp
#include <dlfcn.h>
void* dlsym (void* handle,const char* symbol);
```
    当库被装入后，可以把dlopen()返回的句柄作为dlsym()的第一个参数，以获得符号在库中的地址。使用这个地址，就可以获得库中特定函数的指针，并且调用被加载的库中相应函数

    Args:
    - handle  // 加载库的句柄
    - symbol  // 函数符号

    Return:
    查询的函数地址

    使用dlsym不仅可以寻找到函数的地址，还可以找到变量的地址。

### - dlclose
```cpp
#include <dlfcn.h>
int dlclose (void* handle);
```
    用于关闭指定句柄的动态链接库

    Args:
    - handle // 加载库的句柄

    Return
    - 执行结果

### - dlerror
```cpp
#include <dlfcn.h>
const char* dlerror (void);
```
    当动态链接库的操作失败时，可以通过dlerror获得错误信息，返回值为NULL时表示操作函数执行成功

## Mcserver Plugin机制
    mcserver采用了上述的机制，来完成plugin的功能

    将不同的工艺实现以动态库的形式提供，然后在mcserver中调用上述机制来获得对应的API来完成具体的功能。