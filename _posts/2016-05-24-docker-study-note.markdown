---
layout: post
title:  "Docker Study Note"
date:   2016-05-24 09:20:00 +0800
categories: [container, docker]
---

# Docker? #

**Docker**, 他徹底釋放虛擬化的, 讓應用程式的分派、部署和管理都變的前所未有的效率和輕鬆!

Docker誕生2013年初, 為dotCloud的業餘專案。基於Google公司推出的Go語言實作。專案後來加入Linux基金會, 遵從Apache 2.0協議, source code在GitHub進行維護。

Docker專案目標: 實作輕量級的作業系統虛擬化解決方案。Docker的基礎是Linux容器(LXC)等技術。

container是在**作業系統層級上實作虛擬化**, **直接使用本地主機的作業系統**, 而**傳統方式則是在硬體層面實作**。

# 為什麼要使用Docker? #

Docker對系統資源的使用率很高, 一台主機可以同時執行數千個Docker容器。

container除執行其中應用外, 基本不消耗額外的系統資源, 使得應用效能很高, 同時系統資源消耗更少。傳統虛擬機方式執行10個不同應用就要啟動10個虛擬機, 而Docker只需啟動10個隔離的應用即可。

## 更快速的交付和部署 ##

對DevOps來說, 最希望一次建立或設定, 可在任意一個地方正常執行。

開發者可使用一個標準的映象檔建立一套開發容器, 開發完成後, 維運人員可直接使用這個容器來部署程式碼。Docker可快速建立容器, 快速迭代應用程式, 並讓整個過程清晰可見, 使其他團隊成員容易理解應用程式如何建立和工作的。

Docker容器輕快, 啟動時間秒級, 大量節省開發、測試、部署時間。

## 更有效率的虛擬化 ##

Docker容器執行不須額外虛擬化支援, 他是**核心層級的虛擬化**, 因此可實作更高的效能和效率。

## 更輕鬆的遷移和擴展 ##

Docker幾乎可在任一平台運行, 實體機、虛擬機、公有雲、私有雲、個人電腦、伺服器等。這樣的兼容性可讓使用者把一個應用程式從一個平台遷移到另一個。

## 更簡單管理 ##

使用Docker, 只須小小修改, 就可替代以往大量的更新工作。所有修改都以增量的方式被分發和更新, 從而實作自動化並且有效率的管理。

# 基本概念 #

Docker包括三個基本概念:

- 映像檔(Image)
- 容器(Container)
- 倉庫(Repository)

理解這三個概念, 就理解整個Docker生命週期。

# Docker映像檔 #

Docker映像檔就是個唯讀模板。

e.g.一個映像檔可包含一個完整的ubuntu作業系統環境, 裡面僅安裝了Apache或使用者需要的其他應用程式。

映像檔可用來建立Docker容器。

Docker提供一個很簡單的機制來建立映像檔或更新現有映像檔, 使用者甚至可直接從其他人那裏下載一個已經做好的映像檔來直接使用。

# Docker 容器 #

Docker利用容器來執行應用。

**容器是從映像檔建立的執行實例**。它可被啟動、開始、停止、刪除。每個容器是互相隔離的、保證安全的平台。

可把容器看成一個簡易版的Linux環境(包括root使用者權限、程式空間、使用者空間和網路空間等)和在其中執行的應用程式。

*映像檔是唯讀的, 容器在啟動時建立一層可寫層作為最上層。

# Docker 倉庫 #

倉庫是集中存放映像檔檔案的場所。有時候會把倉庫和倉庫註冊伺服器(Registry)混為一談, 並不嚴格區分。實際上, 倉庫註冊伺服器上往往存放多個倉庫, 每個倉庫又包含多個映像檔, 每個檔案有不同tag。

倉庫分為

- 公開倉庫(Public)
- 私有倉庫(Private)

兩種形式。

最大公開倉庫是Docker Hub, 存放數量龐大的映像檔供使用者下載。大陸的公開倉庫包括Docker Pool等, 可供大陸使用者更穩定快速存取。

使用者也可在本地網路建立一個私有倉庫。

當使用者建立自己的映像檔後就可使用`push`命令將他上傳到公有或私有倉庫, 這樣下次在另一台使用這個映像檔時候, 只需從倉庫上pull下來就可以了。

# Ubuntu 14.04 系列安裝 Docker #

## 透過Docker套件庫安裝最新版本 ##

要安裝新的Docker版本, 首先需要安裝`apt-transport-https`支援, 之後透過新增套件庫來安裝。 

	$ sudo apt-get install apt-transport-https
	$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	$ sudo bash -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
	$ sudo apt-get update
	$ sudo apt-get install -y lxc-docker

## 快速安裝法(建議使用) ##

	$ curl -sSL https://get.docker.com/ubuntu/ | sudo sh

(沒試過...)

# CentOS7 系列安裝 Docker #

CentOS7系統`CentOS-Extras`庫中已內建Docker, 可直接安裝:

	$ sudo yum install docker

安裝後啟動Docker, 並讓它隨系統啟動自動載入

	$ sudo service docker start
	$ sudo chkconfig docker on

# Docker 映像檔 #

Docker執行容器前需要本地端存在的映像檔, 如果映像檔不存在本地端, Docker會從映像檔倉庫下載(預設Docker Hub公共註冊伺服器的倉庫)

# 取得映像檔 #

使用`docker pull`

	$sudo docker pull ubuntu:12.04
    12.04: Pulling from library/ubuntu
    5b09daf4d35a: Pull complete
    250127028a29: Pull complete
    5d399287b49a: Pull complete
    3669ee93dc6d: Pull complete
    ce60cb8863fa: Pull complete
    Digest: sha256:b04374f49de4fb1cbfe98b304c7e64a4a78a2a8b9ba83d3481e33b3d54653849
    Status: Downloaded newer image for ubuntu:12.04
    
下載過程中, 會輸出取得映像檔的每一層訊息。

該命令實際上相當於: `$ sudo docker pull registry.hub.docker.com/ubuntu:12.04`命令, 即從註冊伺服器`registry.hub.docker.com`中的`ubuntu`倉庫來下載標記為`12.04`的映像檔。

有時官方倉庫註冊服務器下載較慢, 可從其他倉庫下載。其他倉庫下載需指定完整的倉庫伺服器位址。

	$sudo docker pull dl.dockerpool.com:5000/ubuntu:12.04

(才給我超慢...)

完成後, 即可隨時使用該映像檔了, 例如建立一個容器, 讓其中執行bash。

	ubuntu@ip-172-31-15-54:~$ sudo docker run -ti ubuntu:12.04 /bin/bash
	root@d1eb1a91bfc2:/#

# 列出本機映像檔 #

使用`docker image`

	ubuntu@ip-172-31-15-54:~$ sudo docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	ubuntu              latest              17b6a9e179d7        2 weeks ago         120.7 MB
	ubuntu              14.04               f6e25e99cf98        2 weeks ago         187.9 MB
	ubuntu              trusty              f6e25e99cf98        2 weeks ago         187.9 MB
	ubuntu              precise             ce60cb8863fa        2 weeks ago         138.5 MB
	ubuntu              12.04               ce60cb8863fa        2 weeks ago         138.5 MB

可看到幾段文字

- 來自哪個倉庫
- 映像檔標記
- ID(唯一)
- 建立時間
- 映像檔大小

其中映像檔`ID`唯一標示映像檔, 注意到有些是相同映像檔, 所以同`ID`。

`TAG`用來標記同倉庫的不同映像檔。通過`TAG`來發行版本。利用下面的命令指定使用映像檔ubuntu:14.04啟動容器

	ubuntu@ip-172-31-15-54:~$ sudo docker run -ti ubuntu:14.04 bash
	root@f7b6d4b6c412:/#

如果沒指定`TAG`, 預設使用`latest`。

# 建立映像檔 #

建立映像檔有多種方法, 使用者可從Docker Hub取得已有映像檔並更新, 也可在本機建立一個。

## 修改已有映像檔 ##

    ubuntu@ip-172-31-15-54:~$ sudo docker run -ti training/sinatra /bin/bash
    Unable to find image 'training/sinatra:latest' locally
    latest: Pulling from training/sinatra

    d634beec75db: Pull complete
    27fb5491e391: Pull complete
    8e3415728a3f: Pull complete
    630b03963440: Pull complete
    962115fdbb58: Pull complete
    9ea38b02c228: Pull complete
    e20166048ece: Pull complete
    8b16a891bd1a: Pull complete
    Digest: sha256:03fc0cd265cbc28723e4efd446f9f2f37b4790cf9cc12f1b9203c79fb86b6772
    Status: Downloaded newer image for training/sinatra:latest
    root@07f1abc83f1c:/# gem install json
    Fetching: json-1.8.3.gem (100%)
    Building native extensions.  This could take a while...
    Successfully installed json-1.8.3
    1 gem installed
    Installing ri documentation for json-1.8.3...
    Installing RDoc documentation for json-1.8.3...
    root@07f1abc83f1c:/# exit
    exit

現在容器已被改變, 使用`docker commit`命令來提交更新後的副本。

	ubuntu@ip-172-31-15-54:~$ sudo docker commit -m "Added json gem" -a "Docker Newbee" 07f1 ouruser/sinatra:v2
	bf80c42c7bcfffd14b94fa7ac53c553b9eb40e56754b48aa18ee94cd46459974

`-m`: 表示提交的說明信息; `-a`: 可指定更新的使用者信息; 之後是用來建立映像檔的容器ID; 最後指定新映像檔的名稱與tag。建立成功會印出新映像檔ID。

使用`docker images`查看新建立的映像檔。

	ubuntu@ip-172-31-15-54:~$ sudo docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	ouruser/sinatra     v2                  bf80c42c7bcf        4 minutes ago       452.4 MB
	ubuntu              latest              17b6a9e179d7        3 weeks ago         120.7 MB
	ubuntu              14.04               f6e25e99cf98        3 weeks ago         187.9 MB
	ubuntu              trusty              f6e25e99cf98        3 weeks ago         187.9 MB
	ubuntu              precise             ce60cb8863fa        3 weeks ago         138.5 MB
	ubuntu              12.04               ce60cb8863fa        3 weeks ago         138.5 MB
	training/sinatra    latest              8b16a891bd1a        23 months ago       447 MB

之後, 可使用新的映像檔來啟動容器

    ubuntu@ip-172-31-15-54:~$ sudo docker run -ti ouruser/sinatra:v2 /bin/bash
    root@61257544dd76:/#

## 利用Dockerfile建立映像檔 ##

使用`docker commit`擴展一個映像檔比較簡單, 但不方便在一個團隊中分享。我們可用`docker build`來建立一個新的映像檔。為此, 首先須建立一個Dockerfile, 裡面包含一些用來建立映像檔的指令。

新建一個目錄和一個Dockerfile

	root@61257544dd76:/# mkdir sinatra
	root@61257544dd76:/# cd sinatra/
	root@61257544dd76:/sinatra# touch Dockerfile

Dockerfile中每一條指令都會建立一層映像檔, 例如:

    ubuntu@ip-172-31-15-54:~$ mkdir sinatra
    ubuntu@ip-172-31-15-54:~$ cd sinatra/
    ubuntu@ip-172-31-15-54:~/sinatra$ vi Dockerfile
    ubuntu@ip-172-31-15-54:~/sinatra$ cat Dockerfile
    # This is a comment
    FROM ubuntu:14.04
    MAINTAINER Docker Newbee <newbee@docker.com>
    RUN apt-get -qq update
    RUN apt-get -qqy install ruby ruby-dev
    RUN gem install sinatra

Dockerfile基本語法是:

- 使用`#`來註釋
- `FROM`指令告訴Docker使用哪個映像檔作為基底
- 接著是維護者信息
- `RUN`開頭指令會在建立中執行, 比如安裝一個套件, 在這裡使用apt-get安裝一些套件

完成Dockerfile後可使用`docker build`建立映像檔。

	$ sudo docker build -t="ouruser/sinatra:v2" .
	Uploading context  2.56 kB
	Uploading context
	Step 0 : FROM ubuntu:14.04
	 ---> 99ec81b80c55
	Step 1 : MAINTAINER Kate Smith <ksmith@example.com>
	 ---> Running in 7c5664a8a0c1
	 ---> 2fa8ca4e2a13
	Removing intermediate container 7c5664a8a0c1
	Step 2 : RUN apt-get -qq update
	 ---> Running in b07cc3fb4256
	 ---> 50d21070ec0c
	Removing intermediate container b07cc3fb4256
	Step 3 : RUN apt-get -qqy install ruby ruby-dev
	 ---> Running in a5b038dd127e
	Selecting previously unselected package libasan0:amd64.
	(Reading database ... 11518 files and directories currently installed.)
	Preparing to unpack .../libasan0_4.8.2-19ubuntu1_amd64.deb ...
	Setting up ruby (1:1.9.3.4) ...
	Setting up ruby1.9.1 (1.9.3.484-2ubuntu1) ...
	Processing triggers for libc-bin (2.19-0ubuntu6) ...
	 ---> 2acb20f17878
	Removing intermediate container a5b038dd127e
	Step 4 : RUN gem install sinatra
	 ---> Running in 5e9d0065c1f7
	. . .
	Successfully installed rack-protection-1.5.3
	Successfully installed sinatra-1.4.5
	4 gems installed
	 ---> 324104cde6ad
	Removing intermediate container 5e9d0065c1f7
	Successfully built 324104cde6ad

其中`-t`標記添加tag, 指定新的映像檔的使用者信息。"."是Dockerfile所在路徑(當前目錄), 也可以換成具體的Dockerfile路徑。

可看到build指令後執行的操作。他要做的第一件事就是上傳這個Dockerfile內容, 因所有操作都要依據Dockerfile來進行。然後, Dockerfile中的指令被一條條執行。每一步都建立了一個新的容器, 在容器中執行指令並提交修改(就跟之前的`docker commit`一樣)。當所有指令都執行完畢以後, 返回了最終的映像檔id。所有中間步驟所產生的容器都會被刪除和清理。

*注意一個映像檔不能超過127層

此外, 還可利用`ADD`命令複製本地檔案到映像檔; 用`EXPOSE`命令向外部開放埠號; 用`CMD`命令描述啟動後執行的程序等。例如:

    # put my local web site in myApp folder to /var/www
    ADD myApp /var/www
    # expose httpd port
    EXPOSE 80
    # the command to run
    CMD ["/usr/sbin/apachectl", "-D", "FOREGROUND"]

現可利用新建立的映像檔啟動一個容器

	$ sudo docker run -ti ouruser/sinatra:v2 /bin/bash
	root@8196968dac35:/#

還可用`docker tag`命令修改映像檔標籤

	$ sudo docker tag 5db5f8471261 ouruser/sinatra:devel
	$ sudo docker images ouruser/sinatra
	REPOSITORY          TAG     IMAGE ID      CREATED        VIRTUAL SIZE
	ouruser/sinatra     latest  5db5f8471261  11 hours ago   446.7 MB
	ouruser/sinatra     devel   5db5f8471261  11 hours ago   446.7 MB
	ouruser/sinatra     v2      5db5f8471261  11 hours ago   446.7 MB

## 從本機匯入 ##

要從本機匯入一個映像檔, 可使用OpenVZ(容器虛擬化的先鋒技術)的模板來建立: OpenVZ模板下載位置: [https://download.openvz.org/template/precreated/](https://download.openvz.org/template/precreated/)。

比如, 先下載一個ubuntu-14.04的映像檔, 之後使用以下命令匯入

	ubuntu@ip-172-31-15-54:~/sinatra$ wget https://download.openvz.org/template/precreated/ubuntu-14.04-x86_64-minimal.tar.gz
	ubuntu@ip-172-31-15-54:~/sinatra$ sudo cat ubuntu-14.04-x86_64-minimal.tar.gz | sudo docker import - ubuntu:14.04-min
	bf799f534b271e83e63426468541dc487030e83772aad628f02533092b529eaa


然後查看新匯入映像檔:

	ubuntu@ip-172-31-15-54:~$ sudo docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	ubuntu              14.04-min           bf799f534b27        3 hours ago         215.4 MB

## 上傳映像檔 ##

使用者可以透過`docker push`指令, 把自己建立的映像檔上傳到倉庫中來共享。例如, 使用者在Docker Hub上完成註冊後, 可推送自己的映像檔到倉庫中。

    $ sudo docker push ouruser/sinatra

    The push refers to a repository [ouruser/sinatra] (len: 1)
    Sending image list
    Pushing repository ouruser/sinatra (3 tags)

# 儲存和載入映像檔 #

## 儲存映像檔 ##

可使用`docker save`命令

	ubuntu@ip-172-31-15-54:~$ sudo docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	ubuntu              14.04-min           bf799f534b27        3 hours ago         215.4 MB
	ouruser/sinatra     devel               bf80c42c7bcf        6 hours ago         452.4 MB
	ouruser/sinatra     v2                  bf80c42c7bcf        6 hours ago         452.4 MB
	ubuntu              latest              17b6a9e179d7        3 weeks ago         120.7 MB
	ubuntu              trusty              f6e25e99cf98        3 weeks ago         187.9 MB
	ubuntu              14.04               f6e25e99cf98        3 weeks ago         187.9 MB
	ubuntu              precise             ce60cb8863fa        3 weeks ago         138.5 MB
	ubuntu              12.04               ce60cb8863fa        3 weeks ago         138.5 MB
	training/sinatra    latest              8b16a891bd1a        23 months ago       447 MB
	ubuntu@ip-172-31-15-54:~$ sudo docker save -o ubuntu_14.04.tar ubuntu:14.04

## 載入映像檔 ##

可使用`docker load`從建立本地檔案中再匯入到本地映像倉庫:

	ubuntu@ip-172-31-15-54:~/sinatra$ sudo docker load --input ubuntu_14.04.tar

or

	ubuntu@ip-172-31-15-54:~/sinatra$ sudo docker load < ubuntu_14.04.tar

這將匯入映像檔以及相關元資料信息(包括標籤等)。