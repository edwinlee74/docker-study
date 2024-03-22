<h1 align=center>Dockerfile</h1>

Dockerfile是用以建置image的指令稿，下列是最常用的一些指令:


| 指令                |  描述                                                                                                          |
| -----------------   | -----------                                                                                                    |
| FROM \<image>       | 定義image的基礎                                                                                                |
| RUN \<command>      | 在當前image頂部的新圖層中執行任何命令並提交結果，還具有用於運行命令的shell。                                    |
| WORKDIR \<directory>| 為 Dockerfile 中的任何CMD、RUN、COPY、ENTRYPOINT、ADD 和後面的指令設定工作目錄。                               |
| COPY \<src> \<dest> | 從中複製新檔案或目錄，並將它們添加到容器的檔案系統中，路徑為從<src>到<dest>。                                   |
| CMD \<command>      | 允許您定義基於此映像啟動容器後運行的預設程式。每個 Dockerfile 只有一個 ，當存在多個實例時，只考慮最後一個實例。 |

<h3>檔名</h3>
要注意的是Dockerfile預設是無需副檔名的，但如果專案需求可以在docker build後加上--file的參數來指定Dockerfile。

<h3>Docker images</h3>
Docker image是由層(layers)所構成的，每一層都是由Dockerfile去建置的結果，每一層也會依序堆疊，並且都是基於上一層的增量。

<h3>範例</h3>

下面以一個典型的Dockcer容器的建置流程，以下用一個python做為例子說明:
```shell
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"
```

下面是Dockerfile:
```shell
# syntax=docker/dockerfile:1
FROM ubuntu:22.04

# install app dependencies
RUN apt-get update && apt-get install -y python3 python3-pip
RUN pip install flask==3.0.*

# install app
COPY hello.py /

# final configuration
ENV FLASK_APP=hello
EXPOSE 8000
CMD ["flask", "run", "--host", "0.0.0.0", "--port", "8000"]
```

一個Dockerfile可以分為幾個部份來說明:

<h4>Dockerfile syntax</h4>

Dockerfile第一行的#syntax聲明了Dockerfile的語法版本，雖然是可選的，但這會影響你的Docker建構器會以何種語法來解析Dockerfile，如不指定會依Dockerfile前端的綁定版本，大部份的人會解析指令為docker/dockerfile:1，這會讓[BuildKit](https://docs.docker.com/build/buildkit/)使用最新的語法來解析。

```shell
# syntax=docker/dockerfile:1
```

<h4>Base Image</h4>

下面指令定義了要使用那一個基礎image:
```shell
FROM ubuntu:22.04
```
<h4>Environment setup</h4>

下面指令在Base image裡執行安裝套件的指令，其中的# install app dependencies代表註釋:
```shell
# install app dependencies
RUN apt-get update && apt-get install -y python3 python3-pip
```
<h4>Installing dependencies</h4>

下面指令安裝了python的相關套件:
```shell
RUN pip install flask==3.0.*
```

<h4>Copying files</h4>

下面指令手土竹女表示將本機的hello.py檔案複製到image的/目錄下:
```shell
COPY hello.py /
```
<h4>設定環境變數</h4>

如果你的應用程式有用到環境變數，也可以聲明:
```shell
ENV FLASK_APP=hello
```

<h4>暴露port號</h4>

聲明你的應用程式將用那一個port，雖然這是非必要的，但卻是一個好習慣:
```shell
EXPOSE 8000
```

<h4>啟動應用程式</h4>

下面以"exec from"方式來執行指令:
```shell
CMD ["flask", "run", "--host", "0.0.0.0", "--port", "8000"]
```
也可以用"shell from"方式來執行:
```shell
CMD flask run --host 0.0.0.0 --port 8000
```
這二種的差異可參考[Shell and exec form](https://docs.docker.com/reference/dockerfile/#shell-and-exec-form)

<h4>建置</h4>

要開始建置使用下列指令:
```shell
docker build -t test:latest .
```
其中-t test:latest表示image的name和tag，而 . 代表了在當前目錄去找Dockerfile，建置完成後用下列指令來啟動container:
```shell
docker run -p 127.0.0.1:8000:8000 test:latest
```
這代表了本機的port 8000對應到了你的應用程式的port 8000