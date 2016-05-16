---
layout: post
title:  "Linux LPIC-1 Study Note(CH 10)"
date:   2016-05-16 14:26:00 +0800
categories: [LPIC-1, linux]
---

# Chapter 10 參數設定與Shell Script #

Shell是用戶和Linux作業系統之間的介面。Linux有多種Shell, 其中預設使用的是BASH。本章主要講述Shell的運作原理、Shell的種類、Shell一般操作以及BASH的一些特性。

## 了解BASH環境 ##

BASH是Linux上最常被使用的shell, 所有指令都必須透過這個介面傳達給核心。本節將從基本shell介紹起, 陸續介紹BASH的特性與使用方式:

### 認識Shell ###

Shell的中文意思是**殼**, 如同覆蓋在核心之外的那層外殼一般, 作為使用者與核心之間的介面。事實上我們最常使用的是Shell中的指令, 也就是Shell command, 常簡稱為command(指令)。我們透過指令與作業系統溝通, 讓作業系統完成我們交付的工作。

Shell是一個**指令語言編譯器**, 擁有內建的Shell指令集, Shell也能被系統中其他應用程式所調用。使用者輸入指令都先由Shell編譯, 然後傳給Linux核心。有些指令如切換目錄的指令「cd」, 就是包含在Shell內部(意思是使用BASH就會自動包含cd這指令)的。還有一些指令如複製指令「cp」、刪除和移動指令「rm」, 是存在於檔系統中某個目錄下單獨的程式。

對於使用者而言不用在乎是哪種類型的指令, 只要是Shell內建的指令, 或在參數PATH定義路徑之下的指令, 都可直接執行使用。如果輸入指令不是一個Shell內建指令並在路徑裡沒找到這個可執行檔, 系統會顯示一條錯誤資訊:

	ubuntu@ip-172-31-15-54:~$ psaaa
	psaaa: command not found

如果成功找到指令, 該內部指令或應用程式將被系統使用並傳給Linux核心, 根據指令的不同會有不同的輸出結果。

	ubuntu@ip-172-31-15-54:/home$ ls -l ubuntu/
	total 56
	-rw-rw-r-- 1 ubuntu ubuntu    30 May 14 15:47 aaa
	-rw-rw-r-- 1 ubuntu ubuntu 31732 May 13 16:43 apache2.conf
	-rw-rw-r-- 1 ubuntu ubuntu   100 May 13 09:49 file1
	-rw-rw-r-- 1 ubuntu ubuntu    80 May 15 22:26 first
	-rw-rw-r-- 1 ubuntu ubuntu   157 May 16 01:36 myfile.txt
	-rw-rw-r-- 1 ubuntu ubuntu     1 May 15 22:26 test1
	-rw-rw-r-- 1 ubuntu ubuntu    20 May 14 15:54 test1.txt
	-rw-rw-r-- 1 ubuntu ubuntu     0 May 15 22:26 test2

Shell的另一個重要特性是他本身就是一個**直譯式型的程式語言**, Shell程式設計語言支援絕大多數高階語言的功能, 如函數、變數、陣列與程式控制結構。Shell編程語言簡單易學, 任何指令都能放到一個可執行的Shell程式中。

當一般用戶成功登入系統, 此時系統將執行一個稱為Shell的程式, 正是Shell進程提供了指令行提示符號。default, 普通用戶用「$」作提示符號, 超級用戶root用「#」做提示符號。一旦出現Shell提示符號, 就可在提示符號輸入指令名稱及指令所需要的參數, Shell執行這些指令並將回應輸出。

### Shell的種類與差異 ###

Linux中的Shell有多種類型, 其中最常用的幾種是Bourne Shell(sh)、C Shell(csh)和Korn Shell(ksh), 以及最常使用的BASH, 如下:

#### Bourne Shell ####

這是最有名的Unix Shell, 適用所有Linux與Unix系統, 創立於1978年底, 以資助者Stephen Bourne名字命名。Bourne Shell是一個交換式的指令編譯器和指令程式編寫語言。

#### C Shell ####

Bill Joy於1980年初期, 在美國柏克萊大學開發C Shell。主要是為了讓用戶更容易使用互動式功能, 新增歷史、別名、檔案名替換、作業控制等功能。

#### Korn Shell ####

AT&T的Bell實驗室David Korn開發Korn Shell, 結合了所有C Shell的互動式特性, 並融入Bourne Shell語法, 因此Korn Shell受廣大用戶的歡迎。他還新增數學計算、程序協作(coprocess)、行內編輯(inline editing)等功能。

#### Bourne Again Shell(BASH) ####

BASH是GNU計畫的一部分, 用來替代Bourne Shell。目前大多數的Linux都以BASH作為預設Shell, 並執行sh時, 其實使用的是BASH。

BASH的名字是「Bourne-Again Shell」的縮寫, 也是個雙關語born again(再度誕生)。BASH在1987年由Brian Fox創造, 在1990年, Chet Ramey成為主要維護者。BASH是大多數Linux系統以及Mac OS X v10.4預設的Shell, 他能執行在大多數Unix風格的作業系統上。

### BASH環境變數 ###

Linux是一個多用戶的作業系統, 每個用戶登入系統後, 都會有一個專用的執行環境。通常每個用戶預設的環境都是相同的, 這個**預設環境實際上就是一組環境變量**。用戶可對自己的執行環境進行個別設定, 其方法就是修改相應的系統環境變數, 而這變數定義在 `/etc/profile` 中, 並在登入系統時會自動載入。

#### 常用環境變數 ####

- `_`(底線) : 上一個指令的最後一個參數
- `$` : 目前Shell的程序號碼
- `!` : 最後一個背景執行指令的程序號碼
- `BASH` : BASH的完整路徑名
- `BASH_ENV` : 和ENV一樣, 但只可在BASH6.0或更高的版本中設定
- `BASH_VERSION` : BASH的版本號
- `CDPATH` : cd的搜尋路徑。它是以冒號分隔的目錄列表, Shell透過它來搜尋cd指定的目標目錄
- `COLUMNS` : 設定視窗的寬度
- `EDITOR` : 文書編輯器emacs、gmacs或vi的路徑名
- `ENV` : 每一個新的BASH Shell啟動時執行的環境檔。通常賦予這個變數的檔案名是BASHrc.env的值
- `EUID` : 列出shell啟動時被初始化的用戶ID
- `FORMAT` : 用來格式化在指令管道上的time保留字的輸出
- `GROUP`: 列出當前用戶所屬的群組, 在BASH6.x以前版本中沒有
- `HISTCMD` : 當前指令的歷史編號或歷史清單中的序號
- `HISTFILE` : 指定保存指令歷史的檔案, 預設是~/.BASH_history
- `HISTFILESIZE` : 歷史檔能包含的最大行數。當給這個變數賦值, 預設是500
- `HISTSIZE` : 記錄在指令檔中的指令個數, 預設值500
- `HOME` : 家目錄, 未指定目錄時, cd指令將轉向該目錄
- `HOSTFILE` : 包含一個格式和如同`/etc/hosts`的檔案的名字, 當Shell需要補全一個主機名時將讀取該檔案
- `IFS` : 內部欄位分隔符號, 一般是空白字元(SPACE), 跳位字元(TAB)和換行符(NEWLINE), 用於指令替換, 迴圈結構中的表和讀取的輸入產生的詞的欄位劃分
- `LANG` : 語言種類, 可設定常用的英文(en)或繁體中文(zh_TW.UTF-8)
- `MAIL` : 如果該參數被設定為某個郵件檔的名字, 而MAILPATH未被設定, 當郵件到達MAIL指定的檔案時, Shell會通知用戶
- `OLDPWD` : 前一個工作目錄
- `PATH` : 指令搜尋路徑。由冒號分隔的目錄列表, 例如/usr/bin:/usr/local/bin:/bin:/sbin
- `PS1` : 主要的提示符號。顯示結果如jos@jos-desktop:~$
- `PS2` : 次要的提示符號, 預設是>
- `PWD` : 當前的工作目錄
- `RANDOM` : 每次使用該變數, 就產生一個0到32767隨機整數
- `SECONDS` : 顯示登入系統後到現在的秒數
- `SHELL` : 目前使用的SHELL
- `TMOUT` : 指定退出當前SHELL等待輸入的秒數

列出 PS1 與 PS2 的提示符號用法:

	ubuntu@ip-172-31-15-54:/home$ echo $PS1
	\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\u@\h:\w\$

- \h : 主機短名稱
- \H : 完整主機名稱
- \w : 完整的工作目錄名稱, 用戶家目錄會以`~`取代
- \W : 使用basename指令取得當前目錄名稱(目前所在的最後一層目錄)
- \u : 目前帳號的名稱
- \$ : 提示字元, 一般用戶$, root為#
- \# : 登入後第幾個使用的指令
- \v : BASH版本資訊
- \d : 日期, 格式為"星期 月 日"
- \t : 時間, 格式為24小時制"HH:MM:SS"
- \T : 時間, 格式為12小時制"HH:MM:SS"
- \A : 時間, 格式為24小時制"HH:MM"
- \@ : 時間, 格式為12小時制"am/pm"

#### 變數「?」的用處 ####

變數「?」表示前一個敘述、指令執行或條件式判斷的結果。例如數字2大於數字1, 因此[2 -gt 1]的條件判斷(gt表示greater than, 大於的意思)是正確的, 因此傳回值($?)為0

	#!/bin/bash

	[ 2 -gt 1 ]
	echo $?

執行結果:

	ubuntu@ip-172-31-15-54:/home$ . test.sh
	0
	
#### 環境變數的使用 ####

1. 查看環境變數

	要顯示所有環境變數與設定內容, 可使用env指令:
	
		ubuntu@ip-172-31-15-54:/home$ env
		XDG_SESSION_ID=7
		TERM=xterm-256color
		SHELL=/bin/bash
		SSH_CLIENT=1.165.99.118 49461 22
		SSH_TTY=/dev/pts/0
		USER=ubuntu
		LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arj=01;31:*.taz=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lz=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.axa=00;36:*.oga=00;36:*.spx=00;36:*.xspf=00;36:
		MAIL=/var/mail/ubuntu
		PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/	usr/games:/usr/local/games
		PWD=/home
		LANG=en_US.UTF-8
		SHLVL=1
		HOME=/home/ubuntu
		LOGNAME=ubuntu
		SSH_CONNECTION=1.165.99.118 49461 172.31.15.54 22
		LESSOPEN=| /usr/bin/lesspipe %s
		XDG_RUNTIME_DIR=/run/user/1000
		LESSCLOSE=/usr/bin/lesspipe %s %s
		_=/usr/bin/env
		OLDPWD=/home/ubuntu
			
	對於已知的環境變數名稱, 如果要查看定義內容, 可使用「echo $變數名」:
	
		ubuntu@ip-172-31-15-54:/home$ echo $BASH_VERSION
		4.3.11(1)-release
		
2. 宣告(或顯示)一般變數: declare

	若要設定一般變數, 指令為「declare 變數名=定義值」(不可加上「$」), 例如要設定一般變數ABC為123:
	
		declare ABC=123
		
	宣告一般變數可省略declare:
	
		ABC=123
		
3. 宣告(或顯示)環境變數:

	若要設定環境變數, 指令為「export 變數名(=定義值)」(不可加上「$」), 例如要設定60秒內沒任何動作就退出當前SHELL的環境變數TMOUT:
	
		ubuntu@ip-172-31-15-54:~$ export TMOUT=60
		ubuntu@ip-172-31-15-54:~$ echo $TMOUT
		60
		
	注意: 一般變數與環境變數的差異在於一般變數只能用在現有的Shell環境使用, 而環境變數除在現有的環境外, 還可在現有Shell所fork產生的子環境下使用, 例如延伸的程序、該Shell下執行的Shell Script等
	
	注意: 如果不加上export, 則是宣告一般變數, 加上export才會宣告為環境變數。一般變數不會出現在env這指令中, 須使用export宣告為環境變數才會出現
	
4. 變數的顯示與設定: set

	輸入set指令可列出所有變數(包括環境變數)、函數等, 若輸入`set -o`會出現set開啟的參數設定:
	
		ubuntu@ip-172-31-15-54:~$ set -o
		allexport      	off
		braceexpand    	on
		emacs          	on
		errexit        	off
		errtrace       	off
		functrace      	off
		hashall        	on
		histexpand     	on
		history        	on
		ignoreeof      	off
		interactive-comments	on
		keyword        	off
		monitor        	on
		noclobber      	off
		noexec         	off
		noglob         	off
		nolog          	off
		notify         	off
		nounset        	off
		onecmd         	off
		physical       	off
		pipefail       	off
		posix          	off
		privileged     	off
		verbose        	off
		vi             	off
		xtrace         	off
		
	下面列出set常用的參數設定:
	
	- `-C`, `-0 noclobber` : 對現有的一般檔案不得使用覆蓋「>」或是附加的方式「>>」變更檔案
	- `-n`, `-o noexec` : 讀取指令但不實際執行
	- `-P`, `-o physical` : 執行與連結有關的指令時, 會進入該連結檔所在的實際位置
	- `-t`, `-o onecmd` : 當執行一行指令後就離開當前的Shell
	- `-x`, `-o xtrace` : 執行指令時列出指令與完整的參數(例如可讓我們了解alias別名中定義的指令參數)
	- `-o vi` : 指定vi為編輯器

	如果要取消set的參數設定, 可將參數前的「-」換成「+」即可
	
5. 取消變數宣告: `unset` 或 `unv -u`

	要取消變數的宣告, 不論是一般變數或是環境變數都可使用unset這指令。例如要取消已定義的abc變數:
	
		unset abc
		
	或是
	
		unv -u abc
		
	注意: 如果使用`readonly`或`declare -f`宣告變數, 則變數不得被修改(包括取消變數宣告或重新定義變數)
	
#### 登入時套用環境變數 ####

環境變數與使用者登入的環境相關, 使用者登入, 會套用自己的環境變數。不同環境, 不同使用者, 套用不同環境變數

如果所有使用者都需套用環境變數, 可定義在/etc/profile之中。

如果是個別套用環境變數, 可定義在使用者家目錄( /home/帳號或是~ )下, 名為.profile(部分發行商使用.bash_profile)與.bashrc

### BASH的特性 ###

Bourne Again Shell(BASH)正如他的名字暗示, 是Bourne Shell的延伸, 與Bourne Shell完全相容, 並在Bourne Shell的基礎上增強很多特性。BASH也包含很多Csh和Korn Shell裡的優點, 使得BASH有很靈活和強大的程式編寫介面, 同時又有很友善的用戶介面。下面介紹BASH一些特性:

#### 指令補齊 ####

按下`Tab`鍵, BASH會幫你補齊指令並顯示在螢幕上

	lsa

按下`Tab`鍵

	lsattr
	
按兩下`Tab`鍵, 會將所有可能指令列出

	ubuntu@ip-172-31-15-54:~$ ls
	ls           lsblk        lscpu        lsinitramfs  lsof         lspgpot
	lsattr       lsb_release  lshw         lsmod        lspci        lsusb
	
按下Enter前指令不會被執行

除指令外, 一般檔案也適用指令補齊的功能。如果我們使用ls或cat之類的指令, 後面接上要列出或查看的檔案, 此時這樣的檔案就不限於指令(定義在$PATH中), 任何系統上的檔案都可使用

#### 指令歷史紀錄 ####

BASH支援指令歷史紀錄, 代表BASH保留先前已經在Shell裡輸入過的指令, 這數目取決於`HISTSIZE`的變數。BASH把先前輸入的指令以文字檔形式儲存在`HISTFILE`的BASH變數指定檔案中, 預設值為`.BASH_history`, 存放在用戶目錄中, 該檔案的檔名以一個點開頭, 表示為隱藏的, 只有輸入帶`-a`或`-A`參數的`ls`指令才可見到

可使用兩種方法顯示歷史紀錄列表:

1. 上鍵(方向鍵)
2. 指令`history`

		ubuntu@ip-172-31-15-54:~$ history
    	1  s
    	2  ls
    	3  lsb_name -a
    	4  lsa_name -a
    	5  lsb_release -a
    	6  clear
    	7  cat --help
    	8  ls
    	9  vi file1
   		10  cat -A file1
   		11  cat -b file1
   		12  cat /etc/hosts.allow
   		13  cat -b /etc/hosts.allow
   		14  exit
   		...
   		
#### 輸入與輸出重新定向 ####

輸入與輸出重定向通常與FD(檔案描述符號, File Descriptor)有關, Shell的FD通常為10個, 即0~9, 常用FD有3個, 為0(stdin, 標準輸入)、1(stdout, 標準輸出)、2(stderr, 標準錯誤輸出), 預設與鍵盤及螢幕有關

用 `<` 來改變讀入的資料通道(stdin), 使之從指定的檔案中讀入。而 > 可用來改變輸出的資料通道(stdout, stderr), 使之輸出到指定的檔案。`<` 的預設值是0, 因此 `<` 與 `<0` 是一樣的

	cmd < file_name
	
使cmd指令從file_name讀入資料, 也就是說指令cmd輸入來源是file_name

	cmd <> file_name
	
以讀寫方式把檔案file_name重定向到輸入, 檔案file_name不會被改變

	ubuntu@ip-172-31-15-54:~$ cat > file_name
	testing
	hello
	this is a test
	hello world
	ubuntu@ip-172-31-15-54:~$ cat file_name
	testing
	hello
	this is a test
	hello world
	ubuntu@ip-172-31-15-54:~$ cat > file
	this is a second test file
	hello word
	testhello
	ubuntu@ip-172-31-15-54:~$ cat > file_name < file
	ubuntu@ip-172-31-15-54:~$ cat file_name
	this is a second test file
	hello word
	testhello
	
輸出重定向與輸入重定向類似, 輸出重定向能把一個指令的輸出重定向到一個檔案裡, 而不僅是指顯示在螢幕上, `>` 的預設值是1, 因此 `>` 與 `1>` 是一樣的。

- cmd > file : 把cmd指令的標準輸出重定向到file檔案中
- cmd 1> file : 把cmd指令的標準輸出重定向到file檔案中
- cmd >> file : 把cmd指令的標準輸出重定向到file檔中(追加)
- cmd 2> file : 把cmd指令的標準錯誤輸出重定向到file檔中
- cmd 2>> file : 把cmd指令的標準錯誤輸出重定向到file檔中(追加)
- cmd > file 2>&1 : 把cmd指令的標準輸出和cmd指令的標準錯誤輸出一起重定向到file檔中。&1表示引用標準輸出的檔控制碼, 因而2>&1表示將標準錯誤重定向到標準輸出
- cmd >> file 2>&1 : 把cmd指令的標準輸出和cmd指令的標準錯誤輸出一起重定向到file檔中(追加)

舉例:

	ubuntu@ip-172-31-15-54:~$ echo 'hello world' > file2
	ubuntu@ip-172-31-15-54:~$ ls file1 file2 file3
	ls: cannot access file3: No such file or directory
	file1  file2
	ubuntu@ip-172-31-15-54:~$ ls file1 file2 file3 1>donefile
	ls: cannot access file3: No such file or directory
	ubuntu@ip-172-31-15-54:~$ ls file1 file2 file3 1>donefile 2>errorfile
	ubuntu@ip-172-31-15-54:~$ cat donefile
	file1
	file2
	ubuntu@ip-172-31-15-54:~$ cat errorfile
	ls: cannot access file3: No such file or directory
	
#### 管道 ####

管道可把一系列指令連接起來。一個指令輸出會通過管道傳給第二個指令而作為第二個指令的輸出, 第二個指令的輸出又會作為第三個指令的輸入, 以此類推。而管道的最後一個指令的輸出才會顯示在螢幕上(如果指令行裏用了輸出重定向的話, 最終輸出將會寫入到一個檔案裡)

	$ cat file_name | grep "mywork" | wc -l
	
這個管道將把cat指令(列出一個檔file_name內容)的輸出送給grep指令。grep指令在輸入字串中查找子字串mywork, grep的指令輸出則是所有包含mywork的行, 這個輸出又送給wc指令, 帶-l選項的wc將統計輸入的行數。

	ubuntu@ip-172-31-15-54:~$ cat file_name
	Things to do today
	mywork need to do work1
	mywork need to do work2
	groupwork need to do work3
	ubuntu@ip-172-31-15-54:~$ cat file_name | grep "mywork" | wc -l
	2