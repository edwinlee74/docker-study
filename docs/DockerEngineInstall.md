<h1 align=center>Docker Engine安裝</h1>

安裝Docker最簡單的方法是安裝Docker Desktop，可參考[官網](https://docs.docker.com/engine/install/)，但如果僅僅只是要運行容器，可以只裝Docker Engine，要注意的是Docker本身不提供支援Docker Engine而是將它做為Docker Desktop的其中一個元件，Docker Engine是一個open source專案，由[Moby project](https://mobyproject.org/)社群來維護。


<h3>安裝</h3>

要將Docker Engine安裝在linux中，各版本的linux套件管理方式不同，請參考[官網](https://docs.docker.com/engine/install/)，這裡會以Ubuntu來做為安裝說明。

<h4>OS 需求</h4>

* Ubuntu 須為64 bit版本。
* Ubuntu Mantic 23.10
* Ubuntu Jammy 22.04 (LTS)
* Ubuntu Focal 20.04 (LTS)
同時也相容 x86_64 (or amd64), armhf, arm64, s390x, and ppc64le (ppc64el)這幾個架構。

<h4>使用apt方式安裝</h4>

將Docker 的apt repository加進列表
```shell
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

安裝相關套件
```shell
$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

測試安裝是否成功
```shell
$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete
Digest: sha256:6352af1ab4ba4b138648f8ee88e63331aae519946d3b67dae50c313c6fc8200f
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
..........................................................................
..........................................................................
..........................................................................
```