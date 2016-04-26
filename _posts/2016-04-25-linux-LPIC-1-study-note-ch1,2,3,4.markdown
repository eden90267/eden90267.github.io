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

一般將安裝分三階段