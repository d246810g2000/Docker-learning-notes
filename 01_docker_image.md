
### 課程範例中會使用到的images

- alpine：輕量化的Linux OS
- nginx：輕量化的網站伺服器
- apache：重量的網站伺服器

### 在Linux上安裝Docker Toolbox

安裝好docker toolbox後，會在macbook的application裡面，出現一個docker的資料夾，在資料夾中，會有一個docker quickstart terminal，可以使用。

```bash
sudo yum install docker -y  # yum 用來安裝套件
sudo docker --version

sudo service docker start # service 指令，啟動特定服務
sudo service docker stop 
```

### 下載docker image

在image名稱後的`:`是指版本的tag.
通常image名稱如果沒有*<人名>/*, 就表示是官方的image.

```bash
docker pull uopsdod/course-image-first:v4
docker images
```

### 建立docker image

docker預設的讀取dockerfile的方法，要求file的名稱，一定要是Dockerfile，並且沒有附檔名。更多的資訊，可以參考這個連結：https://stackoverflow.com/a/42810735

通常會建立一個資料夾，下方放這個Dockerfile，之後就可以用docker build的方式，把image建立起來。

- `-t`：就是給image這個名稱
- `.`：這個.就是表示在當下這個目錄下，非常重要，很容易被忽略

```bash
docker build -t uopsdod/course-image-build .

# 進階：在build的時候，給予build arguments
docker build -t uopsdod/course-image-build002 --build-arg my_name_is="Tom Cruise" .
```

### 上傳docker image到docker hub

```bash
docker login
docker push uopsdod/course-image-build002
```

### 清理docker image

- `docker rmi <image_name or image_id>`

但是前提要求這個image沒有被任何container使用，所以如果有container在用，則需要先將這個container刪掉

```bash
docker rm <container_id>  # 記得rm 和 rmi兩個是不同的指令
docker rmi <image_name>

docker rmi -f <image_name>  # 使用-f來強制刪掉image
```








