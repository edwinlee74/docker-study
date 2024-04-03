<h4 align=center>Docker Compose</h4>

Docker Compose是一種可用來定義多個容器應用程式執行的工具。

Compose 簡化了對整個應用程式堆疊的控制，使你可以在單個易於理解的YAML設定檔中輕鬆管理服務、網路和volume。然後，只需一個命令，即可創建並啟動所有服務從你的設定檔。

Compose 適用於所有環境;生產、暫存、開發、測試，以及 CI 工作流。它還具有用於管理應用程式的整個生命週期的命令：

* 啟動、停止和重建服務
* 查看正在運行的服務狀態
* 串流傳輸正在運行的服務的日誌輸出
* 在服務上運行一次性命令

<h4>安裝</h4>
如果按照先前的Docker Engine安裝，那麼已經裝好了docker-compose-plugin這個套件，檢查一下它的版本:

```shell
root@cacti-svr:~# docker compose version
Docker Compose version v2.25.0
```

<h4>Docker Compose如何運作</h4>
Docker Compose依賴YAML設定檔，通常名為compose.yaml。

compose.yaml檔遵行[Compose Specification](https://compose-spec.io/)所提供的標準，是正式[Compose Specification](https://github.com/compose-spec/compose-spec)的實作。

### Compose 應用程式模型

* #### service: 應用程式的運算元件定在此。
* #### network: 服務彼此間藉由網路來溝通。
* #### volumes: 定義服務所要存放永久性資料的地方。
* #### config:  定義服務的設定值。
* #### secret:  定義敏感性資料。

<h4>Compose 檔案</h4>
預設Compose 檔案會去從工作目錄中查找compose.yaml或compose.yml，對於早期版本也支援docker-compose.yaml或docker-compose.yml相後相容。

多個compose 檔也可合併在一起，詳見[Working with multiple Compose files](https://docs.docker.com/compose/multiple-compose-files/)。

<h4>範例說明</h4>

下列範例針對上面概念說明，這個是非標準的。

一個應用程式可以分為前端、後端服務，前端在執行時間使用由基礎架構管理的HTTP設定檔進行配置，提供外部網域名稱以及由平台的安全性秘鑰儲存注入的HTTPS伺服器憑證，後端則將資料儲存在持久性volume中。

兩個服務在隔離的後端網路上相互通信，而前端也連接到前端網路並公開連接埠443供外部使用。
![example](../img/compose-application.webp)

範例應用程式由以下部分組成：

2 個服務，由 Docker 映像支援：webapp和database
1 個秘鑰（HTTPS 證書），注入前端
1個配置（HTTP），注入前端
1 持久卷，附加到後端
2 網路

```shell
services:
  frontend:
    image: example/webapp
    ports:
      - "443:8043"
    networks:
      - front-tier
      - back-tier
    configs:
      - httpd-config
    secrets:
      - server-certificate

  backend:
    image: example/database
    volumes:
      - db-data:/etc/data
    networks:
      - back-tier

volumes:
  db-data:
    driver: flocker
    driver_opts:
      size: "10GiB"

configs:
  httpd-config:
    external: true

secrets:
  server-certificate:
    external: true

networks:
  # The presence of these objects is sufficient to define them
  front-tier: {}
  back-tier: {}
```