---
layout: post
title:  "Linux LPIC-1 Study Note(CH12)"
date:   2016-07-25 16:43:00 +0800
categories: [linux, lpic]
---

# Chapter 12: 網路與安全設定 #

網路部分：基本網路概念問題，以及Linux的網路指令與設定檔。

安全部分：系統與網路安全，以及資料加密。

## 基本網路概念 ##

### 常用名詞與基本通訊指令 ###

#### TCP/IP ####

TCP/IP是一種網路通訊協定，他是**網路上的基礎協定**，也是一種**電腦資料打包**和**定址**的標準方法。

底下為TCP封包的Header資訊：

- Source Port
- Destination Port
- Sequence Numver(封包序號)
- Acknowledgment Number(回應序號)
- Data Offset(資料補償)
- Reserved(保留)
- Control Flag(控制旗標)：URG、ACK、PSH、RST、SYN、FIN
- Window(滑動視窗)
- Checksum(確認)
- Urgent Pointer(緊急資料所在位置)
- Option與Padding: 較少使用的欄位

常見的TCP服務包括**telnet**、**SMTP**、**HTTP**等，事實上，大部分的伺服器為了確保資料正確性，都是採用TCP協定。

#### UDP ####

UDP是User Datagram Protocol的簡寫，為用戶資料流協定，他與TCP相似但簡單許多。TCP是可靠的協定，原因是他有**錯誤檢查**與**三段交握**來確認資料的完整性，但UDP是一個「不可靠」的協定，原因是他不能保證資料的正確性。因此若有可靠性要求，則應用程式應避免使用它。

UDP封包header如下：

- UDP Source Port
- UDP Destination Port
- Length
- UDP checksum

常見的UDP服務包括DNS查詢、FTP資料埠的傳輸等。

#### ICMP ####

ICMP是internet control message protocol的簡寫，負責在**IP網路系統**內**傳遞管理**與**錯誤**的訊息。不像TCP與UDP，在ICMP上沒有埠號的資訊。

ICMP的訊息可以分兩類：第一為詢問類，第二為錯誤類，不論哪一類都會送出請求，並等待對方的回應，因此訊息都是雙向的。

常見的ICMP類指令包括ping、traceroute等。

#### TTL ####

TTL是Time To Live的簡寫，也就是封包存活的時間。事實上，它代表的是次數，而不是真正的分秒。

每個封包必定包含TTL這個數值，代表若該封包經過指定的次數的點，如果還沒到達目的地，就自動消失。舉例，封包TTL=63，代表封包經過63個點，若能未到達目的地，則自動消失。

#### 封包資訊 ####

在網路上傳遞資料，都是根據通訊協定來處理，而每一個通訊協定在傳輸資料時，都會將要傳遞的資料拆成小單位的包裏，這樣的包裏就稱為封包。

封包是能夠在網路上面進行傳輸的最小資訊單位。**一個封包包含有發送端節點地址**、**接收端節點地址**、**和這兩個節點之間需要傳送的數據**。

#### 封包過濾 ####

所謂封包過濾，就是針對**封包的Header部分進行資料過濾**，包括來源與目的地、埠號、長度、以及特殊資訊等，過濾的動作包括拒絕、丟棄、轉向等。

封包過濾是一般防火牆必備的功能之一，在Linux上最常用的就是iptables與TCP wrapper。

#### 封包轉址 ####

所謂封包轉址，是將封包轉到本機以外的位址，這樣的功能多半用在**路由器**或是**閘道伺服器**上。這樣的功能可能只是將封包轉向到正確的位址，也有可能變更了部分封包的資訊。

在Linux中，會用來作為封包轉址的工具就是iptables。此外，在Linux中要啟用封包轉送的功能，必須把`/proc/sys/net/ipv4/ip_forward`設為1(default 0)。

#### 網路裝置、網路裝置名稱、網路裝置別名 ####

所謂網路裝置，包含主機板上的網路晶片、一般PCI的網路卡、數據機、光纖裝置等。在Linux系統上，會對這樣的網路裝置賦於一個網路裝置名稱，以RJ45網路線連接的裝置，如網路卡、網路晶片而言，系統會將網路裝置名稱訂為eth0，如果有超過一個的網路裝置，系統會依序訂為eth1、eth2、...；若以撥接裝置，如數據機，系統會將裝置訂為ppp0。

一個網路裝置名稱上會附掛一個網路位址(IP address)，若我們要在一個網路裝置名稱上附掛一個以上的網路位址，就必須使用網路裝置別名了(alias)。

對於一個網路裝置，如eth0，可加上冒號與數字來建立別名，如eth0:0、eth0:1、eth0:2、...。對於不同的別名，我們可以給予不同的網路位址。

#### 網路位址(IP address) ####

在IPv4架構下，網路位置是個32bits的資料，每個bit只有兩種可能：0或1。因此網路位址的分布從32個0到32個1。

00000000000000000000000000000000 到 11111111111111111111111111111111

一般表示法習慣八位加一個點:

00000000.00000000.00000000.00000000 到 11111111.11111111.11111111.11111111

上面是二進位表示法，若改成10進位，則會變成

0.0.0.0到255.255.255.255

由於目前對網路位址的需求日益增加，因此後來產生了新的IPv6架構，容量由原來的32bits增加到128bits。

從0.0.0.0到255.255.255.255，網路位址分為五類：

1. Class A

	網路位址由1.0.0.0到127.0.0.0。

	能擁有一個Class A的網路通常是極大的機構，如國家、教育單位。

2. Class B

	網路位址由128.0.0.0到191.255.0.0。

	能擁有一個Class B的網路通常是大型的機構或學校單位，如大學、政府部門、大型的商業組織

3. Class C

	網路位址由192.0.0.0到223.255.255.0。

	能擁有一個Class C的網路通常是較小型的機構或商業組織，如中小型企業。

4. Class D

	網路位址由224.0.0.0到239.255.255.255，主要為多點傳播，通常使用在特殊的通訊協定上，一般使用不到。

5. Class E

	網路位址由240.0.0.0到255.255.255.255，常用於廣播與**網路遮罩**的使用

#### 子網路遮罩 ####

子網路遮罩的功能就是決定網路位址所在的網路有多大，也就是對於哪些網路位址需使用廣播。若以二進位來看子網路遮罩，必定是以1開頭，若1的右邊出現0，則全部為0。

可透過子網路遮罩判斷網路位址的前幾位是固定的(出現1的部分就是固定的)，並藉此了解網路的大小。

這裡列出Class C以下的子網路遮罩：

11111111.11111111.11111111.11111111=255.255.255.255 <- 0個網路位址

11111111.11111111.11111111.11111110=255.255.255.254 <- 1個網路位址

11111111.11111111.11111111.11111100=255.255.255.252 <- 2個網路位址

11111111.11111111.11111111.11111000=255.255.255.248 <- 4個網路位址

11111111.11111111.11111111.11110000=255.255.255.240 <- 8個網路位址

11111111.11111111.11111111.11100000=255.255.255.224 <- 16個網路位址

11111111.11111111.11111111.11000000=255.255.255.192 <- 32個網路位址

11111111.11111111.11111111.10000000=255.255.255.128 <- 64個網路位址

11111111.11111111.11111111.00000000=255.255.255.0 <- 128個網路位址

※ 經驗談：子網路遮罩能讓我們知道該網段之下有多少IP位址，但實際可用的需要扣掉頭尾的位址，其中第一個位址表**網路**，最後一個為**廣播**，而且通常會有一個是**聯外的閘道位址**，因此可用位址要減3。

#### 網路與廣播 ####

由網路位址與子網路遮罩就可以知道該網路所分配的區段範圍為何，該範圍中的第一個網路位址稱為network，代表整個網路位址，而最後一個網路位址代表廣播，作為廣播通訊之用。

#### 閘道、預設閘道 ####

閘道是一個閘道口，連接內外的網路。作為閘道的主機，可能是一般電腦，可能是IP分享器，也可能是功能較多的交換集線器。

閘道伺服器本身也有網路位址、子網路遮罩、以及閘道等設定，它會將內部網路的封包傳遞到外部網路。

預設閘道是所有主機上必填的網路設定之一(除撥接與動態位址之外)，所有連往區域網路以外的封包，都會被丟到預設閘道上，再由閘道伺服器做後續的處理。

#### 路由表、路由器、多重路由 ####

路由是封包傳遞的路徑，而路由表是記載路由規則的表格。當從A點傳輸資料到B點時，可以傳輸的路徑通常不只一條，而路由表就會記載可以傳輸的路徑。

廣義而言，只要機器會將非本地的封包傳送到正確的目的地，就稱為路由器。但事實上，路由器多半定義了路由表，以便尋找較近的路徑，因此路由器通常是一台獨立的主機，而在Linux上，路由器的功能跟閘道是合一的。

當我們有多條線路，我們會透過路由表定義多重路由，也就是定義不同目的地的路徑規則。舉例，將連網際網路的封包丟給預設閘道，而連往海外公司的封包，則丟給另一台路由器處理。

#### 主機名稱、網域名稱、完整網域名稱 ####

如果主機的DNS對應www.test.com.tw，那麼www為主機名稱，test.com.tw為網域名稱，而www.test.com.tw為完整網域名稱。

主機名稱通常用簡單且符合該主機的功能來命名，如www、mail、ns等；而網域名稱就是我們向ISP註冊的網域名稱，每年需繳500~1500不等；而完整網域名稱就是主機名稱加上網域名稱。

要注意的是，上面的三種名稱都不得以特殊符號命名，允許特殊符號包括「-」與「_」。

#### 網域與子網域 ####

當申請了網域後，可以將網域再往下切割成子網域，子網域的名稱會加在主網域之前，並以「.」做為區隔。e.g. 申請test.com.tw這個網域，可以再將此網域切割為sales.test.com.tw、rd.test.com.tw、account.test.com.tw，並將子網域的命名權交給其他主機。

因此所謂子網域，是定義次層的名稱伺服器，在現實中最常切子網域的應該是學校。

#### http與https ####

一般瀏覽網頁是透過http的通訊協定，並在網址列輸入http://網址。若我們瀏覽加密的網頁時，則會輸入https開頭的網址。

https是http加上ssl(security socket layer)，也就是透過ssl達到加密的效果。在Linux上的ssl套件為openssl，因此要建置含有https服務的伺服器，可透過Apache加上openssl。http使用的埠號為80，而https使用的埠號為443。

#### FTP的命令埠與資料埠 ####

FTP使用命令埠作為命令的傳輸與下達之用，預設值為21；而資料埠是做為資料傳遞之用，預設值為20。

命令埠可讓我們順利連上主機，但要建立檔案的傳輸時，就必須開啟資料埠做資料的傳遞之用。

### 網路相關指令 ###

介紹網路設定與連線查詢的相關指令

#### ifconfig ####

ifconfig是一個查詢與設定網路位址的指令，可以設定網路位址、子網路遮罩與廣播位址。

語法：ifconfig [介面名稱] [參數]

參數如下：

- up：啟動此介面
- down：關閉此介面
- [ -] arp：啟動或是停止此介面上的ARP協定
- [ -] promisc：啟動或是停止此介面上的promisc mode
- [ -] allmulti：啟動或是停止此介面上的all-multicast mode
- metric N：設定介面的長度為N
- mtu N：設定最大的傳輸單位為N
- Netmask 子網路遮罩：設定子網路遮罩
- broadcast廣播位址：設定廣播位址

介面名稱：為網路介面的名稱，通常第一張網路卡為eth0，第二張為eth1...依此類推。

若要顯示eth0裝置的狀態，執行過程如下：

	$ ifconfig eth0
	eth0      Link encap:Ethernet  HWaddr 06:6e:23:ef:45:c5
	          inet addr:172.31.19.185  Bcast:172.31.31.255  Mask:255.255.240.0
	          inet6 addr: fe80::46e:23ff:feef:45c5/64 Scope:Link
	          UP BROADCAST RUNNING MULTICAST  MTU:9001  Metric:1
	          RX packets:828131 errors:0 dropped:0 overruns:0 frame:0
	          TX packets:330809 errors:0 dropped:0 overruns:0 carrier:0
	          collisions:0 txqueuelen:1000
	          RX bytes:818086950 (818.0 MB)  TX bytes:66709269 (66.7 MB)

若要將eth0停用：

	# ifconfig eth0 down

#### ifup ####

ifup是將網路介面啟用的指令，如同ifconfig ... up

語法：ifup [參數] 介面名稱

參數如下：

- -a，--all：啟用所有網路介面
- --force：強迫啟用
- -h，--help
- -V，--version
- -v，--verbose：顯示詳細執行過程

若要啟用網路介面eth0：

	# ifup eth0

#### ifdown ####

ifdown是將網路介面停用的指令，如同ifconfig ... down

語法：ifdown [參數] 介面名稱

參數如下：

- -a，--all：停用所有網路介面
- --force：強迫停用
- -h，--help
- -V，--version
- -v，--verbose：顯示詳細執行過程

若要停用網路介面eth0：

	# ifdown eth0

#### route ####

route是顯示與設定路由表的指令，也就是封包的傳送路徑

語法：route [參數] 內部指令 [內部指令參數]

參數如下：

- -F：儲存核心FIB的route表
- -C：將核心的route快取列出
- -v：詳細顯示執行結果
- -n：以數字顯示，而不做DNS反查
- -e：以netstat編排方式顯示路由狀態

內部指令：

- del | add：刪除 | 增加 指定的路由紀錄
- -net | -host：指定為 網段 | 主機 的位址
- target：目的地位址或網路表示

內部指令參數：

- netmask NM：指定子網路遮罩為NM
- gw IP_addr：指定閘道的IP address為`IP_addr`
- metric M：設定路由表中的長度欄位(metric field)
- mss M：設定TCP最大的區塊長度(Maximum Segment Size)為Mbytes
- reject：指定拒絕的路由。他會強迫對設定位址的路由查詢失敗
- dev IF：指定該路由僅跟特定的裝置IF(如eth0、ppp0)相關

若在eth0的裝置加上一個路由到192.168.10.0(子網路遮罩255.255.255.0)這個網段：

	$ sudo route add -net 192.168.10.0 netmask 255.255.255.0 dev eth0
	$ route
	Kernel IP routing table
	Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
	default         ip-172-31-16-1. 0.0.0.0         UG    0      0        0 eth0
	172.31.16.0     *               255.255.240.0   U     0      0        0 eth0
	192.168.10.0    *               255.255.255.0   U     0      0        0 eth0

若要將原有的閘道位址10.1.1.1停用，而使用新的閘道位址10.1.1.2。

	# route del default gw 10.1.1.1 // 刪除舊的閘道10.1.1.1
	# route add default gw 10.1.1.2 // 增加新的閘道10.1.1.2

#### ping ####

ping是傳送ICMP echo request封包的指令，常用於測試對方的位址是否有回應，使用方式如下所示：

語法：ping [參數] 位址

- -b：對廣播位址傳送ICMP echo的封包
- -c 次數：只傳送指定次數的封包。若不指定次數則會持續傳送封包，直到使用者按`Ctrl+C`中斷為止
- -f：大量的傳送ICMP echo封包
- -i 秒數：每隔多少秒傳送一次封包，只有管理者可以將此數值設在0.2秒之下
- -l 介面位址：指定傳送的介面位址
- -n：只透過數字位址傳送，而不會做名稱反查
- -r：直接傳送封包，而不透過閘道
- -s 封包大小：指定傳送的封包大小，單位為bytes，預設值為56
- -v：顯示詳細的執行過程
- -V：顯示版本資訊

對www.google.com丟出5次ICMP echo封包，並顯示統計結果

    $ ping -c 5 www.google.com
    PING www.google.com (216.58.197.228) 56(84) bytes of data.
    64 bytes from nrt13s49-in-f4.1e100.net (216.58.197.228): icmp_seq=1 ttl=56 time=1.98 ms
    64 bytes from nrt13s49-in-f4.1e100.net (216.58.197.228): icmp_seq=2 ttl=56 time=1.93 ms
    64 bytes from nrt13s49-in-f4.1e100.net (216.58.197.228): icmp_seq=3 ttl=56 time=1.88 ms
    64 bytes from nrt13s49-in-f4.1e100.net (216.58.197.228): icmp_seq=4 ttl=56 time=2.01 ms
    64 bytes from nrt13s49-in-f4.1e100.net (216.58.197.228): icmp_seq=5 ttl=56 time=1.85 ms

    --- www.google.com ping statistics ---
    5 packets transmitted, 5 received, 0% packet loss, time 4004ms
    rtt min/avg/max/mdev = 1.855/1.933/2.010/0.081 ms

#### netstat ####

netstat是一個顯示網路狀態的指令，用法如下：

語法：netstat [參數]

- -a：顯示所有資訊，包含一般資訊、socket資訊、路由表、網路介面等
- -c：將網路狀態持續列出
- -e：顯示其他相關資訊
- -l：顯示正在listen狀態的socket
- -M：顯示隱藏的連線，必須支援ip偽裝的功能才能用到此參數
- -n：直接以IP位址顯示，不經過名稱伺服器
- -p：顯示與socket相關的程式名稱及PID
- -r：顯示系統路由表
- -t：僅顯示TCP通訊協定的連線狀態
- -u：僅顯示UDP通訊協定的連線狀態
- -v：列出完整的執行過程
- -w：僅顯示RAW通訊協定
- --help

若要顯示TCP的連線狀態：

	$ netstat -nt
	Active Internet connections (w/o servers)
	Proto Recv-Q Send-Q Local Address           Foreign Address         State
	tcp        0      0 172.31.31.22:22         43.251.79.36:50582      ESTABLISHED
	tcp        0      0 172.31.31.22:22         43.251.79.36:50580      ESTABLISHED

若要顯示目前TCP應用程式所使用的埠號：

	$ netstat -apt
	(No info could be read for "-p": geteuid()=1000 but you should be root.)
	Active Internet connections (servers and established)
	Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
	tcp        0      0 *:ssh                   *:*                     LISTEN      -
	tcp        0      0 localhost:6010          *:*                     LISTEN      -
	tcp        0      0 ip-172-31-31-22.ap-:ssh 43.251.79.36:50582      ESTABLISHED -
	tcp        0      0 ip-172-31-31-22.ap-:ssh 43.251.79.36:50580      ESTABLISHED -
	tcp6       0      0 [::]:ssh                [::]:*                  LISTEN      -
	tcp6       0      0 ip6-localhost:6010      [::]:*                  LISTEN      -

若要顯示路由列表：

	$ netstat -r
	Kernel IP routing table
	Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
	default         ip-172-31-16-1. 0.0.0.0         UG        0 0          0 eth0
	172.31.16.0     *               255.255.240.0   U         0 0          0 eth0
	192.168.10.0    *               255.255.255.0   U         0 0          0 eth0

使用netstat -r與route指令相同。

#### traceroute ####

traceroute是路由連線追蹤的指令，可查詢由本機到對方主機所經過的路由器，使用方式如下：

語法：traceroute [參數] 主機位址 [封包大小(單位為bytes)]

- -d：啟動偵錯模式
- -f ttl：設定起始封包的存活時間
- -F：設定不能分割的位元
- -l：使用ICMP ECHO(ping指令)的封包而不使用UDP資料傳送
- -l 介面：指定網路介面，如eth0
- -m 次數：設定封包的最多跳躍次數。預設30次
- -p 埠號：設定探測封包所使用的埠號，預設為33434
- -r：不使用路由表，而以直接連線的方式連結
- -s 來源位址：在多個位址時，使用指定的來源位址
- -v：顯示完整的執行過程
- -w 秒數：設定探測的間隔秒數
- -z 秒數：設定探測間的時間，單位為毫秒，預設值為0，建議值500，也就是0.5秒

顯示連線到163.14.157.13所經過的路由器：

	$ traceroute 163.14.157.13
	traceroute to 163.14.157.13 (163.14.157.13), 30 hops max, 60 byte packets
	 1  ec2-175-41-192-130.ap-northeast-1.compute.amazonaws.com (175.41.192.130)  1.507 ms ec2-175-41-192-134.ap-northeast-1.compute.amazonaws.com (175.41.192.134)  1.496 ms  1.490 ms
	 2  27.0.0.154 (27.0.0.154)  2.153 ms 27.0.0.172 (27.0.0.172)  2.112 ms  2.124 ms
	 3  52.95.30.135 (52.95.30.135)  13.425 ms 52.95.30.123 (52.95.30.123)  3.596 ms 27.0.0.154 (27.0.0.154)  2.497 ms
	 4  52.95.30.123 (52.95.30.123)  3.515 ms 52.95.30.133 (52.95.30.133)  3.064 ms 52.95.30.125 (52.95.30.125)  6.023 ms
	 5  52.95.30.180 (52.95.30.180)  2.786 ms as9505-2.ix.jpix.ad.jp (210.171.224.89)  2.885 ms  2.946 ms
	 6  203.78.181.241 (203.78.181.241)  33.718 ms  32.610 ms  32.667 ms
	 7  17-60-41-175.TWGATE-IP.twgate.net (175.41.60.17)  32.603 ms 203.78.181.241 (203.78.181.241)  32.830 ms 17-60-41-175.TWGATE-IP.twgate.net (175.41.60.17)  32.806 ms
	 8  46-61-41-175.TWGATE-IP.twgate.net (175.41.61.46)  34.876 ms 17-60-41-175.TWGATE-IP.twgate.net (175.41.60.17)  32.811 ms  32.806 ms
	 9  46-61-41-175.TWGATE-IP.twgate.net (175.41.61.46)  35.101 ms 192.192.61.70 (192.192.61.70)  34.303 ms  34.264 ms
	10  192.192.61.1 (192.192.61.1)  34.546 ms 192.192.61.185 (192.192.61.185)  34.616 ms 192.192.61.66 (192.192.61.66)  35.867 ms
	11  192.192.61.198 (192.192.61.198)  33.507 ms 192.192.61.185 (192.192.61.185)  33.975 ms  34.063 ms
	12  192.192.61.198 (192.192.61.198)  33.536 ms  33.607 ms  33.840 ms
	13  140.111.230.30 (140.111.230.30)  34.535 ms 192.192.61.198 (192.192.61.198)  33.867 ms 163.14.10.3 (163.14.10.3)  35.173 ms
	14  163.14.10.3 (163.14.10.3)  35.196 ms *  35.244 ms
	15  * 163.14.10.3 (163.14.10.3)  35.296 ms *
	16  * * *
	17  * * *
	18  * * *
	19  * * *
	20  * * *
	21  * * *
	22  * * *
	23  * * *
	24  * * *
	25  * * *
	26  * * *
	27  * * *
	28  * * *
	29  * * *
	30  * * *

### 網路與DNS相關設定檔 ###

本節介紹網路設定與域名設定相關的設定檔。

#### /etc/hosts ####

這是主機名稱與位址的對應設定

	$ cat /etc/hosts
	127.0.0.1 localhost

	# The following lines are desirable for IPv6 capable hosts
	::1 ip6-localhost ip6-loopback
	fe00::0 ip6-localnet
	ff00::0 ip6-mcastprefix
	ff02::1 ip6-allnodes
	ff02::2 ip6-allrouters
	ff02::3 ip6-allhosts

#### /etc/resolv.conf ####

這是DNS伺服器的設定檔，重點在於nameserver後面接的是DNS伺服器的IP位址

	$ cat /etc/resolv.conf
	# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
	#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
	nameserver 172.31.0.2
	search ap-northeast-1.compute.internal

#### /etc/sysconfig/network-scripts/ifcfg-eth0 ####

這是Red Hat系列(Red Hat Enterprise、Fedora、CentOS等)的網路位址設定檔，檔名中的eth0代表了介面的名稱(意思是如果eth1介面，檔名就必須為ifcfg-eth1)，內容包含了**網路裝置名稱**、**IP位址**、**子網路遮罩**、**廣播位址**等資訊。

#### /etc/network/interfaces ####

這是Debian與Ubuntu的網路位址設定檔，內容包含了**網路裝置名稱**、**IP位址**、**子網路遮罩**、**廣播位址**等資訊。

    $ cat /etc/network/interfaces
    # This file describes the network interfaces available on your system
    # and how to activate them. For more information, see interfaces(5).

    # The loopback network interface
    auto lo
    iface lo inet loopback

    # Source interfaces
    # Please check /etc/network/interfaces.d before changing this file
    # as interfaces may have been defined in /etc/network/interfaces.d
    # NOTE: the primary ethernet device is defined in
    # /etc/network/interfaces.d/eth0
    # See LP: #1262951
    source /etc/network/interfaces.d/*.cfg

	$ cat /etc/network/interfaces.d/eth0.cfg
	# The primary network interface
	auto eth0
	iface eth0 inet dhcp

#### /etc/services ####

這是服務名稱、埠號與通訊協定的名稱對應檔，內容範例如下：

    $ cat /etc/services
    # Network services, Internet style
    #
    # Note that it is presently the policy of IANA to assign a single well-known
    # port number for both TCP and UDP; hence, officially ports have two entries
    # even if the protocol doesn't support UDP operations.
    #
    # Updated from http://www.iana.org/assignments/port-numbers and other
    # sources like http://www.freebsd.org/cgi/cvsweb.cgi/src/etc/services .
    # New ports will be added on request if they have been officially assigned
    # by IANA and used in the real-world or are needed by a debian package.
    # If you need a huge list of used numbers please install the nmap package.

	tcpmux          1/tcp                           # TCP port service multiplexer
	echo            7/tcp
	echo            7/udp
	discard         9/tcp           sink null
	discard         9/udp           sink null
	systat          11/tcp          users
	daytime         13/tcp
	daytime         13/udp
	netstat         15/tcp
	qotd            17/tcp          quote
	msp             18/tcp                          # message send protocol
	msp             18/udp
	chargen         19/tcp          ttytst source
	chargen         19/udp          ttytst source
	...

#### /etc/nsswitch.conf ####

這是name service switch的設定檔，提供**與名稱相關服務的搜尋設定**，例如網路訊息服務NIS、域名服務DNS等，都會使用這設定作為名稱搜尋的依據，範例如下：

    $ cat /etc/nsswitch.conf
    # /etc/nsswitch.conf
    #
    # Example configuration of GNU Name Service Switch functionality.
    # If you have the `glibc-doc-reference' and `info' packages installed, try:
    # `info libc "Name Service Switch"' for information about this file.

	passwd:         compat
	group:          compat
	shadow:         compat

	hosts:          files dns
	networks:       files

	protocols:      db files
	services:       db files
	ethers:         db files
	rpc:            db files

	netgroup:       nis

#### INETD伺服器與XINETD伺服器 ####

INETD可拆成三段來看：I-NET-D，其中I-NET表示internet，D表示Daemon，也就是網際網路上的**常駐程式**。

※Daemon：是精靈的意思，也就是介於人與神之間的一種媒介。在Linux中，Daemon的意思也接近如此，代表的是人與機器之間的媒介，通常我們稱為常駐程式。

INETD是監視一些網路請求的程序，其根據網路請求相應的服務程序來處理連接請求。它可以為多種服務管理連接，當inetd接到連接請求時，它能夠確定連接所需的程式，啟動相應的程序。

使用INETD不需要為每個服務都啟動獨立的服務程式，因此執行那些負載不重的服務有助於降低系統負載。一般來說，INETD主要用於啟動其他服務程式如telnet、ipop3等，但它也有能力直接處理某些簡單的服務，例如echo、daytime等。

XINETD為INETD的取代品，它是由兩個詞的合併而來，INET-D，其中INET為internet，D為Daemon(常駐程式)。

X在Linux或是Unix中有shadow(陰影)之意，表示更安全、功能更強的產品。XINETD延續了INETD中的所有功能與特性，並加強了安全上的防護。`/etc/xinetd.d/`底下為XINETD中伺服器的設定檔案，通用格式如下：

	service <service_name>
	{
		<A> <B> <參數> <參數> ...
	}

其中`<B>`是給予運算符號，有三種：'='、'+='、'-='

`<A>`是屬性，列一些較常用的在下面供參考：

1. id

	這項屬性用來確認服務的唯一性，特別是相同名稱的服務，但卻使用不同的通訊協定。

2. type

	包含下列三種

	- RPC：Remote Procedure Call，遠程程序呼叫。
	- INTERNAL：本身包含在XINETD中的服務。
	- UNLISTED：此項服務未被包含在/etc/service中。

3. disable

	包含yes與no，yes表示停止服務，no或是沒有包含這項屬性則表示提供服務

4. socket_type

	包含下列三種屬性

	- stream：stream-based的服務，也就是TCP service，如telnet，ftp等。
	- dgram：datagram-based服務，也就是UDP service，如DNS等。
	- raw：需要直接使用IP的服務。

5. instance

	同時間連線的數目，UNLIMITED表示不限制

6. server

	決定這個服務所使用的應用程式

7. server_args

	這個服務所需的應用程式後面所需要加的參數

8. bind

	這個服務所使用的IP位址

9. only_from

	限制某些特定來源IP位置，才提供連線服務，其他的均不得建立連線

10. no_access

	限制某些特定來源IP位置 不提供服務

11. access_time

	可提供服務的時間

12. port

	指定這項服務所使用的埠號


## 上網的設定方式 ##

常見的包括固定位址上網、動態位址上網與ADSL的pppoe撥號上網。

### 固定位址上網 ###

#### 指令設定方式 ####

假設主機的IP位址為111.23.1.1，子網路遮罩為255.255.255.0，那麼我們可以通過以下的指令修改IP位址：

*ifconfig eth0 111.23.1.1 netwask 255.255.255.0*

此外，可通過以下指令來增加預設閘道

*route add default gw 111.23.1.254*

此外，要注意的是，若已經設定預設閘道，而要更換預設閘道，則必須將原有的預設閘道移除，再將新的預設閘道加入，如下；

*route del default gw 10.1.1.1*

*route add default gw 111.23.1.254*

※使用指令修改，無法更新設定檔的資料，也就是說重啟後，通過指令的設定都會消失。因此，若是希望永久的更改設定，必須修改設定檔後重新啟動服務。

#### 使用設定檔設定網路位址 ####

Fedora與Red Hat：

※ NetworkManager是Fedora 10之後系統上一個網路位址取得的服務，它會自動偵測目前網路上的設定，並自動修改設定檔的內容，包含無線網路的偵測等。但相對的，當它無法識別十，就會產生無法連線的狀況，因此底下範例，建議將NetworkManager停用並開啟network服務，可執行`systemctl NetworkManager.service disable`以及`systemctl network.service enable`。

若電腦主機名為home.test.com，網路卡的IP位址為111.23.1.1，子網路遮罩為255.255.255.240；預設閘道(gateway)為111.23.1.14。

`step 01` **修改/etc/sysconfig/network-scripts/ifcfg-eth0，如下所示：**

    DEVICE=eth0
	BOOTPROTO=static
    IPADDR=111.23.1.1       // IP位址
	NETMASK=255.255.255.240 // 子網路遮罩
	GATEWAY=111.23.1.14     // 預設閘道
    ONBOOT=yes              // 開機時啟用開設定

`step 02` **修改/etc/sysconfig/network，如下所示：**

    NETWORKING=yes
    HOSTNAME=home.test.net

`step 03` **如果沒有改HOSTNAME，那麼執行以下指令即可；如果有，則需重新啟動：**

*systemctl network.service restart*

※ 注意：若要檢查是否生效，我們可使用ifconfig看看狀況，如果出現eth0且設定正確，那IP位址與子網路遮罩的設定就沒問題了；若使用route -n，且在0.0.0.0後面跟著111.23.1.14，那gateway就沒問題了。

Ubuntu：

`step 01` **修改/etc/network/interfaces，如下所示：**

    auto lo
    iface lo inet loopback

    auto eth0
	iface eth0 inet static
			address 172.20.11.123
			netmask 255.255.255.0
			network 172.20.11.0
			broadcast 172.20.11.255
			gateway 172.20.11.1

`step 02` **重新啟動網路服務**

*/etc/init.d/network restart*

### 動態位址上網 ###

動態位址的意思是由DHCP伺服器分配IP位址，因此每次取得的IP位址可能不同，故稱為動態。

#### 使用設定檔獲取動態位址 ####

假設我們要將這台Linux主機設為自動獲取網路位址(DHCP用戶端)，可參照以下步驟。

`step 01` **修改/etc/sysconfig/network-scripts/ifcfg-eth0，如下所示：**

	DEVICE=eth0
	BOOTPROTO=dhcp
	ONBOOT=yes

`step 02` **重新啟動網路介面。**

*systemctl network.service restart*

如果使用的是Ubuntu：

`step 01` **修改/etc/network/interfaces，如下所示：**

    auto lo
    iface lo inet loopback

    auto eth0
    iface eth0 inet dhcp

`step 02` **重新啟動網路服務**

*/etc/init.d/network restart*

#### 使用指令獲取動態位址 ####

*dhclient eth0*

獲取的IP位址資訊位於`/var/lib/dhcp/dhclient.leases`之中，使用cat查看內容如下所示：

    $ cat /var/lib/dhcp/dhclient.leases
    lease {
      interface "eth0";
      fixed-address 172.31.31.22;
      option subnet-mask 255.255.240.0;
      option routers 172.31.16.1;
      option dhcp-lease-time 3600;
      option dhcp-message-type 5;
      option domain-name-servers 172.31.0.2;
      option dhcp-server-identifier 172.31.16.1;
      option interface-mtu 9001;
      option broadcast-address 172.31.31.255;
      option host-name "ip-172-31-31-22";
      option domain-name "ap-northeast-1.compute.internal";
      renew 4 2016/08/04 09:16:35;
      rebind 4 2016/08/04 09:43:50;
      expire 4 2016/08/04 09:51:20;
    }

如果要釋放從DHCP伺服器獲取的IP位址，可執行以下指令：

*dhclient -r*

此外，dhcpcd也是DHCP用戶端的指令(需另外安裝)，而dhcpd則是伺服器的指令。