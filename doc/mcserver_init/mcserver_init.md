# Mcserver Init Flow

## Mcserver 初始化流程
### 0 初始化流程图

<image src="mcserver_init.svg">

<br>
<br>
<br>

### 1 日志系统初始化
    Log写入/var/log/messages

<br>


### 2 守护进程
    mcserver默认后台运行，启动时追加-f参数可以使其以前台模式启动

mcserver使用linux上常见的Double Fork方式创建守护进程
- 创建pipe进行父子进程间的通信
- 子进程成功进入daemon状态后，通过pipe发送已启动信息给父进程
- 同时父进程以30s的超时时间来接收此信息，并依据结果退出
- 子进程接管后续所有初始化流程

<br>

### 3 系统初始化
- 模拟io初始化
- 共享内存环境初始化
- crc校验初始化
- 配置信息初始化
- parameters信息初始化
- nvram初始化
- 注册中断信号

<br>

### 4 处理命令行参数

|序号|选项|说明|
|--|--|--|
|1|-a|模拟IO设置|
|2|-c|配置文件选项|
|3|-d|Debug模式|
|4|-e|控制模式设置|
|5|-f|运行模式|
|6|-h|打印列表|
|7|-i|运动学相关设置|
|8|-o|输出设备相关设置|
|9|-p|监听端口选项|
|10|-n|nvram设备|
||||

<br>

### 5 创建socket网络监听
    默认监听端口8055，可通过-p选项更改

<br>

### 6 注册控制模式
    将控制模式写入共享内存

<br>

### 7 业务初始化

|业务归属|业务名称|业务类型|plugin|
|--|--|--|--|
|robot|ExtAxis|外部轴|否|
|robot|robotresource|共享内存数据|否|
|robot|instruction_exec|程序执行|否|
|robot|interpolation|插补|否|
|ext_device|keyence|keyence|否|
|ext_device|robotiq|robotiq|librobotiq.so|
|robot|manual|手动插补|否|
|robot|axis_output_later|轴输出|rbtfpga_pos.so <br> ethercat_pos.so|
|robot|check_thread|执行||
|robot|userdata|用户数据|libuserframe.so|
|robot|tooldata|工具数据|libautoset_tool.so|
|robot|interference|干扰区||
|robot|trackdata|跟踪数据||
|robot|visiondata|视觉数据||
|robot|lasertrackdata|激光跟踪||
|robot|bookprog_data|预定程序||
|/|remote_server|远程模式||
|/|udp||libudpcomm.so|
|/|iobus||libiobus.so|
|/|technic|||
|/|plc_socket|连接plc服务|否|
|ext_device|setup_script_data|安装脚本||
|/|pallet|||
|||||

<br>

### 8 加密业务
|业务归属|业务名称|业务类型|plugin|
|--|--|--|--|
|/|encryption|加密|libfilereadwrite.so|

<br>

### 9 启动socket循环监听
    Socket网络服务采用多线程模型，即为每次socket连接创建单独线程进行处理