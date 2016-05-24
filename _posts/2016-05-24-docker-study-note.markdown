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