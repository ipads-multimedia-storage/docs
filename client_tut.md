# 客户端
## 先备条件
首先请确保你的开发环境成功安装了python，如果你使用的是windows系统，你可以在cmd中通过如下命令检查(请确保你安装的是Python3而不是Python2，具体版本号不重要)。
```
>python -V
Python 3.7.3
```
此外，建议安装git。
```
>git version
git version 2.27.0.windows.1
```
## 启动程序
在工作目录下克隆仓库
```
>git clone git@github.com:ipads-multimedia-storage/client.git
Cloning into 'client'...
remote: Enumerating objects: 133, done.
remote: Counting objects: 100% (133/133), done.
remote: Compressing objects: 100% (91/91), done.
remote: Total 133 (delta 56), reused 107 (delta 35), pack-reused 0
Receiving objects: 100% (133/133), 45.33 KiB | 115.00 KiB/s, done.
Resolving deltas: 100% (56/56), done.
```
进入目录并执行程序
```
>cd client
>python client.py -m debug
Your server's ip is: localhost
Expected fps(-1 for disabled): -1
Initial encoding rate is: 95
Running without arm
Please don't start server until the initialization finishes!
Average frame size is: 80.79817999999999KB, fps is: 32
Initialization finished, you can start server now.
```
如果你的电脑有摄像头，那么运行程序时会出现上述输出，且电脑摄像头指示灯亮起，程序正常运行。
此后该程序会进入休眠状态等待服务器连接，如果你已经能配置好服务端，那么你可以启动服务端进程并与之通信；
如果你的服务端还没有配置好，此时你可以强制结束该客户端进程。

当你的服务端与客户端成功建立连接后，你应该能够在客户端看到如下输出。
```
sending side: built connection with ('localhost', 8002)
receiving side: accepted connection from ('127.0.0.1', 49320)
bandwidth detection: accepted connection from ('127.0.0.1', 49319)
```
同时服务端会弹出窗口展示你电脑的摄像头所拍摄到的图像。至此，你已经启动了整个系统。
## 代码简介
`client.py`为客户端代码的入口文件，其中开启了3个线程，分别负责发送视频，接收数据并操控机械臂抓取，以及带宽检测，如下。
```
t1 = threading.Thread(target=send_video)
t2 = threading.Thread(target=receive_message)
t3 = threading.Thread(target=detect_bandwidth)
```
如果你还没有学什么是线程，可以上网简单查询相关资料，如果你想进一步了解，可参考CSAPP教材相关章节。

- 在`send_video`函数中，首先会打开摄像头并统计帧率和帧数据大小。其中`capture.read()`表示从摄像头中读取一帧图像。统计结束后，会通过`isReady.wait()`等待服务端sink部分连接，服务端连接后，便会开始向服务端source部分不断发送视频数据。

- `receive_message`函数会接收服务端发送的数据并操控机械臂进行抓取。

- `detect_bandwidth`会统计当前带宽信息并对当前编码率和帧率进行调整。

`arm_controller.py`文件是对机械臂底层控制代码的简单封装，`client.py`通过调用其中的函数来控制机械臂。

机械臂底层的相关代码在ArmIK和HiwonderSDK两个目录下，这些由厂商提供，请不要随意更改。