<h3 align=center>Multi-stage builds</h3>
多階段建構是用來優化你的Dockerfile，並使它更易於維護。

<h4>使用多階段建構</h4>
要使用多階段建構，可以在Dockerfile中加入多個FROM，每一個FROM都代表一個階段建構。

以下的Dockerfile共有二個階段，第一階段建構生成二進制檔，而第二階段則是將第一階段生成的二進制檔複製過來。

```shell
# syntax=docker/dockerfile:1
FROM golang:1.21
WORKDIR /src
COPY <<EOF ./main.go
package main

import "fmt"

func main() {
  fmt.Println("hello, world")
}
EOF
RUN go build -o /bin/hello ./main.go

FROM scratch
COPY --from=0 /bin/hello /bin/hello
CMD ["/bin/hello"]
```
只需要一個Dockerfile，無須其他建置腳本，直接docker build:
```shell
docker build -t hello .
```
最終產生的image只會有一個可執行的二進制檔含在裡面，而第一階段的建置工具則不會留在裡面。

<h4>命名你的建構階段</h4>
預設你的階段名稱無須命名，而是以整數從0開始的流水號方式指定，然而你也可以使用 as 命名你的建構階段:

```shell
# syntax=docker/dockerfile:1
FROM golang:1.21 as build
WORKDIR /src
COPY <<EOF /src/main.go
package main

import "fmt"

func main() {
  fmt.Println("hello, world")
}
EOF
RUN go build -o /bin/hello ./main.go

FROM scratch
COPY --from=build /bin/hello /bin/hello
CMD ["/bin/hello"]
```

<h4>指定建構階段</h4>
當你建構image時，你可以不用建構全部階段，如上面的Dockerfile中，你可以指定只建構build階段:

```shell
docker build --target build -t hello .
```
這在以下場景特別有用:

1. 針對某個階段除錯時。
2. 使用一個debug階段，用來開啟除錯工具或符號，另一個則為production階段。
3. 使用testing階段來獲取測試資料，另一個則為production用以真實資料。

<h4>使用外部image作為階段建構</h4>
可以使用copy --from來複製一個個別image:

```shell
COPY --from=nginx:latest /etc/nginx/nginx.conf /nginx.conf
```

<h4>使用上一階段作為新的階段</h4>


```shell
# syntax=docker/dockerfile:1

FROM alpine:latest AS builder
RUN apk --no-cache add build-base

FROM builder AS build1
COPY source1.cpp source.cpp
RUN g++ -o /binary source.cpp

FROM builder AS build2
COPY source2.cpp source.cpp
RUN g++ -o /binary source.cpp
```

<h4>舊式建構器與BuildKit差異</h4>
舊式建構器會處理所有Dockerfile中直到--target階段為止，即使該階段與選擇階段沒有相依關係，而BuildKit則只會建構相依階段:

```shell
# syntax=docker/dockerfile:1
FROM ubuntu AS base
RUN echo "base"

FROM base AS stage1
RUN echo "stage1"

FROM base AS stage2
RUN echo "stage2"
```
如上面的Dockerfile，BuildKit指定stage2階段時，只有base以及stage2階段會處理，因為不相依stage1，所以會跳過。

```shell
DOCKER_BUILDKIT=1 docker build --no-cache -f Dockerfile --target stage2 .
[+] Building 0.4s (7/7) FINISHED                                                                    
 => [internal] load build definition from Dockerfile                                            0.0s
 => => transferring dockerfile: 36B                                                             0.0s
 => [internal] load .dockerignore                                                               0.0s
 => => transferring context: 2B                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                0.0s
 => CACHED [base 1/2] FROM docker.io/library/ubuntu                                             0.0s
 => [base 2/2] RUN echo "base"                                                                  0.1s
 => [stage2 1/1] RUN echo "stage2"                                                              0.2s
 => exporting to image                                                                          0.0s
 => => exporting layers                                                                         0.0s
 => => writing image sha256:f55003b607cef37614f607f0728e6fd4d113a4bf7ef12210da338c716f2cfd15    0.0s
```

如果是舊式建構器則會所有建構階段都會處理:
```shell
DOCKER_BUILDKIT=0 docker build --no-cache -f Dockerfile --target stage2 .
Sending build context to Docker daemon  219.1kB
Step 1/6 : FROM ubuntu AS base
 ---> a7870fd478f4
Step 2/6 : RUN echo "base"
 ---> Running in e850d0e42eca
base
Removing intermediate container e850d0e42eca
 ---> d9f69f23cac8
Step 3/6 : FROM base AS stage1
 ---> d9f69f23cac8
Step 4/6 : RUN echo "stage1"
 ---> Running in 758ba6c1a9a3
stage1
Removing intermediate container 758ba6c1a9a3
 ---> 396baa55b8c3
Step 5/6 : FROM base AS stage2
 ---> d9f69f23cac8
Step 6/6 : RUN echo "stage2"
 ---> Running in bbc025b93175
stage2
Removing intermediate container bbc025b93175
 ---> 09fc3770a9c4
Successfully built 09fc3770a9c4
```