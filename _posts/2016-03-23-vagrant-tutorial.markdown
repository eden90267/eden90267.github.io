---
layout: post
title:  "Vagrant Tutorial"
date:   2016-03-23 09:22:00 +0800
categories: [vagrant, DepOps, VirtualBox, 虛擬化]
---

因應docker學習需要Vagrant+VirtualBox作虛擬機, 所以create這篇文章來記錄其vagrant重點。
Reference: [Vagrant Tutorial（1）雲端研發人員，你也需要虛擬機！](http://www.codedata.com.tw/social-coding/vagrant-tutorial-1-developer-and-vm/)

# Vagrant Tutorial(1)雲端研發人員, 你也需要虛擬機！ #

研發人員也需要虛擬機? 不是伺服器維運人員才需要知道的嗎?

常見原因, 研發者用的本機電腦, 與真正上線服務的電腦, 兩者的作業系統及相關組態, 甚至連本性都很不一樣, 需要一種方法,模擬出一個遠端的環境, 既是獨立於本機的乾淨隔離環境, 又要保持與本機互連的彈性。最後, 為了讓整個團隊能在一致的環境下協同開發, 這種組態設定知識, 也要形諸文件, 可程式化, 可再現, 予以版本控制, 也就是所謂的"infrastructure as code"。

最簡單的答案是: 你需要**虛擬機**。

---

# Vagrant Tutorial(2)跟著流浪漢把玩虛擬機 #

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

以ubuntu/trusty64為例, 請輸入以下指令：

~~~ java

vagrant init ubuntu/trusty64
vagrant up

~~~

有兩種可能情況：

- 如果是第一次在這個host OS上嘗試建立ubuntu/trusty64這個box, 那麼在`vagrant up`這個步驟執行時, Vagrant會先向Vagrant Cloud查詢並下載這個box檔案。
- 如果曾在這個host OS上安裝過ubuntu/trusty64, 且這個box不需升級新版, 那麼`vagrant up`就會直接抓取本機電腦裡面存放的box檔案來增生虛擬機。

就兩道指令, 你已經在本機生出一台虛擬機了！

你可以ssh進去這台虛擬機, 就像以前ssh進去遠端主機一樣：

~~~ java

$ vagrant ssh

vagrant$

~~~

注意到沒？系統提示符號改變了, 提醒你已經從host OS踏入Vagrant所管理的guest OS了。

可看看這個guest OS是否真的為Ubuntu 14.04 LTS(Trusty Tahr) 64-bit:

~~~ java

vagrant$ lsb_release -a

~~~

玩累了, 可以先登出, 回到host OS：

~~~ java

vagrant$ exit

~~~

只要位於同一個工作目錄, 可以隨時再以ssh登入：

~~~ java

vagrant ssh

~~~

登出後, 可暫時將虛擬機關機, 將CPU及記憶體等資源還給hot OS主機：

~~~ java

vagrant halt

~~~

## 小結 ##

VirtualBox、VMware、VMware Fusion、Parallels Desktop等虛擬機軟體, 讓你將任何一台電腦模擬成其他作業系統; Vagrant則進一步將這些虛擬機納入管控, 讓軟體研發流程更順暢、更有彈性。

「Vagrant + VirtualBox組合技」的成功, 啟迪了許多人, 原來虛擬機也可以這麼玩！像boot2docker就師法這種方式, 用「boot2docker + VirtualBox組合技」讓非Linux的電腦上也能跑docker, 更衍生出yungsang/boot2docker、parallels/boot2docker這些Vagrant+Docker+VirtualBox/Parallels Desktop大混血的玩法...

---

# Vagrant Tutorial(3) 細說虛擬機生滅狀態 #

最初Vagrant是站在VirtualBox的基礎, 加工出一套可程式化介面。多瞭解「上層Vagrant」是怎麼與「底層VirtualBox」互動, 會讓你更好駕馭Vagrant的威力。

## 行前準備：回復成乾淨狀態 ##

先將你的host OS恢復道尚未下載啟動guest OS虛擬機的乾淨狀態(簡單說, 就是undo那兩道指令)。先進入同樣的工作目錄, 大掃除一番。

~~~ java

cd demo-1

vagrant halt ; vagrant destroy --force
rm -rf .vagrant Vagrantfile

vagrant box remove ubuntu/trusty64
rm -rf $HOME/.vagrant.d/boxes/ubuntu-VAGRANTSLASH-trusty64

~~~

看到這些undo指令, 加上一點點倒推的想像力, 不難一瞥之前Vagrant做了哪些事。

## 行前準備：雙視窗觀察畫面 ##

開兩個視窗, 一邊終端機, 輸入Vagrant指令; 一邊是VirtualBox視窗, 讓你即時觀察輸入的上層Vagrant指令會對底層的VirtualBox產生什麼變化, 有助於領會進階操作的原理。

- `ls -al`指令查知, 工作目錄已經被完全清空。
- `vagrant box list`, 查看host OS的Vagrant box。

## 定義要用的虛擬機 ##

照Vagrant的遊戲規則, 在啟動任何一個Vagrant box之前, 必須先定義好, 在這工作目錄中, 要啟動的是哪一種box, 以及這個box該有哪些相關屬性。最常見的定義計有：

- 虛擬機的名字, 可能是global(上回ex: ubuntu/trusty64)或local(自己取的precise64、my-ubuntu1404名字)。
- 如果該虛擬機檔案主體, 並未登入在官方的Vagrant Cloud repository裡, 而是另外散置在像Vagrantbox.es所列的這些網路硬碟中, 那麼, 我們也必須明指該母體的URL為何。
- 要分配給虛擬機的主記憶體大小。
- Host與guest OS之間的post與目錄對應關係。

因此, 我們必須先在選定的工作目錄裡(或上層目錄, 或上上層目錄...), 把這些屬性寫進一個名叫**Vagrantfile**的「**Vagrant定義檔**」裡面。

Vagrant是用Ruby寫的, 而Ruby陣營又對於直接以Ruby語言當成DSL(domain-specific language)覺得天經地義理所當然, 更無比自豪, 因此, Vagrantfile本身也就是一份遵循Ruby DSL規則的Ruby源碼文件, 可擺放常數、變數、迴圈等程式語句。

每次都要寫一份Vagrant定義檔, 才能開始玩Vagrant, 也挺麻煩的。你可以複製既有的定義檔過來用, 或更簡單的方法, 只要下一道vagrant init指令, Vagrant就會自動幫你生出一份預設的版本, 現在請輸入：

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant init ubuntu/trusty64
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.

~~~

畫面上的“A `Vagrantfile` has bean placed in this directory.”

畫面上也叫我們“Please read the comments in the Vagrantfile”, 看一看Vagrant自動幫我們生出的Vagrantfile長什麼樣子：

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ cat Vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"
  
[略]

~~~

註解很多, 即使不懂Ruby, 也不難做些初步的修改。有了這份Vagrantfile當出發點, 就能再修改成各專案自己想要的設定。

Vagrantfile是控制Vagrant的重要手段, 也決定了Vagrant能做什麼、不能做什麼。完整的Vagrantfile設定內容, 請見官方文件。這裡會擇要介紹。

## 下載虛擬機檔案本體 ##

且先分析所謂「**查詢**並**下載**box檔案」動作。

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'ubuntu/trusty64' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'ubuntu/trusty64'
    default: URL: https://atlas.hashicorp.com/ubuntu/trusty64
==> default: Adding box 'ubuntu/trusty64' (v20160320.0.0) for provider: virtualbox
    default: Downloading: https://atlas.hashicorp.com/ubuntu/boxes/trusty64/versions/20160320.0.0/providers/virtualbox.box
...

~~~

`vagrant up`後期間：

- 「Loading metadata for box 'ubuntu/trusty64'」這一行, 就是所謂的「向Vagrant Cloud查詢box檔案」動作。
- 「Downloading: https...」這一行就是所謂的「向Vagrant Cloud下載box檔案」動作。

那麼下載回來的box檔案本體會存在哪裡？ 稍後回頭探討。

可用終端機視窗用`vagrant status`指令查看, 在此工作目錄中, Vagrant目前處於什麼狀態：

~~~ java

yuutekiMacBook-Pro:demo-1 eden90267$ vagrant status
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.

~~~

順便也看看, Vagrant在我們工作目錄做了什麼事？

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ ls -al
total 8
drwxr-xr-x   4 eden90267  staff   136  3 24 00:34 .
drwxr-xr-x@ 78 eden90267  staff  2652  3 23 21:31 ..
drwxr-xr-x   3 eden90267  staff   102  3 24 00:34 .vagrant
-rw-r--r--   1 eden90267  staff  3027  3 24 00:21 Vagrantfile

~~~

可看到, 工作目錄除了原本就有的定義檔Vagrantfile之外, 還多了一個.vagrant目錄, Vagrant會用它存放這個box執行個體的狀態和資料。

## Vagrant box 檔案本體 ##

剛剛是針對工作目錄看的local資訊, 現在也讓我們看看global資訊。

回顧系統裡面已經下載過哪些Vagrant box了：

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant box list
ubuntu/trusty64                     (virtualbox, 20160320.0.0)

~~~

順便找找box檔案本體被Vagrant藏在哪裡, 如果沒改變VAGRANT_HOME環境變數, default目錄會是：

Linux & Mac: `$HOME/.vagrant.d目錄`

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ ls -al /Users/eden90267/.vagrant.d
total 16
drwxr-xr-x   9 eden90267  staff   306  3 24 01:12 .
drwxr-xr-x@ 78 eden90267  staff  2652  3 23 21:31 ..
drwxr-xr-x   6 eden90267  staff   204  3 24 00:49 boxes
drwxr-xr-x   8 eden90267  staff   272  3 24 00:49 data
drwxr-xr-x   3 eden90267  staff   102  3 22 23:51 gems
-rw-------   1 eden90267  staff  1675  3 22 23:51 insecure_private_key
drwxr-xr-x   3 eden90267  staff   102  3 22 23:51 rgloader
-rw-r--r--   1 eden90267  staff     3  3 22 23:51 setup_version
drwxr-xr-x   2 eden90267  staff    68  3 24 00:49 tmp
ryuutekiMacBook-Pro:demo-1 eden90267$ ls -al /Users/eden90267/.vagrant.d/boxes
total 0
drwxr-xr-x  6 eden90267  staff  204  3 24 00:49 .
drwxr-xr-x  9 eden90267  staff  306  3 24 01:12 ..
drwxr-xr-x  4 eden90267  staff  136  3 23 00:53 bento-VAGRANTSLASH-centos-5.11
drwxr-xr-x  4 eden90267  staff  136  3 24 00:49 ubuntu-VAGRANTSLASH-trusty64
drwxr-xr-x  4 eden90267  staff  136  3 23 08:33 williamyeh-VAGRANTSLASH-docker-workshop-registry
drwxr-xr-x  4 eden90267  staff  136  3 23 00:48 williamyeh-VAGRANTSLASH-ubuntu-trusty64-docker
ryuutekiMacBook-Pro:demo-1 eden90267$ ls -al /Users/eden90267/.vagrant.d/boxes/ubuntu-VAGRANTSLASH-trusty64/
total 8
drwxr-xr-x  4 eden90267  staff  136  3 24 00:49 .
drwxr-xr-x  6 eden90267  staff  204  3 24 00:49 ..
drwxr-xr-x  3 eden90267  staff  102  3 24 00:49 20160320.0.0
-rw-r--r--  1 eden90267  staff   43  3 24 00:49 metadata_url
ryuutekiMacBook-Pro:demo-1 eden90267$ ls -al /Users/eden90267/.vagrant.d/boxes/ubuntu-VAGRANTSLASH-trusty64/20160320.0.0/
total 0
drwxr-xr-x  3 eden90267  staff  102  3 24 00:49 .
drwxr-xr-x  4 eden90267  staff  136  3 24 00:49 ..
drwxr-xr-x  6 eden90267  staff  204  3 24 00:49 virtualbox
ryuutekiMacBook-Pro:demo-1 eden90267$ ls -al /Users/eden90267/.vagrant.d/boxes/ubuntu-VAGRANTSLASH-trusty64/20160320.0.0/virtualbox/
total 853704
drwxr-xr-x  6 eden90267  staff        204  3 24 00:49 .
drwxr-xr-x  3 eden90267  staff        102  3 24 00:49 ..
-rw-r--r--  1 eden90267  staff        505  3 24 00:49 Vagrantfile
-rw-------  1 eden90267  staff  437074432  3 24 00:49 box-disk1.vmdk
-rw-------  1 eden90267  staff      10595  3 24 00:49 box.ovf
-rw-r--r--  1 eden90267  staff         25  3 24 00:49 metadata.json

# 找到了, 就是這個！！！ ‘box-desk1.vmdk’

~~~

從最後所到目錄可看出, Vagrant將下載回來的box檔案本體, 儲存成互通性較高的VMDK格式, 而不是VirtualBox原生的VDI格式。

## 關機、移除 ##

~~~ java

vagrant halt

~~~

只是暫時「關機」沒「移除」

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant status
Current machine states:

default                   poweroff (virtualbox)

The VM is powered off. To restart the VM, simply run `vagrant up`
ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant box list
ubuntu/trusty64                     (virtualbox, 20160320.0.0)

~~~

要進一步把它移除, 可以用`vagrant destroy`指令：

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Destroying VM and associated drives...

~~~

但上述動作只是從「VirtualBox虛擬機清單」上除名, 但並沒有從「Vagrant所管理維護的『已下載box清單』」上除名：

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant status
Current machine states:

default                   poweroff (virtualbox)

The VM is powered off. To restart the VM, simply run `vagrant up`
ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant box list
bento/centos-5.11                   (virtualbox, 2.2.3)
ubuntu/trusty64                     (virtualbox, 20160320.0.0)
williamyeh/docker-workshop-registry (virtualbox, 5.1)
williamyeh/ubuntu-trusty64-docker   (virtualbox, 1.10.3.20160314033456)
ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Destroying VM and associated drives...
ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant status
Current machine states:

default                   not created (virtualbox)

The environment has not yet been created. Run `vagrant up` to
create the environment. If a machine is not created, only the
default provider will be shown. So if a provider is not listed,
then the machine is not created for that environment.
ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant box list
bento/centos-5.11                   (virtualbox, 2.2.3)
ubuntu/trusty64                     (virtualbox, 20160320.0.0)
williamyeh/docker-workshop-registry (virtualbox, 5.1)
williamyeh/ubuntu-trusty64-docker   (virtualbox, 1.10.3.20160314033456)
ryuutekiMacBook-Pro:demo-1 eden90267$ ls -al /Users/eden90267/.vagrant.d/boxes/ubuntu-VAGRANTSLASH-trusty64/20160320.0.0/virtualbox/
Vagrantfile     box-disk1.vmdk  box.ovf         metadata.json
ryuutekiMacBook-Pro:demo-1 eden90267$ ls -al /Users/eden90267/.vagrant.d/boxes/ubuntu-VAGRANTSLASH-trusty64/20160320.0.0/virtualbox/
total 853704
drwxr-xr-x  6 eden90267  staff        204  3 24 00:49 .
drwxr-xr-x  3 eden90267  staff        102  3 24 00:49 ..
-rw-r--r--  1 eden90267  staff        505  3 24 00:49 Vagrantfile
-rw-------  1 eden90267  staff  437074432  3 24 00:49 box-disk1.vmdk
-rw-------  1 eden90267  staff      10595  3 24 00:49 box.ovf
-rw-r--r--  1 eden90267  staff         25  3 24 00:49 metadata.json

~~~

所以, Vagrant不會輕易把下載回來的「Vagrant box檔案本體」(即畫面中的vmdk檔)清除掉, 只是把藉著它而增生出來的「VirtualBox虛擬機執行個體(instance)」清除掉而已。也因此, 反覆執行up、halt、destroy指令不管多少次, 系統不會每次都跑去Vagrant Cloud下載那動輒數百MB的box檔案本體; 除非強制驅離$HOME/.vagrant.d裡面的庫存資料。

## 小結 ##

這回學會利用vagrant init指令, 叫Vagrant生出Vagrantfile定義檔, 定義想要的vagrant box種類及屬性。之後, 藉由VirtualBox+終端機雙視窗配置, 逐一檢視vagrant up、vagrant halt、vagrant destroy等Vagrant指令, 是如何同時作用在上層Vagrant及底層VirtualBox身上。過程中演穿插介紹實用的vagrant box list, vagrant status查詢指令。