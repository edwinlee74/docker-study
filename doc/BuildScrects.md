<h3 align=center>建置秘鑰</h3>
所謂建置秘鑰是建置過程中像密碼或token這類的敏感資訊，而這些資訊不應最終生成在你的image裡，相反地，應該使用mount方式來暴露你的秘鑰較為安全。

<h4>秘鑰掛載</h4>

要在Dockerfile中掛載秘鑰，可以用下列方式:
```shell
RUN --mount=type=secret,id=mytoken \
    TOKEN=$(cat /run/secrets/mytoken) ...
```

要直接用build來傳遞秘鑰，可用docker build --secret :

```shell
docker build --secret id=mytoken,src=$HOME/.aws/credentials .
```

