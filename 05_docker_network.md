
列印出docker有的網路模式：
```bash
docker network ls
```

裡面的driver會有：
- bridge
- host
- null (也就是none)

### none模式

Argument介紹：
- `--network`：指定network的模型
- `--name`：給定network的名稱
- `<executable command>`：讓container可以在背景程式一直執行的指令，例如`tail -f /dev/null`

```bash
docker run -d --network none --name none-mode <image_name> <executable command>
```

之後再執行`docker container ls`就會看到這個container_id，會有一個NAMES命名為none-mode. 接著再看看none模式下所有的container：

```bash
docker network inspect none  # 只看none模式下的containers
```

在none模式下，這個container是無法和外界溝通的。

```bash
docker exec -it <container_id> /bin/sh

# 進入container的prompt之後
ip addr ls  # 表示列出這個container的ip

ping 8.8.8.8  # 測試看能否從這個container，連上外網google的server
```

### bridge模式

如果要自己建立一個新的bridge的網路模式

- `--drive`：這邊是指定要使用的drive模式

```bash
docker network create --drive bridge <my_bridge_name>

docker network ls  # 這時就會看到自己創建的bridge模式的network
```

建立一個container，它的network是設定到特定的bridge上頭。

```bash
docker run -d --network <my_bridge_name> --name <container_name> <image_name> tail -f /dev/null

docker network inspect <my_bridge_name>
# 在裡面，可以專注兩個點：
# 一個是IPAM，寫的是這個network的ip
# 一個是它旗下有哪些containers
```

因為不同的bridge network，旗下的container是不能互相溝通的，所以如果有一個container，想要再賦予它另外一個bridge的ip，可以用下面的指令：

```bash
docker network connect <my_bridge_name> <container_name>  # 讓這個container有my_bridge_name的子ip，才可以和裡面其它的container溝通
```

### container模式

如果我們要copy一個container的network模式給另外一個container，可以用下面的語法：

- `--network container:<container_name>`：透過container:container_name，讓這個新創的container，有和<container_name>一樣的network設定，它會有一樣的ip位址，而且使用`docker network inspect <my_bridge_name>`會看不到這個新的container在裡面，因為它與另外一個<container_name>共享network設定。

```bash
docker run -d --network container:<container_name> --name <this_container_name> <image_name> tail -f /dev/null 
```

### host模式

```bash
docker run -d --network host --name <my_container_name> <my_image_name>

docker network inspect host

# 進入container的shell裡面
docker exec -it <container_id> /bin/sh
ip addr ls
# 大約在第eth1這個就是它的ip，也等於是這個Linux VM的ip
# 它和附屬的Linux VM共享同一個network設定
# 如果要查詢這個container對外開放的port為何？
netstat -tulpn
```

大家可以發現，在host模式下，在創建這個container時，不需要指定port(`-p xxxx:80`)，因為host會將這個container的port，直接拉到Linux VM下面，因此如果我們要再連到這個Linux VM下面的這個container時，只需要輸入下面的位址：

```bash
echo $(docker-machine ip)  # 得到Linux VM的ip

# 在瀏覽器上面輸入這個位址後，就可以連到Linux VM下面的那個container
xxx.xxx.xxx.xx:80 (80為舉例)
# 而不是以往的
xxx.xxx.xxx.xx:8081 (8081為舉例)
```

