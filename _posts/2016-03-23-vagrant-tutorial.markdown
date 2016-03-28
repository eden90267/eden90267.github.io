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

這回學會利用vagrant init指令, 叫Vagrant生出Vagrantfile定義檔, 定義想要的vagrant box種類及屬性。之後, 藉由VirtualBox+終端機雙視窗配置, 逐一檢視vagrant up、vagrant halt、vagrant destroy等Vagrant指令, 是如何同時作用在上層Vagrant及底層VirtualBox身上。

---

# Vagrant Tutorial (4) 虛擬機, 若即若離的國中之國 #

# 國中之國 #

梵蒂岡這個內陸國, 整個國土都被義大利360°包覆著, 在地理學上, 這種被單獨一個國家包覆的國家, 稱為「國中之國」°

國中之國通常都會與他的外覆國家維持著若即若離的關係, 只要兩國沒有交惡。

某方面來說, 虛擬機也是一種「國中之國」的概念; 在host OS國度中, 挖出一塊相對起來獨立自主的guest OS區域, 且與外面的host OS維持著落即落離的關係。

- 「若離」之處在於：藉由hypervisor之助, 在這國中之國裡, 可以有貌似真實且獨立的CPU、記憶體、儲存空間、網路等資源, 你可以放心在裡面自由大膽實驗, 不必擔心會污染到外面的host OS。
- 「若即」之處在於：畢竟這一切都是hypervisor的功勞。不管是全虛擬話還是半虛擬化技術, 若host OS不釋出善意分享資源, guest OS也只能含淚被禁行禁運了。

Vagrant提供兩種管道, 讓guest OS可與外界互通。本文就來探討Vagrant是怎麼處理這「國中之國 」、「若即若離」的關係。

## Guest OS與外界溝通的兩個管道 ##

Vagrant執行時輸出的畫面, 常常內含玄機, 草草略過很可惜。像`vagrant up`指令執行, 有三處值得注意：

~~~ java

# port forwarding
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)

~~~

~~~ java

    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key

~~~

~~~ java

# 共享目錄
==> default: Mounting shared folders...
    default: /vagrant => /Users/eden90267/demo-1

~~~

畫面告訴我們, 在用`vagrant up`啟動虛擬機的同時, Vagrant已經偷偷幫我們建好兩條互通管道了, 一個是透過TCP/UDP通訊埠(port forwarding), 另一個是透過共享目錄(shared folder)。畫面也告訴我們, 當我們登入guest OS時, 身份會是一個名為"vagrant"的帳號, 登入至127.0.0.1:2222。

以下帶你逐一細看這兩條互通管道。

## 共享管道之一：Port forwarding ##

`vagrant up`所建立的第一條互通管道port forwarding, 請看這兩行輸出：

~~~ java

# port forwarding
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)

~~~

這兩行告訴我們, Vagrant已將以下兩個TCP port連接起來：

- Guest OS 的 22 port(也就是SSH服務預設的TCP port)
- Host OS 的 2222 port

再用白話文解釋幾遍此處的 port forwarding 有什麼意義：

- Guest OS裡面的 22 port 封包, 會導至host OS的 2222 port;反之亦然。
- Guest OS裡面的程式, 只要是與 22 port 連線, 其實骨子裡就是在跟host OS裡的 2222 port 連線。
- Host OS裡面的程式, 只要是與 2222 port 連線, 其實骨子裡就是在跟guest OS裡的 22 port 連線。

當然啦, Vagrant之所以能做到port forwarding這一點, 也是仰賴底層的VirtualBox; 因此, 這件事也可以在VirtualBox裡檢視。請點選該虛擬機的【設定值】->【網路】->【連接埠轉送】。

由此可見, 這台虛擬機, 已經開放 127.0.0.1:2222 給虛擬機之外的「外界」透過SSH方式登入。

如果想再新增其他的port forwarding規則, 請在Vagrantfile裡, 找到以下這行:

~~~ java

# config.vm.network "forwarded_port", guest: 80, host: 8080

~~~

把開頭的註解符號#刪掉, 修改那兩個數字, 再重新啟動此虛擬機box執行個體。

## 共享管道之二：共享目錄 ##

`vagrant up`所建立的第二條互通管道shared folder(共享目錄), 請見這兩行輸出畫面：

~~~ java

==> default: Mounting shared folders...
    default: /vagrant => /Users/eden90267/demo-1

~~~

這兩行告訴我們, Vagrant已將以下兩個目錄連接起來：

- Guest OS的 /vagrant 目錄
- Host OS的工作目錄(/User/eden90267/demo-1)

當然啦, Vagrant之所以能做到「共享目錄」這一點, 也是仰賴底層的VirtualBox; 因此這件事也可以在VirtualBox檢視。

如果想再新增其他共享目錄, 請在Vagrantfile裡, 找到以下這一行：

~~~ java

  # config.vm.synced_folder "../data", "/vagrant_data"

~~~

把開頭的註解符號#刪掉, 修改那兩個字串, 再重新啟動此虛擬機box執行個體。

## Port forwarding實例：SSH ##

vagrant up輸出畫面也告訴我們, default下, 被它啟動的虛擬機, 會開放127.0.0.1:2222給外界, 以vagrant的身份, 透過SSH方式登入。

我們之前都是用vagrant ssh指令登入這台guest OS：

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ vagrant ssh
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.13.0-83-generic x86_64)
[略]
vagrant@vagrant-ubuntu-trusty-64:~$

~~~

但其實這是Vagrant幫的忙, 幫我們處理掉許多SSH登入的細節。

身為硬漢, 就要嘗試用最原始的ssh方式登入：

手動輸入該guest OS對外開放的IP位址(127.0.0.1)、TCP通訊埠(2222)、登入帳號(vagrant)及密碼(vagrant):

~~~ java

ryuutekiMacBook-Pro:demo-1 eden90267$ ssh -p 2222 vagrant@127.0.0.1
vagrant@127.0.0.1's password:
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.13.0-83-generic x86_64)
[略]
vagrant@vagrant-ubuntu-trusty-64:~$

~~~

當然啦, 平常我們不會自找麻煩多打那麼多文字及數字。以上只是確認port forwarding的作用, 順便知道vagrant ssh指令背後幫你做了哪些事情。知道隱藏在背後的細節, 將來遇到Vagrant內建工具無法幫你節省力氣的場合（譬如, 用Ansible、Capistrano等外部程式動態操控guest OS的組態及行為）, 就知道該如何餵SSH帳號密碼給外部程式使用。

## Port forwarding實例：Redis ##

實務上, 真實上線主機的OS裡面, 不只會跑SSH Server讓我們從遠端登入進去管理, 還會跑許多像是httpd、MongoDB、MySQL、Nginx、Redis等daemon, 分別綁定80、27017、3306、6379等port作為對外服務端口。

我們研發人員使用Vagrant管理虛擬機的初衷是, 想在本機端就能模擬出真實上線主機的組態, 越逼真越好。因此, 我們也需要學習如何在單獨一台電腦裡, 用Vagrant模擬出上述的組態配置。我們必須設法在Vagrant的遊戲規則中, 妥善安排host OS與guest OS之間的相互關係, 模擬出client、server machine、server service、public port等真實上線系統的組成元素。

不過, guest OS是個獨立自主的國中之國, 有自己獨立的內部網路, 與外界網路隔絕。因此, guest OS裡面的service原先所綁定的port服務端口, 被guest OS完美地包覆起來, 或者說, 被完美的封印私藏起來, 不讓外界直接觸碰, 就像「禁止通行」交通標誌一樣。畢竟這是虛擬機底層hypervisor的重大責任：資源隔離。

因此, 如果有需要把guest OS裡面的服務開放給host OS存取, 就必須請hypervisor網開一面, 個別放水port forwarding, 讓guest OS裡面的port能開放給外面相連。

聽一堆原理, 現在, 讓我們拿Redis當例子, 親手實驗一下port forwarding設定前與設定後, 到底有什麼不同。

1. 從guest OS角度, 設定Redis server組態。
2. 從host OS角度, 設定Vagrantfile, 並試圖連線至guest OS裡面的Redis server。

[What is the Redis?](http://kejyun.github.io/Laravel-4-Documentation-Traditional-Chinese/docs/redis/)

### Port forwarding設定前 ###

guest OS:

~~~ java

# 安裝 Redis
sudo apt-get install redis-server -y
[略]

# 允許Redis bind至全部network interface...
sudo sed -i -e 's/^bind/#bind/' /etc/redis/redis.conf

# 重啟 Redis, 讓新設定生效
sudo service redis-server restart

# 用redis-cli工具連線進入Redis server, 查看Redis資訊
redis-cli
127.0.0.1:6379> info clients
# Clients
connected_clients:1
client_longest_output_list:0
client_biggest_input_buf:0
blocked_clients:0
127.0.0.1:6379>
127.0.0.1:6379> exit

~~~

host OS:

~~~ java

brew install redis
redis-cli
Could not connect to Redis at 127.0.0.1:6379: Connection refused
not connected>

~~~

很可惜連不進去, 由此可見, 此刻, guest OS裡面的port 6379, 並沒開放給虛擬機以外的「外界」存取。他已被虛擬機系統完美的保護起來, 隔離起來。

### Port forwarding設定後 ###

為讓虛擬機以外的「外界」能順利碰到裡面的port, 必須針對這個port設定port forwarding規則：

1. 登出guest OS並關機
2. 修改Vagrantfile裡面的"forwarded_port"規則：

	`config.vm.network "forwarded_port", guest: 6379, host: 6379`
	
3. 將guest OS重開機

做完後, guest OS與host OS兩者的6379 port, 就會順利連接起來。

host OS:

~~~ java

# 在 host OS 裡, 用同樣的 redis-cli 工具,
# 試試看是否連得進去 guest OS 裡面的 Redis...
redis-cli
127.0.0.1:6379> info clients
# Clients
connected_clients:2
client_longest_output_list:0
client_biggest_input_buf:0
blocked_clients:0
127.0.0.1:6379>

~~~

## 把Redis塞進Docker吧！ ##

聽說最近Docker很夯, 主流大廠紛紛表態支持。試試把前面的Redis改安裝在Docker裡面？

一個典型的Linux雲端伺服器, 如果有支援所謂「Docker化(**dockerized**)」的Redis, 那麼, 他的軟體層, 通常會由Linux->Docker engine -> Docker container -> 「Docker化的Redis」層層堆疊上去。

某種程度上, Docker號稱是輕量級的虛擬化技術, 所以, Docker這裡也會再次遇到跟前面guest OS類似的「國中之國」老問題, 只不過換了個主場及主角：「Docker container裡面的port, 並沒有開放給該container以外的『外界』存取。他已被Docker engine系統完美地保護起來, 隔離起來。」解決方法也是一樣：port forwarding(在Docker世界裡, 可能會用binding、mapping、publishing等術語來稱呼port forwarding這件事)。

因此, 如果想單獨一台本機電腦裡, 用Vagrant模擬出上述的組態配置, 整個軟體層次就會需要兩個層次的port forwarding, 一次是內側的「Docker container <-> guest OS之間」, 一次是外側的「guest OS <-> host OS之間」：

現在來親手實驗吧！

~~~ java

mkdir demo-2
cd demo-2
vagrant init ubuntu/trusty64
vagrant up
vagrant ssh

~~~

然後, 參考Docker官方文件, 用以下指令替Ubuntu安裝Docker engine：

~~~ java

$ sudo apt-get install apt-transport-https
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
$ sudo bash -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
$ sudo apt-get update
$ sudo apt-get install -y lxc-docker

~~~

Reference: [透過Docker 套件庫安裝最新版本](https://philipzheng.gitbooks.io/docker_practice/content/install/ubuntu.html)

現在, 讓我們透過Docker的映像檔管理機制, 下載、安裝一份「Docker化(Dockerized)」的Redis server。我們選擇的是Docker官方製作的"redis"映像檔的最新版本(redis:latest):

~~~ java

docker pull redis:latest

~~~

不過沒那麼順利, 會遇到如下問題：

Cannot connect to the Docker daemon. Is the docker daemon running on this host?

要將current user加入到docker group裡, 所以先敲以下指令：

~~~ java

sudo usermod -aG docker vagrant

~~~

then restart the system.

~~~ java

$ exit
$ vagrant halt
$ vagrant up
$ vagrant ssh
vagrant$ docker pull redis:latest

~~~

安裝好後, 就可以開始啟動它, 不過如果只是這樣執行：

~~~ java

vagrant$ docker run --name my-redis -d redis

~~~

就少設定了在內側的「Docker container <-> guest OS之間」的port forwarding。解決方法是,  只要加上一個-p 6379 6379參數, 就可以讓container外面存取得到這個container裡面的6379 port：

~~~ java

vagrant$ docker run --name my-redis -p 6379:6379 -d redis

~~~

別忘了也要替外側的「guest OS <-> host OS之間」也做一次port forwarding。

host OS連到guest OS裡面的Docker裡面的Redis。整個概念其實蠻一致的, 就是一層一層地包覆著, 一層一層地port forwarding解除封印。以前行之有年的防火牆、NAT、reverse proxy等技術, 不也一樣嗎？

辛辛苦苦把軟體設定好了, 下一步, 就是研究該如何讓這些心血不會因意外就消失不見, 還得從頭來過。下一期將示範如何以“infrastructure as code”角度設定虛擬機組態, 以及儲存、複製增生這些設定好的組態。

---

# Vagrant Tutorial (5) 客製化虛擬機內容的幾種方法 #

先前示範在guest OS裡面, 手動設定好Redis server, 以及一份「**Docker化(Dockerized)**」的Redis server。辛辛苦苦把軟體設定好了, 下一步, 就是研究該如何存檔、散佈、自動化管理, 讓這些心血不會因為意外就消失不見, 還得整個重來過。

讓我們先**手動**將虛擬機組態設定、儲存、複製增生虛擬機等流程完整走過一遍, 之後再設法**自動化**整個流程。

依序演練以下任務：

1. 手動設定Guest OS組態
2. 打包, 存檔
3. 加進本機端Vagrant repository
4. 複製增生虛擬機執行個體
5. 散佈box檔
6. 加進Vagrant Cloud repository
7. 自動設定Geust OS組態(Provisioning)

## 手動設定Guest OS組態 ##

仍以「在guest OS中安裝Redis server」當例子。

換新工作目錄：

~~~ java

mkdir demo-redis-1
cd demo-redis-1

~~~

下一步, 準備一份Vagrantfile定義檔。

以前都是藉由`vagrant init`指令產生一份Vagrantfile供我們差遣, 這回自己硬刻, 手動將以下內容寫進Vagrantfile檔案裡面去：

~~~ java

AGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"
end

~~~

最後, 以下指令安裝Redis server進去：

~~~ java

# 安裝 Redis...
vagrant@vagrant-ubuntu-trusty-64:~$ sudo apt-get install redis-server -y
[略]

# 允許 Redis bind 至全部 network interface...
vagrant@vagrant-ubuntu-trusty-64:~$ sudo sed -i -e 's/^bind/#bind/' /etc/redis/redis.conf

# 重啟Redis, 讓新設定生效。
vagrant@vagrant-ubuntu-trusty-64:~$ sudo service redis-server restart

~~~

## 打包, 存檔 ##

可用`vagrant package`指令, 叫Vagrant將目前這個虛擬機執行個體打包起來, 整個儲存到外部檔案。依照Vagrant慣例, 儲存的檔案, 副檔名會是.box。如果不特別下 `--output` 參數, 預設的輸出檔名會是package.box

假設我們想存成redis.box檔：

~~~ java

vagrant@vagrant-ubuntu-trusty-64:~$ exit
logout
Connection to 127.0.0.1 closed.
ryuutekiMacBook-Pro:demo-redis-1 eden90267$ vagrant package --output redis.box
==> default: Attempting graceful shutdown of VM...
==> default: Clearing any previously set forwarded ports...
==> default: Exporting VM...
==> default: Compressing package to: /Users/eden90267/demo-redis-1/redis.box

# 看看存檔的情況
ryuutekiMacBook-Pro:demo-redis-1 eden90267$ ls -al
total 837456
drwxr-xr-x   5 eden90267  staff        170  3 27 00:05 .
drwxr-xr-x@ 81 eden90267  staff       2754  3 26 23:52 ..
drwxr-xr-x   3 eden90267  staff        102  3 26 23:52 .vagrant
-rw-r--r--   1 eden90267  staff        126  3 26 23:52 Vagrantfile
-rw-r--r--   1 eden90267  staff  428771854  3 27 00:06 redis.box

~~~

有了box檔母體, 就可以據以複製增生新的虛擬機執行個體; 在本機端, 甚至讓別的電腦下載回去安裝。

據Vagrant官方文件"Box File Format"的說法, box檔案會是一個壓縮檔(tag、tag.gz或zip)。以此例而言, 壓縮檔是tag.gz格式。

~~~ java

# 看看這壓縮檔裡面到底塞哪些東西...
yuutekiMacBook-Pro:demo-redis-1 eden90267$ tar ztvf redis.box
-rw-------  0 eden90267 staff 441046016  3 27 00:05 ./box-disk1.vmdk
-rw-------  0 eden90267 staff     10515  3 27 00:04 ./box.ovf
-rw-------  0 eden90267 staff      1675  3 27 00:05 ./vagrant_private_key
-rw-r--r--  0 eden90267 staff       630  3 27 00:05 ./Vagrantfile

~~~

解壓縮的內容,  其實就是tutorial (3)提過的這些東西：

Vagrant box檔案本體被Vagrant藏在哪裡？
	
如果你沒有改變VAGRANT_HOME環境變數的話, 預設目錄會是：
	
- 若host OS是Linux&Mac, 會擺在$HOME/.vagrant.d目錄。
- 若host OS是windows, 會擺在%USERPROFILE%\.vagrant.d目錄。

可見, 本機端的Vagrant repository, 其實擺的就是box檔案解壓縮後的東西, 再加上一些輔助資訊。

## 加進本機端Vagrant repository ##

可用`vagrant box add`指令, 叫Vagrant將這個虛擬機box檔案, 以指定的名字, 添加、登記到本機端的Vagrant repository。

假設我們想把它取名為my-redis-1:

~~~ java

# 先看看本地端的Vagrant repository已有哪些box:
ryuutekiMacBook-Pro:demo-redis-1 eden90267$ vagrant box list
bento/centos-5.11                   (virtualbox, 2.2.3)
ubuntu/trusty64                     (virtualbox, 20160320.0.0)
williamyeh/docker-workshop-registry (virtualbox, 5.1)
williamyeh/ubuntu-trusty64-docker   (virtualbox, 1.10.3.20160314033456)

# 把redis.box這個虛擬機檔案, 添加到本機端的 Vagrant repository, 
# 並取名為  my-redis-1
ryuutekiMacBook-Pro:demo-redis-1 eden90267$ vagrant box add --name my-redis-1 redis.box
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'my-redis-1' (v0) for provider:
    box: Unpacking necessary files from: file:///Users/eden90267/demo-redis-1/redis.box
==> box: Successfully added box 'my-redis-1' (v0) for 'virtualbox'!


# 現在, 再來看看是否真的已經有一個'my-redis-1' box生出來了
ryuutekiMacBook-Pro:demo-redis-1 eden90267$ vagrant box list
bento/centos-5.11                   (virtualbox, 2.2.3)
my-redis-1                          (virtualbox, 0)
ubuntu/trusty64                     (virtualbox, 20160320.0.0)
williamyeh/docker-workshop-registry (virtualbox, 5.1)
williamyeh/ubuntu-trusty64-docker   (virtualbox, 1.10.3.20160314033456)


# 或是, 更深入一點看本機端的Vagrant repository內容
ryuutekiMacBook-Pro:demo-redis-1 eden90267$ ls -al ~/.vagrant.d/boxes/my-redis-1/0/virtualbox/
total 861472
drwxr-xr-x  7 eden90267  staff        238  3 27 00:56 .
drwxr-xr-x  3 eden90267  staff        102  3 27 00:56 ..
-rw-r--r--  1 eden90267  staff        630  3 27 00:56 Vagrantfile
-rw-------  1 eden90267  staff  441046016  3 27 00:56 box-disk1.vmdk
-rw-------  1 eden90267  staff      10515  3 27 00:56 box.ovf
-rw-r--r--  1 eden90267  staff         25  3 27 00:56 metadata.json
-rw-------  1 eden90267  staff       1675  3 27 00:56 vagrant_private_key


# 與前面的 redis.box 壓縮檔內容對照看看...
ryuutekiMacBook-Pro:demo-redis-1 eden90267$ tar ztvf redis.box
-rw-------  0 eden90267 staff 441046016  3 27 00:05 ./box-disk1.vmdk
-rw-------  0 eden90267 staff     10515  3 27 00:04 ./box.ovf
-rw-------  0 eden90267 staff      1675  3 27 00:05 ./vagrant_private_key
-rw-r--r--  0 eden90267 staff       630  3 27 00:05 ./Vagrantfile

~~~

兩相比對, 顯而易見的, 本機端的Vagrant repository, 其實擺的就是box檔案解壓縮之後的東西, 再加上一些輔助資訊(即此例的 metadata.json)

## 複製增生虛擬機執行個體 ##

只要box檔案有添加登記到本機端的Vagrant repository, 日後就可用這個登記過的名字去vagrant init及vagrant up相同組態的虛擬機執行個體。

~~~ java

ryuutekiMacBook-Pro:~ eden90267$ mkdir demo-redis-2
ryuutekiMacBook-Pro:~ eden90267$ cd demo-redis-2
ryuutekiMacBook-Pro:demo-redis-2 eden90267$ vagrant init my-redis-1
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
ryuutekiMacBook-Pro:demo-redis-2 eden90267$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'my-redis-1'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: demo-redis-2_default_1459093697385_35365
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default:
    default: Guest Additions Version: 4.3.36
    default: VirtualBox Version: 5.0
==> default: Mounting shared folders...
    default: /vagrant => /Users/eden90267/demo-redis-2
ryuutekiMacBook-Pro:demo-redis-2 eden90267$ vagrant ssh
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.13.0-83-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Sun Mar 27 15:48:34 UTC 2016

  System load:  0.44              Processes:           82
  Usage of /:   3.5% of 39.34GB   Users logged in:     0
  Memory usage: 24%               IP address for eth0: 10.0.2.15
  Swap usage:   0%

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud


Last login: Sat Mar 26 15:55:35 2016 from 10.0.2.2

~~~

## 散步box檔 ##

有了box檔, 不只可安裝在本機端的Vagrant repository。只要把它擺在一個可供他人下載的網路空間, 甚至可讓別的電腦安裝回去, 安裝在其他電腦自己的本機端Vagrant repository。

這個所謂「可供他人下載的網路空間」, 簡單一點的, 通常會是像Dropbox、Google Drive之類的網路硬碟、雲端硬碟; 高檔一點的, 可能會是Amazon S3或CDN;有隱私要求的, 則可能放在私有檔案伺服器。

來演練一次吧！

~~~ java

cd ..
mkdir demo-redis-3
cd demo-redis-3
vim Vagrantfile

Vagrant.configure(2) do |config|
  # 這次, 改取名為 "my-redis-2"
   config.vm.box = "my-redis-2"

  # 填入該box檔案所在網址
   config.vm.box_url = "http://bit.ly/vagrantbox-redis"
end

vagrant up

~~~

途中就有這麼一行：

~~~ java

==> default: Adding box 'my-redis-2' (v0) for provider: virtualbox
    default: Downloading: http://bit.ly/vagrantbox-redis

~~~

表示Vagrant真的有試著去指定網址下載box檔案。

再往下看, 看到這一行：

~~~ java

==> default: Successfully added box 'my-redis-2' (v0) for 'virtualbox'

~~~

就來看看, 本機端的Vagrant repository是否真的已經新增了my-redis-2這個box:

~~~ java

vagrant box list

~~~

從這例子可看出, 只要別人知道box檔案的網址, 就可以依樣畫葫蘆寫一份Vagrantfile檔, 以將他下載安裝到他們自己的本機端Vagrant repository; 至於他們想替box取什麼名字, 則由他們自己決定。

現在你應該知道, 該怎麼善用像Vagrantbox.es網站所列的box檔案母體了。

## 加進Vagrant Cloud repository ##

到目前為止, 我們所示範的, 都是把box登記到本機端的repository(或其他電腦自己的本機端repository), 取得box名字也是相對於本機端。

「相對於本機端」, 意思是：my-redis-1, my-redis-2這類的box名字, 他們的有效能見範圍(like城程式語言的scope概念), 只限於該機器本身。

那麼, 像ubuntu/trusty64、hashicorp/precise64、chef/centos-6.5這種全域式的box名字, 是怎麼設定出來的？

答案是, 要向Vagrant發明人Mitchell Hashimoto所創辦的HashiCorp公司所經營的Vagrant Cloud服務辦理登記手續, Vagrant Cloud網站才會配置ubuntu/trusty64、hashicorp/precise64、chef/centos-6.5這樣專屬的全域名稱, 供世界各地的Vagrant用戶引用。

所以, Vagrant Cloud就相當於Vagrant世界的中央repository。

1. 先將box檔案上傳到一個可供他人下載的網路空間
2. 連接Vagrant Cloud homepage, Create Box, 替box取一個名字
3. 填入該box檔案所在網址
4. 發佈

發佈完畢, 其他人就可在Vagrant Cloud搜尋到他, 如果希望這是個只限特定人士才搜得到、用得到的box, 就得升級成付費會員。

~~~ java

vagrant init eden90267/ubuntu-trusty64-docker
vagrant up
vagrant ssh

~~~

這種方法的好處是, 用戶不必知道box檔案實際擺放的網址, 不必手寫Vagrantfile的config.vm.box_url設定值, 還享有Vagrant cloud網站提供的瀏覽搜尋服務協助你曝光成果。缺點則是, 如果你不是付費會員, 你的成果會被迫公開在全世界人類眼前。

## 自動設定Guest OS組態：Provisioning ##

剛剛我們先**手動**將虛擬機組態設定、儲存、複製增生虛擬機等流程完整走過一遍, 現在該設法**自動化**整個流程了。

回顧一下, 手動演練過哪些任務：

1. 手動設定Guest OS組態
2. 打包, 存檔
3. 加進本機端Vagrant repository
4. 複製增生虛擬機執行個體
5. 散佈box檔
6. 加進Vagrant Cloud repository

該怎麼自動化這些任務？

其中(2)~(5)都只是在host OS裡面串接流程而已, 很容易用script語言搞定。
「(6)加進Vagrant Cloud repository」稍微麻煩點, 需透過Vagrant Cloud API, 但這份API是帶有REST風格的, 用script語言就能串起來, 厲害一點的人, 甚至用bash + curl組合技就能搞定。

剩下「(1)手動設定Guest OS組態」這一點, 還沒介紹該如何自動化。

這段自動化環節, 有一個正規術語, 叫做「provisioning」。據Wikipedia解釋：

Server provisioning is a set of actions to prepare a server with appropriate systems, data and software, and make it ready for network operation.

Typical tasks when provisioning a server are: select a server from a pool of available servers, load the appropriate software (operating system, device drivers, middleware, and applications), appropriately customize and configure the system and the software to create or change a boot image for this server, and then change its parameters […] to find associated network and storage resources […] to audit the system.

[…] This makes the system ready for operation.

- "server provisioning is a set of actions to prepare a server"告訴我們, provisioning往往是一連串的連續動作。
- "This makes the system ready for operation"告訴我們, provisioning的目的是讓系統組態設定到可上線運作的"I'm READY!"狀態。

Vagrant提供三種自動化provisioning機制。由淺入深, 依序是：

1. inline script(內嵌腳本)
2. external script file(外部腳本檔)
3. configuration management software(組態管理軟體)

只要依照各自的語法去修改Vagrantfile, 爾後, 在以下幾種情況, Vagrant都會自動執行我們準備好的一系列自動化provisioning指令：

- vagrant up第一次執行時
- 每次執行vagrant up --provision時
- 每次執行vagrant provision時
- 每次執行vagrant reload --provision時

讓我們逐一嘗試這三種做法。

### Inline script 內嵌腳本 ###

內嵌腳本做法是, 將我們要Vagrant自動執行的一系列自動化provisioning指令, 以shell script語法, 塞入Vagrantfile的config.vm.provision設定值, 譬如說, 如果寫成這樣：

~~~ java

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.provision "shell", inline: "echo Hello, World"
end

~~~

那麼, 當在此工作目錄下, 第一次執行vagrant up, 會看到輸出畫面的最後面, 在guest OS啟動完畢後, 在port forwarding及shared folder也都配置完畢後, 還多了一行echo Hello, World指令：

~~~ java

ryuutekiMacBook-Pro:demo-test3 eden90267$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'ubuntu/trusty64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/trusty64' is up to date...
==> default: A newer version of the box 'ubuntu/trusty64' is available! You currently
==> default: have version '20160320.0.0'. The latest is version '20160323.0.0'. Run
==> default: `vagrant box update` to update.
==> default: Setting the name of the VM: demo-test3_default_1459182088540_1347
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default:
    default: Guest Additions Version: 4.3.36
    default: VirtualBox Version: 5.0
==> default: Mounting shared folders...
    default: /vagrant => /Users/eden90267/demo-test3
==> default: Running provisioner: shell...
    default: Running: inline script
==> default: stdin: is not a tty
==> default: Hello, World

~~~

當然啦, 實務上, 我們要塞的自動化指令, 不會只有echo Hello, World這麼一行而已, 萬一想塞多行？

~~~ java

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.provision "shell", 
    inline: "echo Hello, World\necho World Peace\necho I love Vagrant"
end

~~~

寫法有點蠢。

漂亮一點做法是, 利用「Vagrant本身也就是一份遵循Ruby DSL規則的Ruby源碼文件」此一事實(Day 3), 再借用Ruby的heredoc語法, 將Vagrantfile改寫成：

~~~ java

$script = <<SCRIPT
# 安裝 Redis
sudo apt-get install redis-server -y
# 允許 Redis bind 至全部 network interface...
sudo sed -i -e 's/^bind/#bind/' /etc/redis/redis.conf
# 重啟 Redis, 讓新設定生效
sudo service redis-server restart
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.provision "shell", inline: $script
end

~~~

如此一來, 程序式的provisioning指令擺在前面, 宣告式的虛擬機屬性內容擺在後面, 一邊一國明顯區隔成兩塊, 比較好維護。

### External script 外部腳本檔 ###

上述script內容, 還可以更進一步抽離到外部檔案。

譬如說, 如果我們在工作目錄裡, 寫一個install.sh檔

~~~ java

#!/bin/bash

# 安裝 Redis
sudo apt-get install redis-server -y
# 允許 Redis bind 至全部 network interface...
sudo sed -i -e 's/^bind/#bind/' /etc/redis/redis.conf
# 重啟 Redis, 讓新設定生效
sudo service redis-server restart

~~~

然後, 修改Vagrantfile的config.vm.provision設定值, 讓它指涉到外部腳本檔install.sh:

~~~ java

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.provision "shell", path: "install.sh"
end

~~~

如此一來, Vagrant便會在provisioning階段, 載入並執行這個install.sh檔。

這種寫法, 適合有潔癖的人。

### Configuration management 組態管理軟體 ###

如果上線主機早已導入組態管理機制, 也早已運用像Chef、Puppet、Ansible、Salt這類比shell script更合身的軟體, 是否也能套用在Vagrant的provisioning環節？如此一來, 就能"infrastructure as code"一以貫之了。

譬如說, 以下這份Ansible檔案, 可用來將Redis安裝在Ubuntu上：

~~~ java

---
# file: playbook.yml
# Ansible playbook for installing Redis on Ubuntu
 
- hosts: all
  sudo: True
 
  tasks:
    - name: Install the Redis package
      apt: name={{ item }} state=present update_cache=yes
      with_items: redis-server
 
    - name: let Redis bind all network interfaces, if necessary.
      lineinfile: dest=/etc/redis/redis.conf regexp="^bind 127.0.0.1" line="#bind 127.0.0.1" insertafter="^bind"
 
    - name: restart redis-server
      service: name=redis-server state=restarted enabled=yes

~~~

該怎樣將它套用在Vagrant身上呢？

有兩種做法, 硬漢法及懶人法。

硬漢法：叫Ansible直接對已經vagrant up之後的guest OS進行SSH連線, 透過此SSH通道設定guest OS組態。想當硬漢的, 就得手動輸入該guest OS對外開放的IP位址(default: 127.0.0.1)、TCP通訊埠(default: 2222), 甚至登入的帳號(vagrant)及密碼(vagrant):

~~~ java

vagrant up

# 將這次Vagrant告訴我們的SSH host:port填入Ansible inventory file
cat <<EOF > inventory
[vagrant]
127.0.0.1 ansible_ssh_port=2222
EOF

# 叫Ansible透過SSH連線, 將"playbook.yml"組態套用進去
ansible-playbook -c paramiko -u vagrant -k -vvv -i inventory playbook.yml

~~~

懶人法：首先, 編輯Vagrantfile內容, 加入config.vm.provision一系列設定值：

~~~ java

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.sudo = true 
end

~~~

接著, 直接varant up(或 vagrant up --provision 或 vagrant provision)即可, 不必準備inventory檔案, 也不用手動執行ansible-playbook程式、夾帶一堆命令列參數, Vagrant會替你打理好這些瑣事。

懶人法的確簡單多了, 不過還是得知道, 硬漢法的原理。

不過這只是Sample示範如何將組態管理軟體融入Vagrant provisioning, 實務上組態管理彼此例複雜。

### Docker, again! ###

如果想將某個Docker image自動裝進去, 該怎做？

你可能會利用剛剛學到的inline script來安裝：

~~~ java

Vagrant.configure("2") do |config|
  config.vm.box = "eden90267/ubuntu-trusty64-docker"
  
  config.vm.provision "shell", inline: "docker pull redis:latest"
end

~~~

或者, 利用Vagrant 1.6新增的"docker" provisioner語法：

~~~ java

Vagrant.configure("2") do |config|
  config.vm.box = "eden90267/ubuntu-trusty64-docker"
  
  config.vm.provision "docker", images: ["redis:latest"]
end

~~~

Vagrant 1.6對Docker的支援還不僅如此, 有興趣的可參考以下文章：

- [Feature Preview: Docker-Based Development Environments](https://www.hashicorp.com/blog/feature-preview-vagrant-1-6-docker-dev-environments.html)
- [Docker provisioner 語法](https://www.vagrantup.com/docs/provisioning/docker.html)
- [Docker provider 完整介紹](https://www.vagrantup.com/docs/docker/)

## 回顧：虛擬機 Wish List ##

Day 2曾經分析過, 軟體研發者需要以下這些「可程式化介面」, 才能將虛擬機納入軟體開發工具鏈中：

1. 自動安裝一台或多台指定版本的guest OS虛擬機。
2. 自動設定好這些虛擬機叢集的組態, 與上線主機共用同一套組態管理機制。
3. 儲存虛擬機現有狀態, 可日後回復或快速複製。
4. 虛擬機層次的repository中央儲存庫。

到目前為止, 我們已利用Vagrant逐一實現各般需求, 只剩下「多台虛擬機叢集」這一招。下回分解。