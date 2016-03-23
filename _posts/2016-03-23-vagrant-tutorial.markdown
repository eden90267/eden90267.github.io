---
layout: post
title:  "Vagrant Tutorial"
date:   2016-03-23 09:22:00 +0800
categories: [vagrant, DepOps, VirtualBox, 虛擬化]
---

因應docker學習需要Vagrant+VirtualBox作虛擬機, 所以create這篇文章來記錄其vagrant重點。
Reference: [Vagrant Tutorial（1）雲端研發人員，你也需要虛擬機！](http://www.codedata.com.tw/social-coding/vagrant-tutorial-1-developer-and-vm/)

# Vagrant Tutorial(1) #

研發人員也需要虛擬機? 不是伺服器維運人員才需要知道的嗎?

常見原因, 研發者用的本機電腦, 與真正上線服務的電腦, 兩者的作業系統及相關組態, 甚至連本性都很不一樣, 需要一種方法,模擬出一個遠端的環境, 既是獨立於本機的乾淨隔離環境, 又要保持與本機互連的彈性。最後, 為了讓整個團隊能在一致的環境下協同開發, 這種組態設定知識, 也要形諸文件, 可程式化, 可再現, 予以版本控制, 也就是所謂的"infrastructure as code"。

最簡單的答案是: 你需要**虛擬機**。

---

# Vagrant Tutorial(2) #

虛擬機技術, 就是一帖解藥。不只是上線環境的伺服器需要, 不只是伺服器維運人員需要; 就連研發者手邊的開發機, 也需要。

## 虛擬機是什麼? ##

虛擬機軟體, 讓你將任何一台電腦模擬出另外N台電腦; 這些被模擬出來的虛擬機(virtual machine), 某種程度上相當於一部完整電腦, 有自己CUP、記憶體、磁碟和I/O裝置, 又可像真實裸機(bare metal machine)一樣, 灌任何一種開機用的作業系統進去。灌好後, 在虛擬機軟體裡面把它「開機」起來, 再將視窗放大到全螢幕, 拿給不知情的第三者看, 他可能根本不知這是虛擬機呢。

行文方便, 以下稱本機電腦用以開機的作業系統為**host OS**, 虛擬機跑的則為**guest OS**。

## 虛擬機的Wish List ##

主流的虛擬機軟體, 都提供易於上手的GUI介面。可是, 對軟體研發者來說, 更需要的是可程式化的介面, 以便將虛擬機納入軟體開發工具鏈中。Ex:

1. 能不能下一些可程式化的指令, 就自動安裝一台或多台指定版本的guest OS虛擬機供我使用, 即使是最陽春的Linux也無妨? 雖已有像VirtualBoxImages.com這樣的網站, 讓你手動下載免費的guest OS裝在VirtualBox上, 但我們要的是**可程式化**。
2. 能不能下一些可程式化的指令, 就自動設定好這些虛擬機叢集的組態? 最好也能與上線主機共用同一套組態管理機制(Chef、Puppet、Ansible、Salt...), "infrastructure as code"一以貫之。
3. 能不能下一些可程式化的指令, 就自動儲存現有狀態(snapshot), 供日後快速複製增生一模一樣的虛擬機, 或是重現歷史狀態以便偵錯?
4. 在軟體層次, 我們有yum、apt之類的套件管理系統; 程式語言層次, 則有Composer、CPAN、gem、Maven、npm、NuGet、pip之類的套件管理系統。同理, 虛擬機層次, 能不能也有類似的repository中央儲存庫'。

雖然就連free且open source的VirtualBox都有提供VBoxManage之類的命令列工具, 但這些工具仍過於陽春, 很難做到上述的需求。想一步到位? 就是"**Vagrant**"。

## 流浪漢Vagrant ##

Vagrant: a person who has no place to live and no job and who asks people for money.

Mitchell Hashimoto 於 2010 年 3 月 8 日首次釋出 Vagrant 0.1.0 版，當時它的 slogan 是:

Vagrant is a tool for building and distributing virtualized development environments.

關鍵字:

- building: 協助你安裝、設定虛擬機。
- distributing: 不只打造, 還協助你儲存、散佈。
- virtualized development environments: 專為研發者量身打造的, 以虛擬機為中心的研發環境。

或許作者想讓這個工具的使用者, 像流浪漢、遊民一樣, 浪跡天涯, 隨遇而安; 不受限於機器的實體位置, 能在任何一台電腦上, 模擬出任何一種遠端主機的可程式化環境, 讓研發人員的日子更好過。

當時他是這麼描述自己:

By providing automated creation and provisioning of virtual machines using Sun’s VirtualBox, Vagrant provides the tools to create and configure lightweight, reproducible, and portable virtual environments.

關鍵字:

- automated: 自動化, 可程式化。
- creation and provisioning: 打造, 設定, 安裝, 啟動, 開通。
- Sun's s VirtualBox: 呃，當年 Sun 還沒賣給 Oracle 呢...
- lightweight: 輕量無負擔, 無須大幅改變自身習慣。
- reproducible: 可再現, infrastructure as code。
- protable: 通吃,　不挑食。

當初Vagrant是站在VirtualBox的基礎上, 用Ruby加工, 寫的一套可程式化界面。 四年半下來, Vagrant已有明顯改進, 以下提兩點與安裝Vagrant有關的部分, 畢竟, 軟體再好也要先降低安裝門檻, 才利於推廣:

1. Vagrant不再限定只能搭配VirtualBox, 多數各種主流虛擬機軟體已經都能支援。
2. 早期, 電腦必須安裝Ruby, 才能用Ruby的gem套件管理系統安裝Vagrant, 所幸, Vagrant從1.1版開始, 將Ruby相關事物包起來, 電腦就不再需要安裝Ruby才能安裝Vagrant了。

Mac OS X用戶, 想透過Homebrew Cask機制安裝VirtualBox+ Vagrant:

~~~ java

$ brew cask install vagrant    --appdir=/Applications
$ brew cask install virtualbox --appdir=/Applications

~~~

## 生出虛擬機, 只要兩道指令! ##

首先, 先選定一個工作目錄, 供Vagrant運作時寫入相關資料之用:

~~~ java

mkdir demo-1
cd demo-1

~~~

接著, 選擇想安裝哪一個guest OS。

Vargant發明人創辦的HashiCorp公司, 經營一個Vagrant Cloud服務, 作為Vagrant虛擬機(在Vagrant術語稱為box)的repository中央儲存庫。可去搜尋屬意的box安裝:

[Discover Vagrant boxes](https://atlas.hashicorp.com/boxes/search?utm_source=vagrantcloud.com&vagrantcloud=1)

常用box:

- hashicorp/precise64: Ubuntu 12.04 LTS (Precise Pangolin) 64-bit
- ubuntu/trusty64: Ubuntu 14.04 LTS (Trusty Tahr) 64-bit
- chef/centos-6.5: CentOS 6.5 x64
- chef/debian-7.4: Debian 7.4 x64
- yungsang/coreos: CoreOS，內建 Docker。
- williamyeh/ubuntu-trusty64-docker: Ubuntu 14.04 LTS，另加上 Docker 支援。
- williamyeh/debian-jessie64-docker: Debian Jessie，另加上 Docker 支援。