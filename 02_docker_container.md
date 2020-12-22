### docker container 的用法

學員們若對於port mapping部分提到的VM Linux與Container環境有疑惑，可以直接先跳到「Dockerfile 建立與使用 觀念解說」單元，裡面就會詳細講解Docker的三大空間概念。

此外，這邊也特別說明一下Linux 的 8081 port是我們自行選的，Container 的 80 port是我們image裡的apache server規定使用的 。總流程如下：

本地主機瀏覽器 -> VM Linux IP:8081 -> Container IP:80

```bash
docker container ls
docker container ls -a  # 多了-a，會把沒有啟動的container也列出來
docker container run --name <container_name> <image_name>
```

### 簡化版寫法

```bash
docker run --name <container_name> <image_name>
```

更簡化版寫法：

```bash
# 這樣寫，系統會自動給這個image一個container名稱
# URL: https://docs.docker.com/engine/reference/run/#name---name
# The UUID identifiers come from the Docker daemon. 
# If you do not assign a container name with the --name option, 
# then the daemon generates a random string name for you.
docker run <image_name> 
```


### interactive mode

```bash
# URL: https://phoenixnap.com/kb/docker-run-command-with-examples
# Docker allows you to run a container in interactive mode. 
# This means you can execute commands inside the container while it is still running.
# By using the container interactively, you can access a command prompt inside the running container. 
# To do so, run the following command
# docker container run -it [docker_image] /bin/bash
docker run  -it --name <container_name> <image_name> /bin/sh
```

### detached mode

```bash
# If you want to keep the container and current terminal session separate, 
# you can run the container in the background using the -d attribute. 
# Using detached mode also allows you to close the opened terminal session without stopping the container.
docker run -d --name <container_name> <image_name> tail -f /dev/null

# 在detaced mode，如果又要進入它的shell prompt
docker exec -it <container_id> /bin/sh
```

### 指定連結port端口

```bash
# 抓網上nginx這個伺服器的最新版本
docker pull nginx:latest
# 這邊的8081是指本地的port端口，要對到nginx這個伺服器image預設的80端口
docker run -d -p 8081:80 --name <container_name> <image_name>

# 想要知道docker machine的ip位址
echo $(docker-machine ip)
```

### 停止與刪除container

```bash
docker container stop <container_id>
docker container rm <container_id>
```

