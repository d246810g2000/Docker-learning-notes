這裡會使用兩個file(Dockerfile, docker-compose.yml)，當進行docker compose volumn的示範：

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

在這邊，多創建了一個container叫myweb5，同時掛載在一個客製的volumn(mainpage-vol002)，寫法為`<volumn_name>:<container_directory>`

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
 myweb5:  # 這是container的名稱
  image: uopsdod/myweb:latest  # 直接從現有的image，啟動一個container
  ports:
   - "8084:80"
  networks:
   - mybridge002
  volumns:
   - mainpage-vol002:/var/www/localhost/htdocs/  # 將volumn空間(mainpage-vol002和container的空間htdocs進行串接) 

networks:  # networks和services是屬於同一層級 
 mybridge001:  # 不要懷疑，要創造自訂的bridge network，這樣寫就完成了
 mybridge002: 

volumns:
 mainpage-vol002:

```
