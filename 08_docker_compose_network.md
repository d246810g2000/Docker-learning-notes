這裡會使用兩個file(Dockerfile, docker-compose.yml)，當進行docker compose network的示範：

```bash
FROM alpine:latest
ENV myworkdir /var/www/localhost/htdocs
ARG whoami=Sam
WORKDIR ${myworkdir}
RUN echo "I am ${whoami}, and this is first line..." >> index.html
RUN echo "I am ${whoami}, and this is second line..." >> index.html
RUN echo "I am ${whoami}, and this is third line..." >> index.html
ENTRYPOINT ["httpd", "-D", "FOREGROUND"]
```


```yaml
# 注意，yaml是用空格來進行indent
version: "3.7"  # 這邊的version，是指compose的版本
services:  # 這裡就是放container的configurations
 myweb:  # 這是container的名稱
  build:
   context: .  # 指Dockerfile所在的目錄位置
   args:  # 就是Dockerfile裡面的argument，可以在這邊進行替代
    whoami: "Tony"
  image: uopsdod/myweb:latest  # build出來的image名稱
  ports:
   - "8080:80"
 myweb2:  # 這是container的名稱
  build:
   context: .  # 指Dockerfile所在的目錄位置
   args:  # 就是Dockerfile裡面的argument，可以在這邊進行替代
    whoami: "Chris"
  image: uopsdod/myweb1:latest  # build出來的image名稱
  ports:
   - "8081:80"
 myweb3:  # 這是container的名稱
  build:
   context: .  # 指Dockerfile所在的目錄位置
   args:  # 就是Dockerfile裡面的argument，可以在這邊進行替代
    whoami: "Jane"
  image: uopsdod/myweb2:latest  # build出來的image名稱
  ports:
   - "8082:80"
 myweb4:  # 這是container的名稱
  image: uopsdod/myweb:latest  # 直接從現有的image，啟動一個container
  ports:
   - "8083:80"
```

當我們使用`docker-compose up`時，docker compose會default創建一個bridge network，而把所有docker-compose.yml裡面的container，放進同一個bridge當中。這個bridge name，預設是當前目錄名字+default。

接著我們來建立客製化network空間：

同樣地，這樣bridge名稱，會是當前目錄名字+自訂的bridge name.

```yaml
# 注意，yaml是用空格來進行indent
version: "3.7"  # 這邊的version，是指compose的版本
services:  # 這裡就是放container的configurations
 myweb:  # 這是container的名稱
  build:
   context: .  # 指Dockerfile所在的目錄位置
   args:  # 就是Dockerfile裡面的argument，可以在這邊進行替代
    whoami: "Tony"
  image: uopsdod/myweb:latest  # build出來的image名稱
  ports:
   - "8080:80"
  networks:
   - mybridge001
 myweb2:  # 這是container的名稱
  build:
   context: .  # 指Dockerfile所在的目錄位置
   args:  # 就是Dockerfile裡面的argument，可以在這邊進行替代
    whoami: "Chris"
  image: uopsdod/myweb1:latest  # build出來的image名稱
  ports:
   - "8081:80"
  networks:
   - mybridge001
 myweb3:  # 這是container的名稱
  build:
   context: .  # 指Dockerfile所在的目錄位置
   args:  # 就是Dockerfile裡面的argument，可以在這邊進行替代
    whoami: "Jane"
  image: uopsdod/myweb2:latest  # build出來的image名稱
  ports:
   - "8082:80"
  networks:
   - mybridge001
 myweb4:  # 這是container的名稱
  image: uopsdod/myweb:latest  # 直接從現有的image，啟動一個container
  ports:
   - "8083:80"
  networks:
   - mybridge002
networks:  # networks和services是屬於同一層級 
 mybridge001:  # 不要懷疑，要創造自訂的bridge network，這樣寫就完成了
 mybridge002: 
```

### 補充

其他網路模式的設定方式，可以參考下列官網：

https://docs.docker.com/compose/compose-file/#network_mode

老師這邊也多提供一個host模式的設定示範(教材-docker-compose-host-mode.yml 請參考下例)。另外，學員們可以結合我們在網路章節學到的概念，搭配這個文件自己試試看其他模式喔。

docker-compose-host-mode的寫法：

```yaml
version: "3.7"
services:
  myweb6:
    build:
      context: .
      args:
        whoami: "Jimmy"
    image: uopsdod/myweb6:latest
    network_mode: "host"
```

