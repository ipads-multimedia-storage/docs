# Run-With-Demo

可以利用当前的系统跑一个较为简单的本机检测

首先分别根据本repo下的`client-tut.md`和`flink-server`repo下的`README.md`学习客户端和服务端的配置。

现在的代码是在树莓派和电脑端通信，本机测试前请首先：

- 将视频的发送端设置为从文件读取(`client.py`)

  ```python
  def send_video():
      # estimate frame_size and fps
      capture = cv2.VideoCapture("video.avi")
  ```

- 将消息的接收端设为本机（`config.properties` in repo `flink-server`）

  ```properties
  socket.sink.hostname = localhost
  ```

接下来在client文件夹中执行

```
python client.py -m debug
```

等到程序输出所有信息后再运行server的代码。

