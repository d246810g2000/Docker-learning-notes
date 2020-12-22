
注意，volumn是儲存在Linux VM Disk之下，而不是在container disk之下。

```bash
docker volumn ls  # 列出現在有哪些volumn

docker volumn create <volumn_name>

docker volumn inspect <volumn_name>
# 在檢視volumn的資訊中，最重要的是mountpoint
# 這個是Linux VM的路徑，也就是volumn從Linux VM當中拿了一個空間，來當作可以儲存的位置，給container用
```

在掛載container的folder到volumn裡面，所用的寫法：
- `<volumn_name>:<path of container folder>`，這樣也叫把Linux VM Disk的空間和container disk的空間串連起來，之後任何的改動，都會在兩邊出現。

```bash
docker run -d -p 8080:80 -v <volumn_name>:/var/www/localhost/htdocs/ <image_name>
```

另一種寫法，是讓docker daemon幫我們自動建立一個volumn，它會是用亂數表示。

```bash
docker run -d -p 8081:80 -v /var/www/localhost/htdocs/ <image_name>
```

