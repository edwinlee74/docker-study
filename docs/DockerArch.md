<h1 align=center>Docker architecture</h1>

Docker採用了client-server架構, Docker client會向Docker daemon交談, 由它來建置、執行、並分發你的容器，Docker client和Docker daemon可在同一台主機上，也可以連向遠端的Docker daemon, 他們彼此之間是籍由REST API來溝通的，另一個Docker client是Docker Compose, 這可以讓將一組的container來組成你的應用程式。

![Docker architecture](../img/docker-architecture.webp)
<h4 align=center>Docker架構圖</h4>

<h3>Docker daemon</h3>
Docker daemon(dockerd) 負責聆聴來自Docker API請求和管理Docker的images、containers、networks以及volumes等物件，同時它也會和其他daemon來管理Docker服務。

<h3>Docker client</h3>
Docker client是使用者與Docker互動的主要方法，當你使用docker run這樣的指令時，client將這些指令送給dockerd，這些指令透過Docker API，Docker client可與多個daemon互相溝通。

<h3>Docker Desktop</h3>
Docker Desktop是一款易於安裝的應用程式，適用於 Mac、Windows 或 Linux 環境，使您能夠構建和共用容器化應用程式和微服務。Docker Desktop 包括 Docker dae,pm、Docker client、Docker Compose、Docker Content Trust、Kubernetes 和 Credential Helper。

<h3>Docker registries</h3>
Docker registry是用於存放Docker images，Docker Hub是公用的儲存庫，任何人都可存取，你也可以使用私人的registry。

<h3>Docker objects</h3>
使用Docker時，會包含許多像是images、containers、networks、volumes、plugin和其他等物件。

<h3>Images</h3>
一個image時唯讀並建立container的基礎，並且通常一個image會建立在另一個image之上，例如:你使用一個ubuntu image為基底, 再將你的AP和相關library給包含進去。

<h3>Containers</h3>
一個container是一個可運行image的實例，你可以透過Docker API或CLI來建立、啟動、停止、刪除它。