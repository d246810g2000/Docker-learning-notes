
創建一個docker compose的yaml檔案

```bash
touch docker-compose.yml
```

在這邊，撰寫configuration，讓docker compose知道怎麼創建這個image.

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
```

注意，這時在同一個目錄下，會有兩個file，一個是Dockerfile，一個是docker-compose.yml
下面是此次示範用的Dockerfile：

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

接下來，要利用新的方法，docker compose來建立image. 

```bash
docker-compose build --no-cache  # --no-cache 就會讓它每次更新時，都重跑一次，不會有些沒有更新到
```

接著，使用docker-compose來啟動這個container.

```bash
docker-compose up -d  # 讓它在背景執行
```

使用docker compose，把所有的container下架。

```bash
docker-compose down 
```

docker compose好用的地方在於，它可以在yaml file當中，一次啟動多個不同的container.

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
```

接著同樣使用`docker-compose build --no-cache`，就會看到三種images被創造出來了。

也可以使用`docker-compose up -d`，就會看到三個container都被啟動了。

再來學另外一種docker-compose yaml的寫法：

在這裡，特別注意第四個container - myweb4的寫法，它是直接從一個既有的image，啟動container。

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

### 利用docker-compose的方式，將compose yaml檔案裡面定義的所以images上傳到hub

原本要push image，需要一個個image，進行下面的操作：

`docker push tomlin918/image-name01`<br/>
`docker push tomlin918/image-name02`<br/>
`docker push tomlin918/image-name03`<br/>

現在則可以簡化為下面的寫法：

`docker-compose push`

同樣地，如果要一次把所以的docker images pull下來，也可以寫成：

`docker-compose pull`


