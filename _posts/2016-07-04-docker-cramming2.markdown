---
layout: post
title:  "Docker cramming2"
date:   2016-07-04 15:35:00 +0800
categories: [docker]
---

# Docker惡補2 #

準備參加2016 DevOps summit大會, 對於Docker的理論與實作還不是很熟悉, 所以藉此文章惡補一下, 僅提供個人認為需要補的內容...

原文: [https://www.gitbook.com/book/joshhu/dockercommands/details](https://www.gitbook.com/book/joshhu/dockercommands/details "全面易懂的Docker指令大全")

# 本章重點 #

docker指令主要分四大類, 分別是:

- Linux系統相關指令
- Docker Registry相關指令
- 映像檔相關指令
- Continer相關指令

指令標準如下:

	docker [選項] 指令 [參數1...] [參數2...] [參數3...]

- [選項]: 設定Docker本身執行環境的選項, 屬於進階部分
- 指令: Docker的指令集, 本章重點
- [參數...]: 伴隨指令集的參數, 也是Docker的精華

e.g.

	$ docker run -d --name web -p 8080:80 joshhu/webdemo

- empty, no options
- `run`, command
- `-d --name web -p 8080:80 joshhu/web`, run arguments

# 基礎系統及Docker Hub指令 #

## 基礎指令 ##

- `和Linux系統有關的指令`: 介紹Docker的資訊。事實上Docker的設定十分複雜, 會在之後介紹更多有關Docker在Linux的環境設定
- `和Registry有關的指令`: 主要針對公開的官方Docker Hub幾個指令查詢, 後續有關自行建立私有的Docker Registry會有完整架設

## 和系統有關的指令及參數 ##

### info ###

列出和系統相關的資訊, 如映像檔數、Container數、檔案系統目錄、Linux核心版本、使用Linux版本、CPU及記憶體等。

*用途: 用來檢查系統是否能正確執行Docker所有指令*

	ubuntu@ip-172-31-19-185:~$ sudo docker info
	Containers: 0
	 Running: 0
	 Paused: 0
	 Stopped: 0
	Images: 0
	Server Version: 1.11.2
	Storage Driver: aufs
	 Root Dir: /var/lib/docker/aufs
	 Backing Filesystem: extfs
	 Dirs: 0
	 Dirperm1 Supported: false
	Logging Driver: json-file
	Cgroup Driver: cgroupfs
	Plugins:
	 Volume: local
	 Network: null host bridge
	Kernel Version: 3.13.0-74-generic
	Operating System: Ubuntu 14.04.4 LTS
	OSType: linux
	Architecture: x86_64
	CPUs: 1
	Total Memory: 992.5 MiB
	Name: ip-172-31-19-185
	ID: CKES:RMVS:IE2Y:V7QR:6QLL:3YON:VWKR:IB6I:MNNU:DZIU:EUIP:5XF5
	Docker Root Dir: /var/lib/docker
	Debug mode (client): false
	Debug mode (server): false
	Registry: https://index.docker.io/v1/
	WARNING: No swap limit support

### version ###

列出目前Docker的版本, 以及Go語言的版本等

*用途: 常用來檢查是否要升級到較高的Docker版本。*

	ubuntu@ip-172-31-19-185:~$ sudo docker version
	Client:
	 Version:      1.11.2
	 API version:  1.23
	 Go version:   go1.5.4
	 Git commit:   b9f10c9
	 Built:        Wed Jun  1 21:47:50 2016
	 OS/Arch:      linux/amd64

	Server:
	 Version:      1.11.2
	 API version:  1.23
	 Go version:   go1.5.4
	 Git commit:   b9f10c9
	 Built:        Wed Jun  1 21:47:50 2016
	 OS/Arch:      linux/amd64

### Docker的環境變數 ###

一個執行Docker的Linux系統其中有許多有關Docker的環境變數設定, 之後會說明。

Docker的環境變數十分強大

## Docker Hub相關指令 ##

### login/logout

	$ sudo docker login
	Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
	Username: eden90267
	Password:
	Login Succeeded

若有自行架設私有的Docker映像檔資料庫, 則輸入該資料庫的位址:

	sudo docker login 192.168.1.100:8000

一般使用Docker映像檔, 是完全不需要密碼的, 只有上傳映像檔才需要

### search ###

最好用是`-s`參數, 用來找評價較高(星級)的映像檔。

	ubuntu@ip-172-31-19-185:~$ sudo docker search -s 10 nginx
	NAME                                     DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
	nginx                                    Official build of Nginx.                        3404      [OK]
	jwilder/nginx-proxy                      Automated Nginx reverse proxy for docker c...   700                  [OK]
	richarvey/nginx-php-fpm                  Container running Nginx + PHP-FPM capable ...   220                  [OK]
	jrcs/letsencrypt-nginx-proxy-companion   LetsEncrypt container to use with nginx as...   75                   [OK]
	million12/nginx-php                      Nginx + PHP-FPM 5.5, 5.6, 7.0 (NG), CentOS...   68                   [OK]
	maxexcloo/nginx-php                      Docker framework container with Nginx and ...   57                   [OK]
	webdevops/php-nginx                      Nginx with PHP-FPM                              42                   [OK]
	h3nrik/nginx-ldap                        NGINX web server with LDAP/AD, SSL and pro...   27                   [OK]
	bitnami/nginx                            Bitnami nginx Docker Image                      18                   [OK]

# Docker映像檔相關指令 #

## 映像檔基礎 ##

### Docker Hub的個人映像檔名稱 ###

	<user name>/<repo name>:<tag name>

e.g.

	joshhu/webdemo:ubuntu14

## 映像檔指令參數種類 ##

`images`, `pull`, `load`, `save`, `push`, `rmi`, `tag`, `pull`, `build`

### `docker images` ###

常用參數:

- `-a`: 列出完整的映象檔層次資訊。每個映像檔是由不同層次組成, 會再稍後說明
- `-q`: 只列出映像檔ID。這在做映像檔批次處理時很方便
- `-tree`: 官方文件已經沒有這個參數, 但還是可以用。列出映像檔不同層次之間的樹狀關係。

### 下載映像檔`docker pull` ###

要將某一個倉庫的所有映像檔都下載回來, 可使用`-a`參數。

### 將映像檔存入/匯出電腦檔案格式`docker save/load` ###

Docker的映像檔雖然名為檔案, 但其格式十分複雜, 不知存哪, 也不知什麼格式, 如果想要和其他人交換, 又不想Registry, 就可用這兩個參數存成`tarball`格式及匯出。存入別忘加`-o`參數, 要不只會顯示不會真的壓縮。

	$ sudo docker save -o webdemou12.tar joshhu/webdemo
	$ ls -al
	total 248696
	drwxr-xr-x 5 ubuntu ubuntu      4096 Jul  4 09:24 .
	drwxr-xr-x 3 root   root        4096 Jul  4 02:15 ..
	-rw------- 1 ubuntu ubuntu       557 Jul  4 05:44 .bash_history
	-rw-r--r-- 1 ubuntu ubuntu       220 Apr  9  2014 .bash_logout
	-rw-r--r-- 1 ubuntu ubuntu      3683 Jul  4 03:24 .bashrc
	-rw-rw-r-- 1 ubuntu ubuntu      1766 Jul  4 03:24 .bashrc_docker
	drwx------ 2 ubuntu ubuntu      4096 Jul  4 02:17 .cache
	drwx------ 2 root   root        4096 Jul  4 07:59 .docker
	-rw-r--r-- 1 ubuntu ubuntu       675 Apr  9  2014 .profile
	drwx------ 2 ubuntu ubuntu      4096 Jul  4 02:15 .ssh
	-rw------- 1 root   root   254619136 Jul  4 09:24 webdemou12.tar

將`tarball`還原回映像檔格式, 則用`load`。

	$ sudo docker load --input webdemou12.tar
	$ sudo docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
	joshhu/webdemo      latest              c58bf6158823        15 months ago       243.2 MB


### 刪除映像檔`docker rmi` ###

這指令刪除本機中存放的映像檔。但如果有容器還在使用則無法刪除。可用`-f`硬刪。

前面提到映像檔是以層次的方式來存放, 因此一個映像檔會有多個層次。可以下`--no-prune=true`這個參數, 只殺掉有`tag name`的映像檔

以一個標準的映像檔來說, 只會殺掉最上面一層, 因為建立時的其他中間層次並不會有tag name, 這樣做的好處是可以留下許多映像檔共用的母層次。**這也是Docker映像檔指令中, 唯一能處理到層次這個等級的參數了**

	$ sudo docker rmi --no-prune=true joshhu/webdemo:u12
	Untagged: joshhu/webdemo:u12
	Deleted: sha256:d8ce897e63110d0145a7d0ca4d9463088704a39cd9badb66ef163c51f8aa4bda
	Deleted: sha256:7a9b60d7b5f9b1afd4bfd6083cb29ab065e5a8f129bfa10622952044afcc2318
	Deleted: sha256:b42b8bce4c059521de7b9a4e18d2e883c220f7d14861215a7622a20be2290086
	Deleted: sha256:af82cfdf1757b62d3420540ab9b1907233e43f922ff8f2351f42da0c9ec71921
	Deleted: sha256:52659e6d881a85d1f296cf2b60deff798e2b7f1636d6196ed632248ce3c51b96
	Deleted: sha256:147d7590f091afaaaeb317e047774261078068c967a46a6ed8812d8e5b2f96bc
	Deleted: sha256:46e45b47b3964ba46c697f32b392ec17911f92fb0c5faf867b6edb5d043e3a4a
	Deleted: sha256:981ea0c266788e62c878e1e57c3dd03b9e39dec157a1ad3d318b6d2a24b8fd99
	Deleted: sha256:f74a0d2ecabdcf40442d448ca02a6e8095f93881c06af9c1e2e3fb2ed2696a06
	Deleted: sha256:ad22369352e5fbea9227391a352b7bb504f132a0c040bb154e53136680975a05
	Deleted: sha256:ba29e844e94b324ab20229b18c952a92635d5900ebfe0dc02204685e3b444712
	Deleted: sha256:cbc1d4a64b93a993b2b0964ad0a18919d18c0ef6b49e085067a20239e1bab9ef
	Deleted: sha256:c2b2ff0ac5d7b4cb561c670a0f6b6f742a6eb1b5da95cb65109a6b7c711f841a
	Deleted: sha256:e3c89f89278ee08ae34e7a642d1ed10558765710c5e630ba44599c3a885f2449
	$ sudo docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
	joshhu/webdemo      latest              c58bf6158823        15 months ago       243.2 MB

### 一次刪掉所有映像檔 ###

可配合Linux的批次指令來一次清乾淨所有映像檔, 輸入

	$ sudo docker rmi -f $(sudo docker images -aq)
	Untagged: joshhu/webdemo:u12
	Deleted: sha256:d8ce897e63110d0145a7d0ca4d9463088704a39cd9badb66ef163c51f8aa4bda
	Deleted: sha256:7a9b60d7b5f9b1afd4bfd6083cb29ab065e5a8f129bfa10622952044afcc2318
	Deleted: sha256:b42b8bce4c059521de7b9a4e18d2e883c220f7d14861215a7622a20be2290086
	Deleted: sha256:af82cfdf1757b62d3420540ab9b1907233e43f922ff8f2351f42da0c9ec71921
	Deleted: sha256:52659e6d881a85d1f296cf2b60deff798e2b7f1636d6196ed632248ce3c51b96
	Deleted: sha256:147d7590f091afaaaeb317e047774261078068c967a46a6ed8812d8e5b2f96bc
	Deleted: sha256:46e45b47b3964ba46c697f32b392ec17911f92fb0c5faf867b6edb5d043e3a4a
	Deleted: sha256:981ea0c266788e62c878e1e57c3dd03b9e39dec157a1ad3d318b6d2a24b8fd99
	Deleted: sha256:f74a0d2ecabdcf40442d448ca02a6e8095f93881c06af9c1e2e3fb2ed2696a06
	Deleted: sha256:ad22369352e5fbea9227391a352b7bb504f132a0c040bb154e53136680975a05
	Deleted: sha256:ba29e844e94b324ab20229b18c952a92635d5900ebfe0dc02204685e3b444712
	Deleted: sha256:cbc1d4a64b93a993b2b0964ad0a18919d18c0ef6b49e085067a20239e1bab9ef
	Deleted: sha256:c2b2ff0ac5d7b4cb561c670a0f6b6f742a6eb1b5da95cb65109a6b7c711f841a
	Deleted: sha256:e3c89f89278ee08ae34e7a642d1ed10558765710c5e630ba44599c3a885f2449
	Error response from daemon: conflict: unable to delete c58bf6158823 (cannot be forced) - image is being used by running container 5333aedb1bc0

### 替本機映像檔加標籤名稱`docker tag` ###

	$ sudo docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
	joshhu/webdemo      u12                 d8ce897e6311        15 months ago       288 MB
	joshhu/webdemo      latest              c58bf6158823        15 months ago       243.2 MB
	$ sudo docker tag joshhu/webdemo:latest joshhu/webdemo:u14
	$ sudo docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
	joshhu/webdemo      u12                 d8ce897e6311        15 months ago       288 MB
	joshhu/webdemo      latest              c58bf6158823        15 months ago       243.2 MB
	joshhu/webdemo      u14                 c58bf6158823        15 months ago       243.2 MB

### 自建映像檔`docker build/history` ###

`docker build`指令可以從現成的映像檔為基礎, 自行建立全新的映像檔, 而`docker history`則會列出製作的每一部過程。之後會討論其完整實作。

### 上傳映像檔`docker push` ###

	docker push <username>/<repo name>:<Tag name>

# Container入門篇 #

Container是一個極輕量的「類」VM, 而Docker又是更輕量的Container

## Container概念 ##

Container是Docker的靈魂

## Container的生命週期 ##

針對一個服務或軟體的宿主而言, 伺服器、VM和Container的分別如下：

- 對伺服器來說, **活著**, 就是電源打開, 記憶體載入作業系統或軟體, 並且開始消耗CPU, 記憶體等系統資源的電腦, 沒有開機的電腦就是**死**的電腦
- 對虛擬化平台來說, **活著**的VM, 就是一個被Hypervisor分配到資源(CPU, 記憶體), 並且開始真正消耗實體伺服器資源的VM, 因為VM本身並沒有所謂真正的開機關機, 一個沒有消耗實體機器資源的VM, 就是**死**的VM
- 對Container來說, 沒有執行`docker run/create`之前, **Container根本不存在**, 更沒有所謂**活著或死掉**的問題。執行`docker run/create`之後, Container才會誕生, 才會開始消耗系統資源(由Linux核心提供), 就是**活著**。執行完畢的Container, 就是**死**的Container, 但是並沒有消失, 還是存在的。要執行`docker rm <container>`指令後, Container才會消失。

## Container指令基礎 ##

Docker對Container指令是最多樣的, 而光一個docker run就有非常多的參數。

這邊列出Docker有關Container指令的分類方便查詢, **粗體**表示常用指令, 會有講解說明。

### Container和映像檔之間的操作 ###

- commit: 將Container的改變存入映像檔
- export: 將Container存成快照
- import: 將Container快照恢復成映像檔

### Container執行時的操作 ###

- **create: 建立Container並執行指令**
- **run: 同create**
- kill: 刪除執行中的Container, **但Container還是存在, 只是死了**
- **rm: 刪除Container(停止或運行中都行)**, Container就從這世上消失了
- pause: 暫停執行中的Container, **仍暫有記憶體停, 服務不中斷**
- unpause: 恢復暫停中的Container
- stop: 停止執行中的Container, **但不暫有記憶體, 服務中斷**
- start: 啟動停止中的Container
- restart: 重新啟動Container
- wait: 讓Container暫停直到Container停止為止
- rename: 更名Container

### Container的狀態 ###

- **inspect: 檢查Container的狀態(非常常用)**
- **stats: 查看Container的CPU、記憶體及網路使用**
- **port: 查看Container的通訊埠使用**
- **ps: 查看Container的使用狀態**
- **top: 查看Container在主系統中的記憶體使用**
- **dip: 查看Container的IP**
- **dpid: 查看Container的pid**

### Container執行時的操作 ###

- **attach: 連接Container的標準輸入輸出端**
- **exec: 在外部向Container內執行指令**
- **denter: 進入Container**
- logs: 將Container內的輸出顯示到螢幕上

### Container和主系統之間的操作 ###

- cp: 複製Container內的檔案到主系統中
- diff: 列出兩個Container之間檔案系統差異
- events: 列出某個時間點之前或之後的事件

### 和Linux的指令配合使用 ###

Docker的執行常常和Linux內部指令配合使用, 如`grep`, `awk`, `xargs`等, 會在需要的時候提及。下面例子就是列出所有Container的狀態(docker stats預設只會列出指定Container的狀態)

	$ sudo docker ps | sudo awk 'NR>1 {print $NF}' | xargs sudo docker stats

	$ sudo docker stats $(sudo docker ps | sudo awk 'NR>1 {pring $NF}')
	
## 快速建立你的第一個Docker服務 ##

	$ sudo docker run busybox echo "hello world"
	hello world
	
### Docker建立的網頁服務 ###

使用一個沒有執行指令, 而是單純啟動Container。其實這個Container並不是沒有執行程式, 而是**預先設定好的啟動程式**已經設定在這個Container的來源映像檔joshhu/webdemo中了

	$ sudo docker run -d --name web -p 8080:80 joshhu/webdemo
	
- `run`: 標準Docker建立Container並執行指令
- `-d`: run指令的無數值參數, 背景執行
- `--name web`: run指令的文字參數, 指定這個Container的名字為web
- `-p 8080:80`: run指令的數值參數, 把主機的8080通訊埠所有流量轉發到web這個Container的80通訊埠
- `joshhu/webdemo`: run指令的文字參數, 使用`joshhu/webdemo`來填入web這個Container

Docker用指令加參數就可以完成99%的動作, 非常方便

### 建立並啟動Container: `docker run/create` ###

	$ sudo docker run busybox echo "hello world"
	hello world
	
短短一個指令, 其實已經過了Container的建立、執行、停止等步驟。

執行完之後, Container就停止, **但還是存在！！**

### 查看Container: docker ps ###

加-a參數, 列出所有Container, 包括已經執行結束的(即死掉的Container)。

	$ sudo docker ps -a
	CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS                      PORTS                  NAMES
	0f40a93c10bb        busybox             "echo 'hello world'"   34 minutes ago      Exited (0) 34 minutes ago                          elegant_noyce
	02dbd8980278        busybox             "echo 'hello world'"   34 minutes ago      Exited (0) 34 minutes ago                          kickass_liskov
	5333aedb1bc0        joshhu/webdemo      "/start.sh"            6 hours ago         Up 6 hours                  0.0.0.0:8080->80/tcp   web
	
`sudo docker ps`只會列出正在執行中的docker。那如何讓一個Container能一直執行下去, 不要死掉或消失？

## 執行中的Container ##

### 永遠執行的container ###

在Docker不加參數情況下, 要讓Container一直執行下去, 就是執行一個無止盡的程式, 如ping localhost

1. 執行一個不會結束的程式, 如ping localhost:

		$ sudo docker run --name test busybox ping localhost
		PING localhost (127.0.0.1): 56 data bytes
		64 bytes from 127.0.0.1: seq=0 ttl=64 time=0.044 ms
		64 bytes from 127.0.0.1: seq=1 ttl=64 time=0.056 ms
		64 bytes from 127.0.0.1: seq=2 ttl=64 time=0.051 ms
		64 bytes from 127.0.0.1: seq=3 ttl=64 time=0.048 ms
		64 bytes from 127.0.0.1: seq=4 ttl=64 time=0.055 ms
		...
		
2. 此時再開啟一個終端視窗, 並執行docker ps, 就可看到正在執行中的Container, 這就是一個標準的「活」的Container

		$ sudo docker ps
		CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
		1e396326d3a8        busybox             "ping localhost"    35 seconds ago      Up 35 seconds                              test
		
事實上, 要讓Container能持續執行下去, 除了Container本身去執行一個永遠不停止的命令之外, 也可以使用`docker run`本身附帶的參數來進行。

### 給Container標準輸入輸出裝置 ###

Container可被視為一台獨立的電腦, 因此當然可以有`鍵盤`及`螢幕`。但這邊所謂的鍵盤螢幕, 就是linux的輸入裝置stdin及輸出裝置stdout。在docker指令如下:

- **-t: attach時Container的螢幕會接到原來的螢幕上**
- **-i: attach時鍵盤輸入會被Container接手**

`docker run`時這兩個參數同時都加, 這個Container就具備了標準的輸入(你目前鍵盤)和輸出(你目前螢幕)

	$ sudo docker run -ti --name test busybox
	/ # ls
	bin   dev   etc   home  proc  root  sys   tmp   usr   var
	
### 讓Container在背景執行 ###

大部分Docker應用程式都是以服務方式執行, 不太需要進入這個Container的命令提示符號操作, 此時可以加`docker run`後輸入`-d`參數, 表示是Detached模式, 或稱之為"Daemonized"的方式執行。

	$ sudo docker run -d busybox
	9de174a52264d6188444258650e7ea17a6d24151330aa33e5f2b257e5781be74
	ubuntu@ip-172-31-19-185:~$ sudo docker ps
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
	
背景沒有這個Container執行？ 因為在啟動時, 並沒有一個**長期駐留記憶體的服務**。

**開機駐留記憶體的服務**, 下列幾個就是符合這個前提的條件:

1. 該Container在產生並啟動時, 來源的映像檔就有一個開機自動執行長駐的服務, 而且這個Container被我們使用-d參數丟入背景。

		sudo docker run -d joshhu/webdemo
		750232967dfdd82027e1d04045d8130f4a199fc51712a67a8928a0121e700f7a
		ubuntu@ip-172-31-19-185:~$ sudo docker ps
		CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
		750232967dfd        joshhu/webdemo      "/start.sh"         5 seconds ago       Up 4 seconds        80/tcp                 serene_easley
		
	可看到這個Container在啟動時, 執行了star.sh, 這檔案就是讓Container一直執行的服務, 在建立映像檔時就建立好了
	
2. 手動對Container**執行一個不停止的服務**並把Container丟入背景執行:

		$ sudo docker run -d busybox ping localhost
		
3. 用-d參數將Container放入背景, 並讓他保持基本輸入或輸出的能力, 就是加入參數`-dti`

		$ sudo docker run -dti busybox
		
## Container內部及外部的執行 ##

### 進入執行中Container內部 ###

#### denter ####

denter不是Docker的指令, 而是前面章節匯入的script, 只要輸入denter <CONTAINER NAME>即可。

	$ sudo denter web
	root@0941e3055a30:~#
	
#### docker attach ####

`docker attach`是用來「監管」Container用的。使用docker attach一個Container後, 就會進入這個Container的操作終端命令列, 但是之前執行docker run的參數, 離開這個Container時, 會中止Container的執行。

- 使用`-d`或`-di`參數: 離開Container時該Container停止
- 使用`-td`參數: 離開Container時該Container繼續在背景

### 在外部執行Container內的程式 ###

#### docker logs ####

這個指令直接讀出Container內的輸出到主機的螢幕上。e.g.

	$ sudo docker run -d --name printtime busybox /bin/sh -c "while true; do date; sleep 1; done"
	1dc47c422ede7824ca6cc1931f6177e7849ad712444a48a03c1b06c30d8060c4
	$ sudo docker logs 1dc
	Mon Jul  4 16:15:08 UTC 2016
	Mon Jul  4 16:15:09 UTC 2016
	Mon Jul  4 16:15:10 UTC 2016
	Mon Jul  4 16:15:11 UTC 2016
	Mon Jul  4 16:15:12 UTC 2016
	Mon Jul  4 16:15:13 UTC 2016
	Mon Jul  4 16:15:14 UTC 2016
	Mon Jul  4 16:15:15 UTC 2016
	...
	
#### docker exec ####

這指令很簡單, 就是在外部向執行中的Container內部下指令, 此時會呼叫Container內部的shell城市來執行你下的指令, 而不管你之前啟動Docker時給Container的指令, 兩個沒有關係。

	$ sudo docker exec printtime ps
	PID   USER     TIME   COMMAND
	    1 root       0:00 /bin/sh -c while true; do date; sleep 1; done
	  290 root       0:00 sleep 1
	  291 root       0:00 ps
	  
就像上例一樣, 我們雖在執行`docker run`時讓這個Container執行一個無限印出系統時間的指令, 但使用`docker exec`時, 也是在Container內另啟動一個`/bin/sh`的shell來執行`ps`