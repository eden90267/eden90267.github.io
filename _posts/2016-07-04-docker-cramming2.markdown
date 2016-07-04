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

