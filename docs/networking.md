<h3 align=center>Networking</h3>
容器網路是指容器彼此之間或其他負載溝通連線能力。

容器預設具有網路，使他們能對外連線，一個容器不會有資訊是關於附加在它所連線上的網路，或者其端點是否為Docker 工作負載。容器只會看到網卡上的IP、DNS、Gateway、Routing table等資訊，除非你使用none network drivder。

<h4>使用者定義網路</h4>
你可以自訂一個使用者定義網路，並在同一網路上連線多個容器，一旦連線上這個使用者定義網路，彼此間可以透過容器IP或名稱來通信。

下面使用bridge network driver範例:
```shell
$ docker network create -d bridge my-net
$ docker run --network=my-net -itd --name=container3 busybox
```

<h4>Drivers</h4>
預設會下列幾個Driver:

|     Driver      |                     描述                      |
| ----------------| --------------------------------------------- |
|     bridge      |  預設的網路Driver                             |  
|     host        |  刪除容器和 Docker 主機之間的網路隔離         |
|     none        |  將容器與主機和其他容器完全隔離               |
|     overlay     |  覆蓋網路將多個 Docker daemons連接在一起      |
|     ipvlan      |  IPvlan 網路提供對 IPv4 和 IPv6 網址的完全控制|
|     macvlan     |  為容器分配 MAC 位址                          |

<h4>容器網路</h4>
為了使用使用者定義網路，你可以直接附加一個容器到另一個容器網路堆疉上，使用--network container: name|id flag 格式。

下列flag不支援使用container:的網路模式:

* --add-host
* --hostname
* --dns
* --dns-search
* --dns-option
* --mac-address
* --publish
* --publish-all
* --expose
以下範例執行一個Radis容器，該容器綁定到localhost，之後再透過redis-cli去連線到
之後再透過redis-cli去連線到redis容器。
```shell
$ docker run -d --name redis example/redis --bind 127.0.0.1
$ docker run --rm -it --network container:redis example/redis-cli -h 127.0.0.1
```
<h4>公開port</h4>
使用--publish 或 -p來公開容器對外服務的port number:

|   旗標值   |                                             描述                                                                                 |
|------------| ---------------------------------------------------------------------------------------------------------------------------------|
| -p 8080:80 | 對應從主機8080 port到容器的TCP 80port                                                                                            |
| -p 192.168.1.100:8080:80 | 對應192.168.1.100 IP主機的8080 port 到	192.168.1.100 IP主機的80 port                                               |
| -p 8080:80/udp   |	對應主機上8080 port到容器的udp 80 port                                                                                  |
|-p 8080:80/tcp -p 8080:80/udp | 將 Docker 主機上的 TCP 8080 port對應到容器中的 TCP 80 port，並將 Docker 主機上的 UDP 8080 port對應到容器中的 UDP 80連接埠|	

<h3>注意:</h3>如果你只是要容器連到另一個容器使用bridge方式即可，無需公開port較為安全。

<h4>IP 位址和主機名</h4>
預設情況下，容器會獲取它連接到的每個 Docker 網路的 IP 位址。 容器從網路的IP子網接收IP位址。 Docker daemon為容器執行動態子網劃分和IP位址分配。 每個網路還具有預設submask和閘道。

可以將正在運行的容器連接到多個網路， 要麼在創建容器時多次傳遞--network flag， 或將docker network connect命令用於已在運行的容器。 在這兩種情況下，都可以使用 --ip 或 --ip6 flag來指定容器在該特定網路上的 IP 位址。

同樣，容器的主機名在 Docker 中預設為容器的 ID。 您可以使用 --hostname覆蓋主機名。 當使用docker network connect連接到現有網路時， 可以使用該--alias flag為該網路上的容器指定其他網路別名。

<h4>DNS 服務</h4>
預設情況下，容器使用與主機相同的 DNS 伺服器，但您可以用--dns覆蓋它。

預設情況下，容器繼承配置檔中定義的 DNS 設置。 附加到預設網路的容器將收到此檔的副本。 附加到自定義網路的容器使用 Docker 的嵌入式 DNS 伺服器。 嵌入式 DNS 伺服器將外部 DNS 尋找轉發到主機上設定的 DNS 伺服器。/etc/resolv.confbridge

您可以使用用於啟動容器的 or 命令的標誌來設定每個容器的 DNS 解析。 下表描述了與 DNS 相關的可用標誌 配置。docker rundocker createdocker run

|     旗標	  |                            描述                            |
|-------------|------------------------------------------------------------|
|  --dns	  |DNS 伺服器的IP位址，若要指定多個 DNS 伺服器，請使用多個--dns。如果容器無法訪問您指定的任何IP位址，則它會使用Google的公共 DNS 8.8.8.8伺服器。這允許容器解析 Internet 域|
| --dns-search|	用於搜索非完全限定主機名的 DNS 搜尋域。若要指定多個 DNS 搜尋前置綴，請使用多個標誌。--dns-search|
| --dns-opt   |	表示 DNS 選項及其值的鍵值對。有關有效選項，請參閱作業系統的resolv.conf文件|
| --hostname  |	 容器用於自身的主機名。如果未指定，則預設為容器的ID。      |