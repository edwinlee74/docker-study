<h1 align=center>Docker architecture</h1>

Docker採用了client-server架構, Docker client會向Docker daemon交談, 由它來建置、執行、並分發你的容器，Docker client和Docker daemon可在同一台主機上，也可以連向遠端的Docker daemon, 他們彼此之間是籍由REST API來溝通的，另一個Docker client是Docker Compose, 這可以讓將一組的container來組成你的應用程式。

![Kubernetes cluster 元件](../img/docker-architecture.webp)
<h4 align=center>Docker架構圖</h4>

<h3>Docker daemon</h3>

<h3>Docker client</h3>

<h3>Docker desktop</h3>

<h3>Docker registries</h3>

<h3>Docker objects</h3>

<h3>Images</h3>

<h3>Containers</h3>