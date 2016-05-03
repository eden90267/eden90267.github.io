---
layout: post
title:  "Linux LPIC-1 Study Note(CH 1, 2, 3, 4)"
date:   2016-04-25 14:37:00 +0800
categories: [LPIC-1, linux]
---

# Chapter 01: 認識 Linux 作業系統 #

## 作業系統的特色與源起 ##

作業系統是一個**人機之間的介面**, 讓使用者便於操作電子設備並完成自己的需求。

在不同電子設備會有相異需求, 因此會搭配不同作業系統。在相同版本的系統下也常會根據不同的硬體設計不同版本, 以Ubuntu Linux為例, 就有一般電腦使用的i386、64位元使用的amd這兩種版本。

Linux起源是芬蘭大學生Linus Benedict Torvalds, 用Unix minix為基底編寫新的作業系統。在1991/10公布Linux核心0.02版, 並邀請網路上同好一起參加Linux開發工作。

Linux這名字來自作者的名字Linus以及Unix作業系統。標誌是一隻企鵝, 原因是Linus在澳洲動物園被企鵝咬了一口, 因此選擇企鵝當Linux圖標。

### 對於Linux系統常見疑惑 ###

Linux與Windows在本質上就有相當大的差異, 並在目的性、功能性、以及週遭軟體的發展上都不相同。

#### Linux是一套作業系統? ####

Linux是一套作業系統, 但我們是找不到一套名叫「Linux」的作業系統。目前市面上的Linux都是由發行商收集核心與套件, 並編寫開機程式而產生的作業系統, 因此都以自己的商標或公司命名。這些發行商所推出的系統與版本都是Linux作業系統。

#### Linux有統一版本號碼? ####

對於Linux只有一個統一版本號, 就是核心版本號, 如2.4.9、2.6.32等, 但一般會記得是發行商所代表的版本號,如Read Hat Enterprise 5, Ubuntu 10.4等, 有的是根據數字, 有的是根據日期, 因此版本號會依照不同發行商而有所不同。

#### Windows上的程式, Linux可以安裝嗎? ####

基本上, Windows上的程式是無法「直接」在Linux上使用的。

但可透過套件「wine」來安裝Windows上的應用程式。但不支援所有程式, 並且效能會大打折扣, 因此不建議使用。

#### Linux上大多數的程式, 都是免費的嗎? ####

否, Linux上大部分程式稱為Free-ware, 也就是Free Software之意, Free並非免費, 而是自由。

可自由使用, 但要確保大家使用的權利, 「不得販賣自由軟體」成為他特色之一。但任何人可以加上其他的「商品」一同販賣, 例如光碟片、書籍、使用手冊、售後服務等。

因此對於Linux上大多數程式, 我們都可任意取得並自由使用, 也可購買或加工後販售, 但這當中的商業行為都來自服務, 而非程式本身。

#### Linux上的程式, 有版權問題? ####

有, 若以一般自由軟體而言, 遵循GPL(General Public License)的規範, 但一般使用者可以自由使用、複製、修改、再發行。因此對自由軟體而言, 他是有版權的, 但我們可自由使用它。

除自由軟體外, 還是有一些是有版權的軟體, 這些軟體就須取得授權後才能使用。

### Linux發行商介紹 ###

Linux不都是free與Open Source? 怎麼有發行商?

核心與大部分套件確實是如此, 但能須有團隊將這些核心與套件整合並製作成安裝光碟, 因此這樣團隊就會成立公司或其他組織, 將這些免費的套件收集並測試, 最後發行自己的版本, 這就稱為發行商。

發行商除收集套件, 還會作以下工作:

1. 製作圖形或文字安裝程式(installer), 方便用戶安裝
2. 製作或使用套件管理工具
3. 製作專屬的商標與圖形介面
4. 定義開機程式與預設啟動的服務
5. 調整核心選項, 並加入自定義模組

介紹市面上常見發行商

#### Red Hat 與 Fedora ####

Marc Ewing 1994寫出第一個Linux版本: Red Hat Linux(1.0), ....

在2003/10作出重大改變: 將產品線分為免費的Fedora與收費的版本Red Hat Enterprise。

#### CentOS ####

源自於Red Hat的Linux發行版之一, 由於源碼都是出自於Red Hat, 因此許多高度穩定的伺服器都會以它代替Red Hat。

#### Debian ####

Debian是一套穩定並精簡的Linux套件, 常被用來架設伺服器的套件之一。

#### SuSE ####

SuSE原是一家德國的Linux公司, 由4人成立。SuSE名字是德文「Software Und System Entwicklung」簡寫, 意思為「軟體及系統開發(Software and System Development)」。

SuSE在歐洲是佔有率極高的Linux作業系統之一。在與Oracle搭配上, 它的相容性可是相當完備, 因此它是安裝資料庫作業系統的極佳選擇。

#### Ubuntu ####

是一句含義為「以人道待人」和「群在故我在」的非洲語, Ubuntu開發者將這種精神帶入電腦軟體的國度。

Ubuntu是社群開發的作業系統, 適用筆記型電腦、個人電腦和伺服器。宣稱永久免費, 無須支付授權費用；可自由下載、使用與分享。每6個月推出一套新的個人版本和伺服器版本, 同一電腦下, 可有至少18個月的免費安全性更新, 使用長時間將會有至少三年的版本支持。

起源於2004年10月。

#### gentoo ####

Gentoo Linux由Daniel Robbins開發, 他希望有一個中央管理工具控管整個系統的套件。眾所皆知, RPM是目前Linux上最流行的套件管理工具, 但安裝RPM我們常碰到兩個大問題：

1. 套件相容性
2. 套件模組的調整

ex: 安裝Apache, 我們無法透過RPM來選擇是否載入ssl與認證的模組; 安裝Sendmail或是Postfix時, 我們也無法通過RPM來選擇是否編入認證的模組。

上述這些問題, 在Gentoo之中得到完整的解決。

1. Gentoo之中絕大部分的套件都是通過原始碼來安裝, 更改編入的模組就輕而易舉了
2. 通過portage的機制, 可記載套件的資訊, 包括模組、相容性和紀錄

因此安裝套件將更彈性化, 並更值得信賴。

目前在全世界許多地點都提供portage的下載點, 並有Gentoo的技術團隊維護, 其中的套件都是最新且通過測試的。因此在套件的更新上, 使用Gentoo是沒問題的。

Gentoo linux官方網站, 做下列幾點聲明：

1. Gentoo linux 遵循 GNU license
2. Gentoo linux 目前是 free software, 將來也是
3. Gentoo linux 會回饋給自由軟體社群
4. Gentoo linux 不會隱藏任何問題

---

# Chapter 02: 認識 LPI 認證考試 #

LPI考試內容廣泛, 包含硬體與軟體、RPM與DEB、系統、網路並含括安全的範圍。

LPI含括Red Hat(Red Hat Enterprise、Fedora、CentOS)與Ubuntu(Debian與Ubuntu)兩套主流系統, 屬於較中立的機構。

117-101

偏重在指令與檔案系統的部分

- System Architecture
- Linux Installation and Package Management
- GNU and Unix Commands
- Devices, Linux Filesystems, Filesystem Hierarchy Standard(標準化檔案系統階層)

117-102

比例比較均衡, 除User Interfaces and Desktops比重5外, 其他比重都在10左右

- Shells, Scripting and Data Management
- User Interfaces and Desktops
- Administrative Tasks
- Essential System Services(必要的系統服務)
- Networking Fundamentals(網路基礎概念)
- Security

 ---

# Chapter 03: 作業系統環境的架設 #

## 虛擬機環境 ##

虛擬機顧名思義, 就是一台虛擬的主機, 在同一台主機上可安裝多個作業系統。一般虛擬主機分為硬體跟軟體的虛擬機, 硬體的虛擬機通常為大型工作站, 擁有多顆處理器與大型的記憶體, 在開機階段可分配給多個作業系統使用。這類主機價格昂貴, 多半用於電信業者的機房之內。

軟體虛擬機是用軟體達到虛擬機的效果。原理是在已經安裝作業系統上安裝一個虛擬機軟體, 再透過該虛擬機分配資源並安裝作業系統。Ex: VirtualBox。

這樣的好處是在資源有限的環境下可安裝第二個作業系統。

相對的, 安裝虛擬機會耗損原有作業系統的資源, 延緩程式執行的速度。因此需要優異的效能, 虛擬機不是最好選擇。

## Pietty遠端連線操作 ##

具備SSH的連線遠端伺服器軟體, 並有完整的中文化支援。

---

# Chapter 04: Linux系統環境建置 #

分三部分: 安裝規劃、安裝過程以及後續的設定調整。

## 安裝規劃 ##

一般將安裝分三階段: pre-installation、installation以及post-installation, 其中pre-installation稱為安裝前的準備, 作用是安裝的前置作業。

第二階段installation只是按照pre-installation的規劃內容一步步執行, 第三階段post-installation是安裝後的調整, 也就是我們常說的系統設定。

在安裝前如果能對主機的用途與架構有個妥善的規劃, 就可以省去事後調整的麻煩。

### 安裝前的準備 ###

以下列出四個安裝前須注意的事項:

#### 1. Linux是否能支援硬體 ####

一般而言, 主機板、CPU、或是硬碟會與Linux不相容的情形並不多見。若在安裝過程看到訊息「signal 11」或是「Invalid memory reference」那就是記憶體問題了，這時候建議更換記憶體。

#### 2. 安裝光碟是否正確 ####

這也是很重要一點, 除光碟本身是否能被光碟機讀取外, 來源也相對重要。來歷不明可能會導致安裝不完全, 或被植入木馬程式。

#### 3. 系統的規劃 ####

首要考量是這台機器的用途為何? 是哪一種類型的伺服器? 如果從這角度出發, 對硬體的選擇也會不同。

- DNS 或 NAT Server: 找一台Pentium II以上的機器就綽綽有餘
- mail server: Pentium 4 搭配 512MB 的 RAM 也足夠了, 如果信件要存放到本機(非單純的轉信伺服器, relay server), 硬碟要視用戶量與存放量而定。
- 安裝資料庫: Pentium 4以上搭配 1GB RAM, 硬碟至少 40GB

#### 4. 網路的規劃 ####

除上面所述, 這台機器在網路架構中, 扮演什麼樣的角色? 提供哪些服務, 放在網路中什麼位置? 有清楚定位, 安裝時也能比較清楚該安裝那些套件, 硬碟需要多大? 應該怎麼切割?

### 硬碟分割規劃 ###

硬碟分割、格式化與掛接, 是檔案系統的基本組成。這裡介紹安裝系統時, 硬碟的分割規劃概念, 將分「硬碟裝置的認定」與「安裝系統時的硬碟分割」加以簡介, 重點在於系統對**硬碟裝置**與**分割區**的認定, 以及**常在安裝時獨立切割的分割區**。

考試範圍102.1: Design hard disk layout

#### 硬碟裝置的認定 ####

在Linux系統中, 會將所有硬體裝置的檔案都放置在 `/dev` 之下(dev -> device 裝置的簡寫), 以硬碟主要分為兩類:

1. IDE裝置

	這類裝置以hd作為開頭, 第三位由英文字母a、b、c依序排列。在傳統的主機板上, 通常有兩個IDE的排線插孔, 而每個排線可插上兩顆硬碟或光碟機。第一個IDE排線上的第一顆硬碟為 `/dev/hda`, 第二顆為 `/dev/hdb`; 第二個IDE排線第一顆硬碟為 `/dev/hdc` (通常會將光碟機插在IDE裝置的第二條排線上, 因此光碟機在Linux系統上常被命名為 `/dev/hdc`), 第二顆 `/dev/hdd` 。

	在IDE硬碟上的分割區, 會以數字作為命名標準。例如系統上有一顆IDE硬碟, 會產生 `/dev/hda` 這檔案, 而當我們將這顆硬碟分割為三個分割區, 就會產生 `/dev/hda1` 、 `/dev/hda2` 、 `/dev/hda3`。

2. SCSI、 SATA與USB裝置

	這類裝置會以sd作為開頭, 第三位由英文字母a、b、c依序排列(順序的認定會以核心偵測硬體的順序為準)。第一顆硬碟 `/dev/sda` 、 第二顆硬碟 `/dev/sdb` 、 第三顆硬碟 `/dev/sdc`, 依此類推。

	同樣的, 在這類硬碟上的分割區, 會以數字作為命名標準。 三分割區: `/dev/sda1` 、 `/dev/sda2` 、 `/dev/sda3`

#### 安裝系統時的硬碟分割 ####

安裝Linux時有兩個必要的硬碟分割區: 「/」, SWAP(又稱為交換區、置換區、交換空間、置換空間, 一個通俗的稱呼為虛擬記憶體)。一般「/」建議要劃分**6GB以上**, 而SWAP需要**512MB以上**。

Linux的系統是一個樹狀的結構, 由最頂層「/」(root結構)往下延伸, 包含 `/bin` 、 `/etc` 、 `/home` 、 `/mnt` 、 `/opt` 、 `/proc` 、 `/sys` 、 `/tmp` 、 `/usr` 、 `/var` 等一級目錄(第一層目錄), 除了「/」之外, 那些目錄需要獨立的硬碟空間?

簡單解答: 目錄下的內容如果時常變動, 那就建議劃分獨立的分割區, 如 `/home` (使用者家目錄) 、 `/tmp` (這是任何人都可讀寫執行的公共目錄)與 `/var` (這目錄下有暫存檔、郵件檔與資料儲存池)這三個。

## 安裝過程介紹 ##

### Ubuntu 系統安裝 ###

### CentOS 系統安裝 ###

### Fedora 系統安裝 ###

## 安裝後的操作 ##

### 硬體資訊 ###

Linux的硬體資訊眾多, 牽涉範圍甚廣, 這邊主要介紹IRQs(中斷訊號)、I/O port(I/O位址)和DMA通道, 以及ISA、PCI與USB裝置, 讓我們更了解硬體跟Linux的互動方式。

#### IRQ中斷訊號 ####

中斷是指**接收外圍硬體或軟體的中斷請求而進行相對應的軟硬體處理**, 其功用能提升機器的效能。IRQ(中斷訊號)編號是0~15, 開始的IRQ只能處理8個IRQs(0~7), 利用IRQ=2串聯後, 就可以處理8~15。 各編號對應的硬體裝置如下:

- 0: system timer
- 1: 鍵盤
- 2: 提供IRQs標號8~15
- 3: 第二個或第四個序列埠(serial port)
- 4: 第一個或第三個序列埠(serial port)
- 5: 音效卡或第二個並列埠(LPT port)
- 6: 第一個軟碟控制器(flppy disk controller)
- 7: 第一個並列埠(LPT port)
- 8: 系統時間(real-time clock)
- 9: 開放的IRQ
- 10: 開放的IRQ
- 11: 開放的IRQ
- 12: PS/2連接滑鼠(PS/2 connector mouse)
- 13: 數學輔助處理器
- 14: 主要的ATA通道(channel)
- 15: 次要的ATA通道(channel)

IRQ的設定存於核心檔中, 檔案位置(`/proc/interrupts`)

	cat /proc/interrupts

#### I/O位址 ####

I/O位址**顯示系統裝置在記憶體中的位置**, 這個對應是唯一的, 能讓CPU和特定裝置相互通訊

裝備 | Linux系統名稱 | IRQ | I/O位址

序列埠(COM1) | /dev/ttySO | 4 | 0x3f8

序列埠(COM2) | /dev/ttyS1 | 3 | 0x2f8
...

I/O的設定存於核心檔中,檔案位置 `/proc/ioports`

	cat /proc/ioports

#### DMA 通道 ####

DMA (direct memory access) 通道, 他**允許介面裝置與記憶體之間直接轉移資料**, 而**不需CPU的參與**。DMA是所有現代電腦的重要特色, 允許不同速度的硬體裝置彼此溝通, 而不須依於CPU的大量中斷負載。

DMA的設定存於核心檔中, 檔案位置為/proc/dma

	cat /proc/dma

#### ISA ####

ISA源自早期IBM電腦的舊技術, 早期靠著jumper來調整IRQ, 後來產生了新的組態技術: 即插即用(Plug and Play, 簡稱PnP), 作業系統能夠自動設定, 自動調整硬體組態。目前使用ISA的機會不多, 為應付考試, 須了解 `pnpdump` 與 `isapnp` 這兩個指令

1. pnpdump

	功能: 顯示ISA PnP介面卡上使用的資訊

	格式: pnpdump [參數]

	- `-h`: 顯示所有幫助
	- `-c`: 會根據 `/proc` 和 `/etc/isapnp.gone` 兩個檔案, 不輸出已使用的裝置
	- `-o`: 將輸出另存為檔案

知道ISA Pnp裝置資訊後, 可利用isapnp來調整其組態, 要注意指令isapnp後面接的是組態檔, 不是裝置名稱。

2. isapnp

	功能: 組態ISA PnP(Plug and Play)裝置
	
	格式: isapnp 組態檔(config)
	
		# isapnp /etc/isapnp.conf
		
#### PCI ####

PCI匯流排常見於現在的個人電腦中, 並已經取代了ISA, 成為標準的擴展匯流排。目前已經推出了PCI Express, 目前也逐漸取代了PCI匯流排, 以下介紹相關指令:

1. lspci

	功能: 列出電腦所有PCI裝置的資訊
	
	格式: lspci [參數]
	
	- `-v`: 詳細輸出PCI匯流排資訊

2. setpci

	功能: 設定PCI裝置組態
	
	格式: setpci [裝置]
	
3. setserial

	功能: 取得或設定序列埠(serial port)組態
	
	格式: setserial 參數 [序列埠裝置]
	
	- `-a`: 輸出序列埠裝置組態時, 印出所有可用資訊
	- `-q`: 輸出較剪短序列埠組態
	- `-v`: 輸出較長序列埠組態

		setserial -a /dev/ttyS0
		
#### USB 裝置 ####

目前的核心都有支援USB上的OHCI、UHCI、EHCI三種控制器, 我們可在 `/proc/bus/usb/devices` 來看USB裝置資訊, 較舊的機器上檔案位置可能在 `/proc/pci` 上。

	cat /proc/bus/pci/devices
	
### 開機過程與紀錄 ###

當我們打開機器電源

1. 經過BIOS檢測
2. 執行開機啟動程式並載入核心
3. 初始程式接手並啟動所有程序與服務

	初始程式: ubuntu早期用init, 在ubuntu 6.10後使用了upstart, 而近期的Fedora則使用systemd

4. 顯示登入畫面

開機過程, 除了在開機時會顯示在畫面上之外, 還會記錄在核心的 ring buffer 之內, 可以使用 `dmesg` 這指令顯示其內容。此外, 在記錄檔 `/var/log/message` 之中也會記錄開機過程的內容。

希望顯示開機過程中第一顆SCSI硬碟的相關資訊:

	# dmesg | grep sda

開機時核心的參數定義在開機管理程式GRUB的設定檔中, 在GRUB2中已經不需要我們手動更改設定檔。如果開機後想了解核心的設定參數, 可查詢 `/proc/cmdline` :

	# cat /proc/cmdline

開機後想查詢核心版本, 可使用 `uname` 這指令加參數 `-r` 。如果想查詢完整訊息, 可加上參數 `-a` :

	# uname -r

	# uname -a

### 切換執行層級 ###

在Linux中執行層級(run level)共有六個

- 0: 表示關機
- 1~5: 不同Linux發行商而異
- 6: 表示重啟系統

以Red Hat(Red Hat Enterprise, Fedora, CentOS, Mandriva, SuSE與紅旗等)系列:

- 2: 代表缺少部分網路功能的文字介面模式
- 3: 代表完整功能的文字介面模式
- 4: 保留, 可讓使用者自定義
- 5: 完整功能的GUI模式

以Ubuntu而言, 2~5都相同, 可做為文字模式, 也可作為完整的圖形介面模式。

Run level: Ubuntu涵義 | Red Hat涵義

- 0: 關機 | 關機
- 1: 單人模式 | 單人模式
- 2: 圖形介面的多人模式(default) | 受限的多人模式
- 3: 保留 | 文字介面的多人模式
- 4: 保留 | 保留, 使用者可自行定義
- 5: 保留 | 圖形介面的多人模式
- 6: 重新開機 | 重新開機

Ubuntu在Run Level 2到5 是沒差別的, 都可作為一般的文字模式或圖形介面模式。

### 關機與重啟系統 ###

#### 登出與關閉系統 ####

關閉作業系統, 只需將執行層級調整為0即可。

	init 0

或使用 `shutdown` 、 `poweroff` 這類的指令關閉作業系統。

`shutdown -h` 或是 `poweroff`

若將電源選項編入核心中(default), 則關閉系統時會一起將機器的電源關閉(關閉電源這項功能取決於主機板的晶片組, 近幾年的主機板都支援這項功能)。

#### 重新啟動系統 ####

只需將initial模式調整為6即可

	init 6

或使用 reboot 這類指令關閉作業系統

	reboot

### 管理共享函式庫 ###

函式庫主要是給**執行檔使用的一個「資料庫」**。將共用的部分獨立寫出, 成為一個共用函式庫, 因此共用函式庫可以給多個程式共用。除了減低檔案大小之外, 在某些套件更新時只需更新函式庫, 而不需更動程式, 可讓套件管理上更有效率。

在Linux中的函式庫都以 `lib` 開頭, 再加上函式庫的名稱, 接著再加上「.so」或「.a」,

- 「.so」表示共享函式庫
- 「.a」表示靜態函式庫

	靜態函式庫中的元件會連結到執行檔中, 因此在程式執行的過程不需要函式庫, 但檔案會比較大。

在「.so」後面可能還會加上「.N」, 其中N表示版本號碼, 因此一個共享函式庫的名稱可能為liblber-2.4.so.2.9.2。

共享函式庫通常放於 `/lib` 之下, 設定檔為 `/etc/ld.so.conf` , 產生的暫存快取檔為 `/etc/ld.so.cache` , 環境變數為 LD_LIBRARY_PATH, 這些內容都必須熟記。

此外, 共享函式庫相關指令有兩個

- ldd: 可列出共享函式庫(關聯性)
- ldconfig: 可設定或建立共享函式庫(關聯性)

這兩個指令有時容易混淆, 要注意。

## 本章考題練習 ##

4.1.2

1. /dev/sda
2. 4
3. BDE
4. CD

4.3.1

1. bus
2. pnpdump
3. A
4. A -> E, lspci的內容不會有網路卡卡號的的資訊
5. E
6. D -> B
7. 7

4.3.2

1. D
2. D -> B
3. dmesg
4. B
5. A, 順序為: BIOS -> bootloader -> kernel -> init -> inetd

4.3.3

1. init
2. A ->　B, 選項 A 中的題目要求使用 `/bin/sh` 取代 `/sbin/init` 。選項 CD 語法錯誤。
3. B

4.3.4

1. AE
2. D

4.3.5

1. LD_LIBRARY_PATH
2. C
3. ldd -> ldd /usr/lib/abc.so
4. A
5. B
6. D

---