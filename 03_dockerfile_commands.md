# Dockerfile的撰寫架構

```bash
# 要使用的base image
FROM alpine:latest  

# 使用RUN這個指令，它可以調用在base image當中，已經可以使用的指令
# 因此最好base image已經是個linux系統，才可以使用許多linux下的指令
RUN echo "[INFO] I am in the image building process..."
RUN ls -l /

# 預設image啟動時，要執行的指令，記得一常串的指令，要分拆，用雙引號包起來，再放到list當中
ENTRYPOINT ["tail", "-f", "/dev/null"]
```

---

### RUN

重點：每個RUN之間，都是獨立的，因為中間都會啟動一個temporary的docker image，因此參考下面的範例。

```bash
RUN cd /var/www/localhost/htdocs
RUN pwd
```
你會發現，它並沒有跳到那個htdocs資料夾下面，如果要讓兩個RUN都在同一個環境，則需要使用`&&`這個符號。

`RUN cd /var/www/localhost/htdocs && pwd`

或是寫成跨行的型式：

```bash
RUN cd /var/www/localhost/htdocs \
    && pwd
```

---

### ENV

ENV是用來設定環境變數，簡化程式碼的寫法。

簡化前原始寫法：
```bash
RUN cd /var/www/localhost/htdocs \
    && echo "Adding this new line 01 in home page..." >> index.html
RUN cd /var/www/localhost/htdocs \
    && echo "Adding this new line 02 in home page..." >> index.html
RUN cd /var/www/localhost/htdocs \
    && echo "Adding this new line 03 in home page..." >> index.html    
```

簡化後寫法：
```bash
ENV myworkdir /var/www/localhost/htdocs
RUN cd ${myworkdir} \
    && echo "Adding this new line 01 in home page..." >> index.html
RUN cd ${myworkdir} \
    && echo "Adding this new line 02 in home page..." >> index.html
RUN cd ${myworkdir} \
    && echo "Adding this new line 03 in home page..." >> index.html
```

---

### WORKDIR

如果想要更簡化Dockerfile的寫法，可以設定讓每一次temporary container啟動時，都在某一個directory下工作。

```bash
ENV myworkdir /var/www/localhost/htdocs
WORKDIR ${myworkdir}  # container 會跳到預設的working directory
RUN echo "Adding this new line 01 in home page..." >> index.html
RUN echo "Adding this new line 02 in home page..." >> index.html
RUN echo "Adding this new line 03 in home page..." >> index.html
```

---

### ARG 

Arg就是argument的意思，用在當我們想要動態地使用一個變數值時，可以使用這個指令。Arg和Env不同的地方在於，當我們在使用docker build時，我們可以改變Arg的變數值。

```bash
ENV myworkdir /var/www/localhost/htdocs
ARG whoami=Sam
WORKDIR ${myworkdir}
RUN echo "I am ${whoami}, and this is first line..." >> index.html
RUN echo "I am ${whoami}, and this is second line..." >> index.html
RUN echo "I am ${whoami}, and this is third line..." >> index.html
```

當我們在build image時，就會看到Arg的用途，如果我們想把`whoami`的值換掉，可以這樣寫…
```bash
docker build --build-arg whoami=Tony -t <image_name> .
```

---

### COPY

假設我們有一個content.txt檔，內容如下：

```txt
<div>
<h3>My Book List</h3>
<ul>
    <li><a href="#">Die Hard</a></li>
    <li><a href="#">Secret</a></li>
    <li><a href="#">Html 101</a></li>
    <li><a href="#">Kubernetes 202</a></li>
    <li><a href="#">AWS 303</a></li>
</ul>
</div>
```

現在我們要把這個txt檔案，寫入index.html頁面之中，作法如下：
記得`COPY`是把原本在linux VM下面的資料，複製到temporary container的環境之下。

```bash
ENV myworkdir /var/www/localhost/htdocs
ARG whoami=Sam
WORKDIR ${myworkdir}
RUN echo "I am ${whoami}, and this is first line..." >> index.html
RUN echo "I am ${whoami}, and this is second line..." >> index.html
RUN echo "I am ${whoami}, and this is third line..." >> index.html
COPY ./content.txt ./  # 注意，這邊是指從linux VM下面的current directory將txt檔複製到 -> container下面的current working directory
RUN cat ./content.txt >> index.html  # 再把content的資訊，塞到index.html裡面
```










