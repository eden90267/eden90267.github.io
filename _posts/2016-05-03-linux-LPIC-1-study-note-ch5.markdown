---
layout: post
title:  "Linux LPIC-1 Study Note(CH 5)"
date:   2016-05-03 15:59:00 +0800
categories: [LPIC-1, linux]
---

# Chapter 05 指令的基本操作 #

Linux的指令使用環境是Bash Shell, Shell的字面意思是**層**或**殼**, 在Linux中, **Shell是介於人機之間的一個介面**, 可透過shell將指令傳達給核心, 而核心也會通過shell將資訊輸出, 因此我們常說的command(指令), 就是shell command(層指令)的簡稱。

## 指令的入門操作 ##

### 指令使用環境 ###

在Linux之下預設的 shell 為 `bash` , 此外, 常見的還有 `Bornel shell` 、 `C shell` 、 `Korn shell` 等。下面列出指令使用的時機:

#### 處理效率 ####

特別是遠端控制, 指令是最有效率的一種方式。

舉例: 重啟FTP。 使用指令, 也就一兩個指令即可; 圖形介面, 需傳送較大的資料, 明顯速度較慢。

再者, 透過圖形介面需使用滑鼠在多視窗間切換, 步驟繁瑣。

#### 沒有圖形介面 ####

當所在的環境沒圖形介面, 就非得使用指令了。Ex: 文字介面的初始模式; 使用Putty遠端連線控制。

#### 遠端控制 ####

除非在區域網路內, 否則連線的頻寬將是一大瓶頸。一般使用指令遠端控制主機, 是最節省資源的方式。

#### 多功能的混合搭配技巧 ####

圖形視窗都是固定的指令操作, 如果要彈性設定多個指令或參數混合使用, 需指令。

### 基本操作技巧 ###

有些指令可直接執行, 但大部分指令都需添加參數才能使用。Ex: useradd 帳號名稱, 才是完整指令, 「帳號名稱」就是參數一部分。

1. 打開使用的環境
2. 挑選合適的指令
3. 添加正確的參數

	可使用 man 指令查詢參數用法

底下列出指令使用上的注意事項:

#### 路徑 ####

一般我們執行指令, 都是直接輸入指令名稱, 如 `ls` 。事實上 `ls` 位於 `/bin` 的目錄之下, 那為什麼執行 `ls` 就相當於執行 `/bin/ls`?

這就是系統上定義了指令的路徑, 讓我們輸入指令後, 會自動到這些路徑下尋找指令。若是路徑錯誤或沒有正確的指定, 可能導致錯誤的執行, 或找不到該指令。

要知道設定的路徑, 可執行已下指令:

	echo $PATH

Ex:

	ubuntu@ip-172-31-7-139:~$ echo $PATH
	/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games


一般而言, 指令位在 `/bin` 、 `/usr/bin` 、 `/sbin` 、`/usr/sbin` 之中。若執行指令卻出現 `command not found` 字樣, 首先確定該指令的位置是否落在指令的路徑中, 或是系統上根本沒有安裝該套件。

---

※經驗談

新增指令路徑(`/opt/abc` to $PATH): `export PATH=$PATH:/opt/abc` , 執行後生效, 但重新登入就沒效果了。如果要每次登入都生效, 可將此行加入到 `/etc/profile` 的最後一行。

---

#### 指令順序 ####

若在 `/bin` 以及 `/usr/bin` 之下都有 `ls` 指令, 那當執行 `ls` , 會執行哪個?第一優先是 shell 內建的指令, 再來才是路徑中的設定順序, 因此若有相同名稱指令, 須注意其順序設定, 或直接輸入完整路徑。

---

※經驗談

指令執行順序是安全上很大的考量。Ex: 如果 `/tmp` 也在指令執行的路徑中, 並且是第一優先, 那只要在 `/tmp` 之下放個 `ls` , 則會優先執行 `/tmp/ls` 而非正確的 `/bin/ls` 。任何人對 `/tmp` 之下的檔案都有修改權限, 因此這樣設定會造成很大的危害。

---

#### 參數順序 ####

一般除特殊狀況, 參數是沒有順序的, Ex: 輸入「-a -v」與輸入「-v -a」以及「-av」效果相同。但若參數後指定要接檔案或特殊物件, 如「-p cmd1 -v cmd2」, 則不能任意變更順序。

#### 常用參數 ####

- `--help`: 顯示說明畫面
- `--version`, `-V`: 顯示版本資訊
- `-v`: 繁瑣模式, 顯示完整的執行過程
- `-i`: 指定介面/交談模式
- `-o`: 輸出介面
- `-l`: 長列表
- `-q`, `-s`: 安靜模式, 不列出任何輸出或是錯誤訊息
- `-R`: 遞迴處理, 連同目錄下所有子目錄一起處理
- `-z`: 壓縮

### 基本的Shell內建指令 ###

就是當我們登入 Shell 時就可使用的指令, 內建於 Shell 中, 而不是以檔案形式存在的指令。基本 Shell 內建指令主要有:

- `alias`
- `bg`
- `cd`
- `declare`
- `enable`
- `exit`
- `export`
- `help`
- `history`
- `jobs`
- `suspend`
- `ulimit`
- `umask`
- `unaias`
- `unset`

分述如下:

#### alias: 定義指令及參數的別名 ####

語法: alias [-p] [別名] [=指令]

- `-p`: 列出目前所有別名設定(可加可不加, 結果相同)

別名: 列出指定的別名
別名 = 指令: 設定該指令的別名

列出現有的別名設定

	# alias

若要將 `ls -al` 的別名設為 `ll`:

	# alias ll='ls -al'

#### cd: 切換目錄 ####

語法: cd

切換目錄到 `/opt` :

	# cd /opt

切換目錄到上一層目錄:

	# cd ../

#### declare: 宣告 shell 變數 ####

語法: declare [參數] name[=value]

- `-i`: 先算出運算式, 再將結果傳給變數
- `-p`: 宣告的變數為 shell 變數
- `-r`: 宣告的變數為唯讀
- `-x`: 宣告的變數為環境變數

顯示目前的 shell 變數:

	# declare

要顯示所有的環境變數:

	# declare -x

#### enable: 啟動或關閉 shell 的內建指令 ####

語法:

- enable [-a]
- enable [-n] 指令

- `-a`: 顯示shell所有啟動的指令
- `-n` 指令: 關閉shell內建的特定指令

顯示目前shell內建指令:

    ubuntu@ip-172-31-7-139:~$ enable -a
    enable .
    enable :
    enable [
    enable alias
    enable bg
    enable bind
    enable break
    enable builtin
    enable caller
    enable cd
    enable command
    enable compgen
    enable complete
    enable compopt
    enable continue
    enable declare
    enable dirs
    enable disown
    enable echo
    enable enable
    enable eval
    enable exec
    enable exit
    enable export
    enable false
    enable fc
    enable fg
    enable getopts
    enable hash
    enable help
    enable history
    enable jobs
    enable kill
    enable let
    enable local
    enable logout
    enable mapfile
    enable popd
    enable printf
    enable pushd
    enable pwd
    enable read
    enable readarray
    enable readonly
    enable return
    enable set
    enable shift
    enable shopt
    enable source
    enable suspend
    enable test
    enable times
    enable trap
    enable true
    enable type
    enable typeset
    enable ulimit
    enable umask
    enable unalias
    enable unset
    enable wait

若要關閉shell內建的指令:

	# enable -n cd

若要加回shell內建指令:

	# enable cd

#### exit: 離開目前的shell ####

語法: exit

若執行 `/bin/bash` 進入另一個 shell, 鍵入 `exit` 則會退回來原來 shell, 執行過程如下:

    ubuntu@ip-172-31-7-139:~$ /bin/bash
    ubuntu@ip-172-31-7-139:~$ exit
    exit
    ubuntu@ip-172-31-7-139:~$

#### export: 宣告環境變數 ####

語法: export [參數] 變數名稱 [=變數設定值]

- `-f`: 設定變數名稱為某一函數的名稱
- `-n`: 移除變數名稱
- `-p`或不加: 列出 shell 上的環境變數

宣告DOG這個變數的內容為happy1234:

	ubuntu@ip-172-31-7-139:~$ export DOG=happy1234
	ubuntu@ip-172-31-7-139:~$ echo $DOG
	happy1234

列出目前的環境變數:

    ubuntu@ip-172-31-7-139:~$ export
    declare -x DOG="happy1234"
    declare -x HOME="/home/ubuntu"
    declare -x LANG="en_US.UTF-8"
    declare -x LESSCLOSE="/usr/bin/lesspipe %s %s"
    declare -x LESSOPEN="| /usr/bin/lesspipe %s"
    declare -x LOGNAME="ubuntu"
    declare -x LS_COLORS="rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arj=01;31:*.taz=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lz=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.axa=00;36:*.oga=00;36:*.spx=00;36:*.xspf=00;36:"
    declare -x MAIL="/var/mail/ubuntu"
    declare -x OLDPWD
    declare -x PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
    declare -x PWD="/home/ubuntu"
    declare -x SHELL="/bin/bash"
    declare -x SHLVL="1"
    declare -x SSH_CLIENT="43.251.79.36 56677 22"
    declare -x SSH_CONNECTION="43.251.79.36 56677 172.31.7.139 22"
    declare -x SSH_TTY="/dev/pts/0"
    declare -x TERM="xterm"
    declare -x USER="ubuntu"
    declare -x XDG_RUNTIME_DIR="/run/user/1000"
    declare -x XDG_SESSION_ID="7"

#### help: shell 內建指令說明 ####

語法: help shell內建指令

	ubuntu@ip-172-31-7-139:~$ help exit
	exit: exit [n]
	    Exit the shell.

	    Exits the shell with a status of N.  If N is omitted, the exit status
	    is that of the last command executed.

#### history: 列出使用過的指令 ####

語法: history [欲查詢最近使用過的指令個數] [參數]

- `-c`: 清除之前history紀錄
- `-d 編號`: 刪除history紀錄中指定編號的那一項
- `-n 歷史檔案`: 讀取尚未讀到的歷史紀錄
- `-r 歷史檔案`: 讀取history檔案, 不會加上紀錄
- `-w 歷史檔案`: 覆寫原有的history檔案

列出最近5個使用過的指令:

	ubuntu@ip-172-31-7-139:~$ history 5
	   32  echo $DOG
	   33  export
	   34  help exit
	   35  history --help
	   36  history 5

#### set: 設定變數 ####

語法: set [參數] 變數名稱 數值

- `-C` , `-o noclobber` : 對於現有的一般檔案不得使用覆蓋">"或是附加的方式">>"變更檔案
- `-n` , `-o noexec` : 讀取指令但不實際執行
- `-P` , `-o physical` : 執行與連結有關的指令時, 會進入該連結檔所在的實際位置
- `-t` , `-o onecmd` : 當執行一行指令後就離開當前的Shell
- `-x` , `-o xtrace` : 執行指令時列出指令與完整的參數(例如可讓我們了解alias別名中定義的指令參數)
- 無 , `-o vi` : 指定vi為編輯器

將vi設為預設的編輯器:

	$ set -o vi

#### unalias: 移除別名設定 ####

語法: unalias [參數] [別名]

- `-a` : 移除所有已定義的別名

將已定義的別名 ll 移除:

	$ unalias ll

#### unset: 移除變數設定 ####

語法: unset [參數] 變數

- `-f` : 僅將函數移除
- `-v` : 僅將變數移除

移除變數 num 的設定:

	$ unset num

## 指令的使用技巧 ##

### 指令的結合使用 ###

指令中除單一指令的使用外, 常常會將多指令混合使用, 如管線(|)、導向(>或>>)與指令中的指令等, 分述如下:

#### 管線的用法 ####

`指令一 [參數] | 指令二 [參數]` , 也就是將 `指令一 [參數]` 的輸出結果傳到 `指令二 [參數]` , 透過指令二的處理後才輸出到螢幕上。

Ex: `ls /etc | more` , 將 `ls /etc` 的結果透過 more 分頁輸出。

#### 特殊符號: 「;」、「&&」與「||」用法 ####

- 「指令一 ; 指令二」: 指令一執行完畢後(無論是否正確執行)執行指令二
- 「指令一 && 指令二」: 指令一正確執行完畢(exit回傳值為0)後才執行指令二
- 「指令一 || 指令二」: 指令一錯誤執行(exit回傳值為1)後才執行指令二(指令一若正確執行則不執行指令二)

#### 指令中的指令 ####

許多指令在執行後, 會進入該指令的操作模式, 如 alpine、fdisk、pico、top等, 進入後我們必須要使用該指令中的指令, 才能正確執行, 一般要離開這樣的指令, 可輸入 exit、q、quit或按下`Ctrl+C`。

### 指令的重新導向 ###

#### 關於輸入、輸出和錯誤輸出 ####

一般我們會直接輸入指令與參數來達成我們要的功能, 而指令的執行結果多半顯示在螢幕上。在這裡介紹的標準輸入與標準輸出, 不只是單純輸入或螢幕顯示, 而是透過導向符號提供更多樣的輸入與輸出方式。

輸入與輸出導向通常與FD(文件描述符號, File Descriptor)有關, Shell的FD有10個, 即0~9, 常用FD有3個

- 0 (stdin, 標準輸入)
- 1 (stdout, 標準輸出)
- 2 (stderr, 標準錯誤輸出)

預設與鍵盤與螢幕相關。

#### 輸入導向 ####

很多指令可以透過參數來指定輸入的檔案名稱, 但若是該指令沒這樣的參數, 就能透過輸入導向解決問題。

- cmd < file_name

	cmd指令從file_name讀入資料, 也就是說指令cmd的輸入來源是file_name

- cmd > output_file < input_file

	cmd指令從input_file中讀入資料, 處理完畢後再將資料寫入output_file

- cmd <> file_name

	cmd指令從file_name中讀入資料, 處理完畢後再將資料寫回相同檔案file_name

舉例:

    ubuntu@ip-172-31-7-139:~$ cat > file_name
    testing
    hello
    this is a test
    hello world
    ubuntu@ip-172-31-7-139:~$ cat file_name
    testing
    hello
    this is a test
    hello world

類似的, 我們可讓cat從testfile獲得輸入資料, 然後輸出給檔案file_name

    ubuntu@ip-172-31-7-139:~$ cat > testfile
    this is a second file
    hello world
    test hello
    ubuntu@ip-172-31-7-139:~$ cat > file_name < testfile
    ubuntu@ip-172-31-7-139:~$ cat file_name
    this is a second file
    hello world
    test hello

#### 輸出導向 ####

如果我們希望將執行結果存到檔案、其他終端機介面或是特定裝置上, 就會使用「輸出導向」這功能。

不加導向符號「>」的意思是將結果輸出到螢幕上, 若是在導向後面加上檔案名稱, 則會將指令的執行結果輸出到指令的檔案, Ex: `ls > temp.txt` 。「>」與「>>」差異在於前是覆蓋, 後者是附加。

#### 正確輸出與錯誤輸出 ####

使用導向符號「>」的預設值是1, 因此「>」與「1>」是一樣的。

語法如下:

- `cmd > file` , 把 cmd 指令的標準輸出導向到file檔案中
- `cmd 1> file` , 把 cmd 指令的標準輸出導向到file檔案中
- `cmd >> file` , 把 cmd 指令的標準輸出導向到file檔案中(追加)
- `cmd 2> file` , 把 cmd 指令的標準錯誤輸出導向到file檔案中
- `cmd 2>> file`, 把 cmd 指令的標準錯誤輸出導向到file檔案中(附加)
- `cmd > file 2>&1` , 把 cmd 指令的標準輸出和 cmd 指令的標準錯誤輸出一起導向到file檔案中。&1 表示引用標準輸出的檔案控制碼, 因而 2>&1 表示將標準錯誤導向到標準輸出
- `cmd >> file 2>&1` , 把 cmd 指令的標準輸出和 cmd 指令的標準錯誤一起導向到file檔案中(附加)

範例:

	ubuntu@ip-172-31-7-139:~$ echo 'hello world' > file3

假設file1與file2不存在, file3存在, 透過 `ls` 列出這三個檔案

	ubuntu@ip-172-31-7-139:~$ ls -l file1 file2 file3
	ls: cannot access file1: No such file or directory
	ls: cannot access file2: No such file or directory
	-rw-rw-r-- 1 ubuntu ubuntu 12 May  6 08:57 file3

前兩筆顯示file1與file2不存在(錯誤輸出), file3正確列出(標準輸出)

    ubuntu@ip-172-31-7-139:~$ ls file1 file2 file3 1>donefile
    ls: cannot access file1: No such file or directory
    ls: cannot access file2: No such file or directory
    ubuntu@ip-172-31-7-139:~$ cat donefile
    file3
    ubuntu@ip-172-31-7-139:~$ ls file1 file2 file3 2>errorfile
    file3
    ubuntu@ip-172-31-7-139:~$ cat errorfile
    ls: cannot access file1: No such file or directory
    ls: cannot access file2: No such file or directory
    ubuntu@ip-172-31-7-139:~$ ls file1 file2 file3 1>donefile 2>errorfile

## 常用指令介紹 ##

### 檔案內容與文字處理 ###

如果要變動檔案內容, 最簡單方式就是開啟檔案編輯, 但如果只要抽取檔案部分資料、特定欄位, 或是特定字元、字串的取代, 就不需開啟文字編輯器加以編輯, 可直接使用指令完成這些動作。

#### awk: 文字資料的進階處理 ####

語法: awk [參數] [輸出條件] [檔案]

參數:

- `-F"字元"` : `--Field-separator="字元"`, 以指定的字元作為分隔欄位
- `--help` : 顯示說明畫面
- `--version` : 顯示版本資訊

保留變數:

- ARGC : 指令執行所使用的參數個數(不包含awk本身)
- ARGV : 指令執行所使用的參數, 以陣列方式表達
- CONVFMT : 數字輸出的格式
- FILENAME : 所輸入的檔案名稱
- FS : 每欄所使用的分割字元
- IGNORECASE : 忽略大小寫
- NF : 欄位的個數
- NR : 輸入紀錄的編號
- OFMT : 字元輸出的格式
- OFS : 數字輸出的格式
- ORS : 每筆資料的輸出格式, 預設為換行
- RS : 每筆資料的輸入格式, 預設為換行

輸出條件:

- `index(字串, 子字串)` : 列出字串當中的子字串所在的位置
- `length(字串)` : 列出字串的長度
- `print(字串)` : 列出字串
- `split(字元, 陣列, 字串)` : 將字串使用分隔字元切割後, 存入陣列中
- `substr(字串, m, n)` : 列出字串後, 從位置 m 到位置 n 的字元

顯示 `/etc/passwd` 的內容:

    ubuntu@ip-172-31-7-139:~$ awk '{ print }' /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
    bin:x:2:2:bin:/bin:/usr/sbin/nologin
    sys:x:3:3:sys:/dev:/usr/sbin/nologin
    sync:x:4:65534:sync:/bin:/bin/sync
    games:x:5:60:games:/usr/games:/usr/sbin/nologin
    man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
    lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
    mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
    news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
    uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
    proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
    www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
    backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
    list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
    irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
    gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
    nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
    libuuid:x:100:101::/var/lib/libuuid:
    syslog:x:101:104::/home/syslog:/bin/false
    messagebus:x:102:106::/var/run/dbus:/bin/false
    landscape:x:103:109::/var/lib/landscape:/bin/false
    sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
    pollinate:x:105:1::/var/cache/pollinate:/bin/false
    ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash

要將 /etc/passwd 當中的內容以冒號為分割, 取出第一位與第六位:

    ubuntu@ip-172-31-7-139:~$ awk -F":" '{ print $1 $6 }' /etc/passwd
    root/root
    daemon/usr/sbin
    bin/bin
    sys/dev
    sync/bin
    games/usr/games
    man/var/cache/man
    lp/var/spool/lpd
    mail/var/mail
    news/var/spool/news
    uucp/var/spool/uucp
    proxy/bin
    www-data/var/www
    backup/var/backups
    list/var/list
    irc/var/run/ircd
    gnats/var/lib/gnats
    nobody/nonexistent
    libuuid/var/lib/libuuid
    syslog/home/syslog
    messagebus/var/run/dbus
    landscape/var/lib/landscape
    sshd/var/run/sshd
    pollinate/var/cache/pollinate
    ubuntu/home/ubuntu

#### col: 過濾保留字元 ####

語法: col [參數]

- `-b` : 過濾所有控制字元, 包含RLF與HRLF
- `-f` : 過濾RLF, 但保留HRLF
- `-l 列數` : 定義記憶體中緩衝區列數, 預設值為 128 列
- `-p` : 強迫不過濾未知的字元

將testfile當中的RLF字元過濾:

	ubuntu@ip-172-31-7-139:~$ col -f < testfile
	this is a second file
	hello world
	test hello

將cat的線上說明(man)輸出至檔案cat.txt, 並過濾所有控制字元

	ubuntu@ip-172-31-7-139:~$ man cat | col -b > testfile

#### cut: 擷取檔案中每一行的指定範圍 ####

語法: cut [參數] [檔案名稱]

- `-b 輸出範圍` : --bytes=LIST, 輸出指定的bytes數或是範圍
- `-c 輸出範圍` : --characters=LIST, 輸出指定的字元數或是範圍
- `-d 分隔字元` : --delimiter=DELIM, 指定分隔欄位的字元
- `-f 輸出範圍` : --fields=LIST, 設定輸出的範圍
- `-s` : --only-delimited, 該行沒有分隔欄位字元, 則不顯示該行
- `--help`
- `--version`

/etc/passwd的內容:

    ubuntu@ip-172-31-7-139:~$ cat /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
    bin:x:2:2:bin:/bin:/usr/sbin/nologin
    sys:x:3:3:sys:/dev:/usr/sbin/nologin
    sync:x:4:65534:sync:/bin:/bin/sync
    games:x:5:60:games:/usr/games:/usr/sbin/nologin
    man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
    lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
    mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
    news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
    uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
    proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
    www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
    backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
    list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
    irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
    gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
    nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
    libuuid:x:100:101::/var/lib/libuuid:
    syslog:x:101:104::/home/syslog:/bin/false
    messagebus:x:102:106::/var/run/dbus:/bin/false
    landscape:x:103:109::/var/lib/landscape:/bin/false
    sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
    pollinate:x:105:1::/var/cache/pollinate:/bin/false
    ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash

將前三個字母抽出:

    ubuntu@ip-172-31-7-139:~$ cut -b 1-3 /etc/passwd
    roo
    dae
    bin
    sys
    syn
    gam
    man
    lp:
    mai
    new
    uuc
    pro
    www
    bac
    lis
    irc
    gna
    nob
    lib
    sys
    mes
    lan
    ssh
    pol
    ubu

使用:為分隔欄位字元

    ubuntu@ip-172-31-7-139:~$ cut -d : -f 1 /etc/passwd
    root
    daemon
    bin
    sys
    sync
    games
    man
    lp
    mail
    news
    uucp
    proxy
    www-data
    backup
    list
    irc
    gnats
    nobody
    libuuid
    syslog
    messagebus
    landscape
    sshd
    pollinate
    ubuntu

將/etc/passwd當中的帳號與根目錄欄位列出:

    ubuntu@ip-172-31-7-139:~$ cut -d : -f 1,6 /etc/passwd
    root:/root
    daemon:/usr/sbin
    bin:/bin
    sys:/dev
    sync:/bin
    games:/usr/games
    man:/var/cache/man
    lp:/var/spool/lpd
    mail:/var/mail
    news:/var/spool/news
    uucp:/var/spool/uucp
    proxy:/bin
    www-data:/var/www
    backup:/var/backups
    list:/var/list
    irc:/var/run/ircd
    gnats:/var/lib/gnats
    nobody:/nonexistent
    libuuid:/var/lib/libuuid
    syslog:/home/syslog
    messagebus:/var/run/dbus
    landscape:/var/lib/landscape
    sshd:/var/run/sshd
    pollinate:/var/cache/pollinate
    ubuntu:/home/ubuntu

#### expand: 將Tab取代為空白鍵 ####

語法: expand [參數] [檔案名稱]

- `-i` : --initial, 如果Tab後面還有文字,則不取代
- `-t` :　--tabs=NUMBER, -t後面加上數字, 可指定用多少空白取代tab(default is 8)
- `--help`
- `--version`

使用cat查詢檔案內容, 並用expand將Tab改為兩個空白:

    ubuntu@ip-172-31-7-139:~$ cat /etc/hosts
    127.0.0.1 localhost

    # The following lines are desirable for IPv6 capable hosts
    ::1 ip6-localhost ip6-loopback
    fe00::0 ip6-localnet
    ff00::0 ip6-mcastprefix
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters
    ff02::3 ip6-allhosts
    ubuntu@ip-172-31-7-139:~$ cat /etc/hosts | expand -t 2
    127.0.0.1 localhost

    # The following lines are desirable for IPv6 capable hosts
    ::1 ip6-localhost ip6-loopback
    fe00::0 ip6-localnet
    ff00::0 ip6-mcastprefix
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters
    ff02::3 ip6-allhosts

	ubuntu@ip-172-31-7-139:~$ vi testhosts
	ubuntu@ip-172-31-7-139:~$ cat testhosts
	127.0.0.1       localhost
	ubuntu@ip-172-31-7-139:~$ cat testhosts | expand -t 2
	127.0.0.1 localhost

#### unexpand: 將空白鍵取代為 Tab ####

語法: unexpand [參數] [檔案名稱]

- `-a` : --all, 取代所有空白,包括一開始的空白
- `--first-only` : 僅取代一行開頭的空白
- `-t` : --tabs=N, -t後面加上數字,可指定用多少空白取代tab(default is 8)
- `--help`
- `--version`

使用unexpand將兩個空白改為Tab:

	ubuntu@ip-172-31-7-139:~$ cat readme.txt
	This  is  new  line
	A  B  C
	ubuntu@ip-172-31-7-139:~$ unexpand -t 2 readme.txt
	This    is      new      line
	A        B      C

#### sed: 檔案內容修改 ####

語法: sed [參數] [語法] [檔案名稱]

- `-n` : --quiet, 安靜模式, 自動執行
- `-l N` : --line-length, 指定每一行最多N字元,超過自動拆行
- `-s` : --separate, 將檔案視為分離的, 而不是單獨連續的長字串
- `--help`
- `--V`

sed的語法眾多且複雜, 以下僅列舉兩個常用的語法:

	'[範圍][動作]/[原字串]/[新字串]/[動作]'
	'[字串]/[動作]/[原字串]/[新字串]/[動作]'

testfile檔案內容:

    ubuntu@ip-172-31-7-139:~$ cat testfile
    this is line 1
    this is line 2
    this is line 2
    this is line 3
    this is line 3

將testfile的2-3行刪除:

    ubuntu@ip-172-31-7-139:~$ sed '2, 3d' testfile
    this is line 1
    this is line 3
    this is line 3

將內容is字串換成paper(僅換每行的第一個is、換每行的所有is):

    ubuntu@ip-172-31-7-139:~$ sed 's/is/paper/' testfile
    thpaper is line 1
    thpaper is line 2
    thpaper is line 2
    thpaper is line 3
    thpaper is line 3
    ubuntu@ip-172-31-7-139:~$ sed 's/is/paper/g' testfile
    thpaper paper line 1
    thpaper paper line 2
    thpaper paper line 2
    thpaper paper line 3
    thpaper paper line 3

#### sort: 將文字檔內容重新排序 ####

語法: sort [參數] [檔案名稱]

- `-b` : --ignore-leading-blanks, 忽略一開始的空白
- `-d` : --dictionary-order, 只考慮空白、數字字元、與英文字母
- `-f` : --ignore-case, 忽略大小寫, 也就是大小寫視為相同
- `-g` : --general-numeric-sort, 只比較一般的數字部分
- `-i` : --ignore-nonprinting, 只考慮可以映出的字串
- `-M` : --month-sort, 比較月份, 如Jan<Feb<...<Dec
- `-n` : --numeric-sort, 根據字串的數值比較
- `-r` : --reverse, 將結果以相反的方式呈現
- `--help`
- `--version`

將w的輸出, 透過sort依照第五個欄位排序:

	ubuntu@ip-172-31-7-139:~$ w -h | sort -k 5
	ubuntu   pts/0    43.251.79.36     05:57    7.00s  0.51s  0.00s w -h

testfile內容:

    ubuntu@ip-172-31-7-139:~$ cat testfile
    2 this is line 1
    3 this is line 5
    5 this is line 2
    1 this is line 4
    4 this is line 3

若直接使用sort, 則會依照最小的欄位排序:

    ubuntu@ip-172-31-7-139:~$ sort testfile
    1 this is line 4
    2 this is line 1
    3 this is line 5
    4 this is line 3
    5 this is line 2

若以第五個欄位排序:

    ubuntu@ip-172-31-7-139:~$ sort -k 5 testfile
    2 this is line 1
    5 this is line 2
    4 this is line 3
    1 this is line 4
    3 this is line 5

#### tr: 轉換或變更檔案中的字元 ####

語法: tr [參數] 字串一 [字串二]

- `-c` : --complement(補充), 取代或刪除不包含字串一的字元
- `-d` : --delete, 刪除字串一所包含的字元, 此參數不需要字串二
- `-s` : --squeeze-repeats, 當字串一連續出現時, 取代為一個, 此參數不需要字串二
- `-t` : --truncate-set1, 若字串一與二的長度不同, 指定字串一的長度給字串二
- `--help`
- `--version`

將testfile的檔案內容透過tr將is字元分別換成mn:

	ubuntu@ip-172-31-7-139:~$ cat > testfile
	this is line 1
	this is line 2
	this is line 2
	this is line 3
	this is line 3
	ubuntu@ip-172-31-7-139:~$ cat testfile
	this is line 1
	this is line 2
	this is line 2
	this is line 3
	this is line 3
	
	ubuntu@ip-172-31-7-139:~$ cat testfile | tr is mn
	thmn mn lmne 1
	thmn mn lmne 2
	thmn mn lmne 2
	thmn mn lmne 3
	thmn mn lmne 3
	
將testfile的檔案內容小寫全部換成大寫:

	ubuntu@ip-172-31-7-139:~$ cat testfile | tr a-z A-Z
	THIS IS LINE 1
	THIS IS LINE 2
	THIS IS LINE 2
	THIS IS LINE 3
	THIS IS LINE 3
	
#### uniq: 顯示並刪除檔案中重複的行 ####

語法: uniq [參數] [輸入檔] [輸出檔]

- `-c` : --count, 在每一行的左邊顯示重複的次數
- `-d` : --repeated, 僅顯示重複出現的行
- `-f 欄位` : --skip-fields=N, 不比較指定的欄位(行數)
- `-i` : --ignore-case, 忽略大小寫
- `-s 字元位置` : --skip-chars=N, 不比較指定的字元
- `-u` : --unique, 僅顯示出現一次的行
- `-w 字元數` : --check-chars=N, 指定每行僅比較前幾個字元
- `--help`
- `--version`

將檔案唯一化:

	ubuntu@ip-172-31-7-139:~$ cat testfile
	this is line 1
	this is line 2
	this is line 2
	this is line 3
	this is line 3
	ubuntu@ip-172-31-7-139:~$ uniq testfile
	this is line 1
	this is line 2
	this is line 3
	
顯示該檔案重複出現的行:

	ubuntu@ip-172-31-7-139:~$ uniq -d testfile
	this is line 2
	this is line 3
	
#### split: 分割檔案 ####

語法: split [參數] [檔案名稱]

- `-a N` : --suffix-length=N, 分割檔檔名最末尾的長度為N, 預設為2
- `-b N` : --bytes=SIZE, 每個檔案輸出的大小, 單位為bytes
- `-c N` : --line-bytes=SIZE, 每個輸出檔中每行的大小, 單位為bytes
- `-d` : --numeric-suffixes, 分割檔檔名末尾以數字依序列出(01、02...), 而不使用英文字母列出(aa、ab...)
- `-l N` : --lines=NUMBER, 每個檔案的行數最多為N
- `--verbose` : 出現錯誤時, 顯示在螢幕上
- `--help`
- `--version`

將apache2.conf分割為小檔, 每個檔案最多為1000bytes:

	ubuntu@ip-172-31-7-139:~/apache_split$ ls
	apache2.conf
	ubuntu@ip-172-31-7-139:~/apache_split$ ll
	total 40
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:46 ./
	drwxr-xr-x 5 ubuntu ubuntu  4096 May 11 19:44 ../
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 11 19:46 apache2.conf
	ubuntu@ip-172-31-7-139:~/apache_split$ split -b 1000 apache2.conf
	ubuntu@ip-172-31-7-139:~/apache_split$ ls
	apache2.conf  xac  xaf  xai  xal  xao  xar  xau  xax  xba  xbd
	xaa           xad  xag  xaj  xam  xap  xas  xav  xay  xbb  xbe
	xab           xae  xah  xak  xan  xaq  xat  xaw  xaz  xbc  xbf
	ubuntu@ip-172-31-7-139:~/apache_split$ ll
	total 168
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:47 ./
	drwxr-xr-x 5 ubuntu ubuntu  4096 May 11 19:44 ../
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 11 19:46 apache2.conf
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xaa
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xab
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xac
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xad
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xae
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xaf
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xag
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xah
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xai
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xaj
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xak
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xal
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xam
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xan
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xao
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xap
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xaq
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xar
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xas
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xat
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xau
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xav
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xaw
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xax
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xay
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xaz
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xba
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xbb
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xbc
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xbd
	-rw-rw-r-- 1 ubuntu ubuntu  1000 May 11 19:47 xbe
	-rw-rw-r-- 1 ubuntu ubuntu   745 May 11 19:47 xbf

將apache2分割為小檔, 每行的行數最多為50行, 且分割檔名以數字為區隔:

	ubuntu@ip-172-31-7-139:~/apache_split2$ ls
	apache2.conf
	ubuntu@ip-172-31-7-139:~/apache_split2$ split -l 50 -d apache2.conf
	ubuntu@ip-172-31-7-139:~/apache_split2$ ll
	total 120
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:51 ./
	drwxr-xr-x 6 ubuntu ubuntu  4096 May 11 19:50 ../
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 11 19:50 apache2.conf
	-rw-rw-r-- 1 ubuntu ubuntu  2134 May 11 19:51 x00
	-rw-rw-r-- 1 ubuntu ubuntu  1144 May 11 19:51 x01
	-rw-rw-r-- 1 ubuntu ubuntu  1458 May 11 19:51 x02
	-rw-rw-r-- 1 ubuntu ubuntu  1538 May 11 19:51 x03
	-rw-rw-r-- 1 ubuntu ubuntu  1767 May 11 19:51 x04
	-rw-rw-r-- 1 ubuntu ubuntu  1727 May 11 19:51 x05
	-rw-rw-r-- 1 ubuntu ubuntu  1297 May 11 19:51 x06
	-rw-rw-r-- 1 ubuntu ubuntu  1653 May 11 19:51 x07
	-rw-rw-r-- 1 ubuntu ubuntu  1818 May 11 19:51 x08
	-rw-rw-r-- 1 ubuntu ubuntu  1776 May 11 19:51 x09
	-rw-rw-r-- 1 ubuntu ubuntu  1314 May 11 19:51 x10
	-rw-rw-r-- 1 ubuntu ubuntu  1532 May 11 19:51 x11
	-rw-rw-r-- 1 ubuntu ubuntu  2076 May 11 19:51 x12
	-rw-rw-r-- 1 ubuntu ubuntu  1567 May 11 19:51 x13
	-rw-rw-r-- 1 ubuntu ubuntu  1821 May 11 19:51 x14
	-rw-rw-r-- 1 ubuntu ubuntu  1356 May 11 19:51 x15
	-rw-rw-r-- 1 ubuntu ubuntu  1929 May 11 19:51 x16
	-rw-rw-r-- 1 ubuntu ubuntu  1895 May 11 19:51 x17
	-rw-rw-r-- 1 ubuntu ubuntu  1419 May 11 19:51 x18
	-rw-rw-r-- 1 ubuntu ubuntu   524 May 11 19:51 x19
	
### 檔案管理 ###

透過指令也能管理檔案, 包括顯示檔案的資訊, 檔案的複製、移動、貼上, 以及**檔案間的比較**。

#### du: 顯示目錄或檔案大小 ####

語法: du [參數] [檔案]

- `-a` : --apparent-size, 顯示目錄中的個別檔案大小
- `-b` : --bytes, 以bytes為單位顯示
- `-c` : --total, 顯示個別檔案大小, 以及總和
- `-D` : --dereference-args, 顯示符號連結的來源檔大小
- `-h` : --human-readable, 提供更高的可讀性, 以K(KB)、M(MB)、G(GB)為單位顯示
- `-H` : 與-h類似, 但以1000為k的單位, 而非1024
- `-k` : 以1024bytes為區塊的單位
- `-l` : --count-links, 重複計算連結檔所佔的空間
- `-L` : --dereference, 指定符號檔連結所對應的實際檔案大小
- `-m` : 以MB為單位顯示
- `-s` : --summarize, 只顯示總和
- `-S` : --separate-dirs, 顯示目錄內容, 不包含子目錄大小
- `-x` : ---one-file-system, 若目錄中有不同檔案系統, 不顯示相異的檔案系統
- `--exclude=檔案` : 忽略指定的檔案或目錄
- `--max-depth` : 僅搜尋指定的目錄層級
- --help
- --version

顯示目前目錄使用狀況, 過程如下:

	ubuntu@ip-172-31-7-139:~$ du
	164     ./apache_split
	8       ./.ssh
	4       ./.cache
	116     ./apache_split2
	376     .

顯示 `/var` 目錄下所有檔案的容量, 僅顯示總計, 並以KB為單位:

	ubuntu@ip-172-31-7-139:~$ sudo du -sk /var/*
	460     /var/backups
	149440  /var/cache
	4       /var/crash
	145028  /var/lib
	4       /var/local
	0       /var/lock
	2120    /var/log
	4       /var/mail
	4       /var/opt
	0       /var/run
	32      /var/spool
	4       /var/tmp

#### file: 顯示檔案類型 ####

語法: file [參數] 檔案名稱

- `-b` : --brief, 不顯示檔案名稱
- `-c` : --checking-printout, 列出詳細過程
- `-C` : --compile, 列出magic.ngc的輸出檔, 先分離一些原始檔案內容到此檔案中
- `-f namefile` : --files-from FILE, 指定名稱檔, 讓file指令依照此檔案內容辨識
- `-F 分隔字元` : --separator STRING, 用來作為分隔的字元
- `-h` : --no-dereference, 若為符號連結的檔案, 不顯示原始的檔案類別
- `-i` : --mime, 輸出檔案的字碼總類
- `-k` : --keep-going, 當發現符合的條件時, 不要停止, 繼續執行
- `-L` : --dereference, 若為連結檔, 直接顯示原始檔案的類型
- `-n` : --no-buffer, 直接將結果輸出到螢幕, 不放暫存區
- `-v` : --version
- `-z` : --uncompress, 試著去讀取壓縮檔的內容
- --help

顯示一般檔與連結檔:

	ubuntu@ip-172-31-7-139:~$ file apache2.conf
	apache2.conf: UTF-8 Unicode text
	ubuntu@ip-172-31-7-139:~$ file /etc/resolv.conf
	/etc/resolv.conf: symbolic link to `../run/resolvconf/resolv.conf'
	
顯示執行檔:

	ubuntu@ip-172-31-7-139:~$ file /bin/grep
	/bin/grep: ELF 64-bit LSB  executable, x86-64, version 1 (SYSV), dynamically linked 	(uses shared libs), for GNU/Linux 2.6.24, 	BuildID[sha1]=9780fb82e027800459e20af35f65055688a3c031, stripped
	
顯示裝置檔:

	vagrant@vagrant-ubuntu-trusty-64:~$ file /dev/sda
	/dev/sda: block special
	
#### ls: 列出目錄或是檔案名稱 ####

語法: ls [參數] [檔案]

- `-a` : --all, 列出所有檔案, 包含以「.」為開頭的隱藏檔案
- `-c` : 使用欄位排列顯示檔案與目錄
- `-f` : 不照字母排序, 而照存入磁碟的順序排序
- `-h` : --human-readable, 以較易閱讀的方式呈現檔案大小, 如以M來顯示MBytes
- `-i` : --inode, 列出--inodes(檔案訊息區的內容)的編號
- `-k` : --kibibytes, 檔案大小以KBytes列出
- `-l` : 使用長列表列出
- `-m` : 以水平排列顯示每個指令, 指令間以逗號「.」表示
- `-n` : --numeric-uid-gid, 使用UID與GID來取代使用者名稱跟群組名稱
- `-o` : 以長列表顯示, 但不顯示群組資訊
- `-q` : --hide-control-chars, 不顯示控制碼, 這是預設值
- `-r` : --reverse, 以字母的相反順序列出
- `-R` : --recursive, 遞迴處理, 列出目錄與子目錄下的所有檔案
- `-t` : 依照檔案最後修改時間列出
- `-u` : 依照檔案最後存取時間列出
- `-1` : 一行列出一檔案或目錄
- `-Z` : --context, 查看SELinux的標籤
- `--author` : 列出該檔案或目錄的原作者
- `--color[=條件]` : 色彩與顯示條件定義在dircolors這個指令中, 可參閱dircolors的參數設定
- `--help`
- `--version`

列出/var下的檔案

	root@ip-172-31-7-139:/home/ubuntu# ls /var
	backups  cache  crash  lib  local  lock  log  mail  opt  run  spool  tmp
	
列出目前目錄下的檔案, 並以長列表顯示(加入 `--color=tty` , 會以彩色顯示)

	root@ip-172-31-7-139:/home/ubuntu# ls -l --color=tty
	total 68
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 11 19:43 apache2.conf
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:47 apache_split
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:51 apache_split2
	-rw-rw-r-- 1 ubuntu ubuntu     6 May  6 09:01 donefile
	-rw-rw-r-- 1 ubuntu ubuntu   102 May  6 09:01 errorfile
	-rw-rw-r-- 1 ubuntu ubuntu    12 May  6 08:57 file3
	-rw-rw-r-- 1 ubuntu ubuntu    45 May  6 04:15 file_name
	-rw-rw-r-- 1 ubuntu ubuntu    28 May  9 08:07 readme.txt
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 11 15:48 testfile
	-rw-rw-r-- 1 ubuntu ubuntu   203 May  9 09:45 wfile
	
以字母相反順序表示:

	root@ip-172-31-7-139:/home/ubuntu# ls -r
	wfile     readme.txt  file3      donefile       apache_split
	testfile  file_name   errorfile  apache_split2  apache2.conf
	
列出inodes的數目:

	root@ip-172-31-7-139:/home/ubuntu# ls -i
	395978 apache2.conf   395976 donefile   395973 file_name   395972 wfile
	395979 apache_split   395977 errorfile  395980 readme.txt
	396015 apache_split2  395975 file3      395981 testfile
	
#### stat: 顯示檔案或檔案系統的狀態 ####

語法: stat [參數] 檔案或檔案系統

- `-f` : 顯示檔案系統, 而非單獨的檔案
- `-L` : 顯示連結檔案
- `-t` : 以簡單的模式列出資訊
- `--help`
- `--version`

顯示apache2.conf檔案的狀態

	root@ip-172-31-7-139:/home/ubuntu# stat apache2.conf
	  File: ‘apache2.conf’
	  Size: 31745     	Blocks: 64         IO Block: 4096   regular file
	Device: ca01h/51713d	Inode: 395978      Links: 1
	Access: (0664/-rw-rw-r--)  Uid: ( 1000/  ubuntu)   Gid: ( 1000/  ubuntu)
	Access: 2016-05-12 21:56:07.994812999 +0000
	Modify: 2016-05-11 19:43:52.942812999 +0000
	Change: 2016-05-11 19:43:52.942812999 +0000
	 Birth: -
	 
顯示檔案系統 `/dev/sda1` 的狀態：

	vagrant@vagrant-ubuntu-trusty-64:~$ stat /dev/sda1
	  File: ‘/dev/sda1’
	  Size: 0         	Blocks: 0          IO Block: 4096   block special file
	Device: 5h/5d	Inode: 6392        Links: 1     Device type: 8,1
	Access: (0660/brw-rw----)  Uid: (    0/    root)   Gid: (    6/    disk)
	Access: 2016-05-12 23:16:08.137686354 +0000
	Modify: 2016-05-12 23:15:48.941773059 +0000
	Change: 2016-05-12 23:15:48.941773059 +0000
	 Birth: -
	 
經驗談： `stat` 與 `ls` 這兩個指令可以列出檔案的inode

#### mkdir: 新增目錄 ####

語法: mkdir [參數] 目錄

- `-m 模式` : --mode=MODE, 模式用法與chmod相同, 參閱chmod參數說明
- `-p` : 若上層目錄不存在, 會依序建立; 若該目錄已存在, 將會略過, 不出現錯誤訊息
- `--help`
- `--verbose` : 以完整過程顯示
- `--version`

在目前下新增data的子目錄:

	ubuntu@ip-172-31-7-139:~$ mkdir data
	
新增一個目錄/data/www/test:

	ubuntu@ip-172-31-7-139:~$ mkdir data/www/test
	mkdir: cannot create directory ‘data/www/test’: No such file or directory
	
#### touch: 更改檔案的時間標記 ####

語法: touch [參數] 檔案名稱

- `-a` : 更改檔案被存取的時間
- `-c` : --no-create, 不要新增檔案
- `-m` : 更改檔案被修改的時間
- `-t 時間` : 時間格式為MMDDhhmm
- `--help`
- `--version`

新增一個名為test1的檔案:

	ubuntu@ip-172-31-7-139:~$ touch test1
	
touch主要是更改檔案的時間標記, 但若檔案不存在, 則會新增一個時間標記為現在的檔案

	ubuntu@ip-172-31-7-139:~$ ls -l apache2.conf
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 11 19:43 apache2.conf
	ubuntu@ip-172-31-7-139:~$ touch apache2.conf
	ubuntu@ip-172-31-7-139:~$ ls -l apache2.conf
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 12 23:30 apache2.conf
	
#### cp: 複製檔案或目錄 ####

語法: cp [參數] 檔案名稱或目錄 檔案名稱或目錄/路徑

- `-b` : 覆蓋時會先備份, 備份檔會在字尾加~
- `-f` : --force, 強迫複製
- `-i` : --interactive, 交談模式, 覆蓋檔案前會先詢問使用者
- `-p` : 保留原有檔案的屬性
- `-r` : --recursive, 遞迴處理, 非檔案或目錄則視為一般檔案處理
- `-R` : --recursive, 遞迴處理, 保留特殊檔案的屬性
- `-u` : 僅複製時間差異的檔案

將檔案testfile複製testfile1:

	ubuntu@ip-172-31-7-139:~$ cp testfile testfile1
	ubuntu@ip-172-31-7-139:~$ ls -l testfile
	-rw-rw-r-- 1 ubuntu ubuntu 75 May 11 15:48 testfile
	ubuntu@ip-172-31-7-139:~$ ls -l testfile1
	-rw-rw-r-- 1 ubuntu ubuntu 75 May 13 03:36 testfile1

將/tmp目錄複製到目前的目錄之下:

	ubuntu@ip-172-31-7-139:~$ cp -r /tmp ./
	ubuntu@ip-172-31-7-139:~$ ls -l
	total 80
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 12 23:30 apache2.conf
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:47 apache_split
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:51 apache_split2
	drwxrwxr-x 3 ubuntu ubuntu  4096 May 12 23:24 data
	-rw-rw-r-- 1 ubuntu ubuntu     6 May  6 09:01 donefile
	-rw-rw-r-- 1 ubuntu ubuntu   102 May  6 09:01 errorfile
	-rw-rw-r-- 1 ubuntu ubuntu    12 May  6 08:57 file3
	-rw-rw-r-- 1 ubuntu ubuntu    45 May  6 04:15 file_name
	-rw-rw-r-- 1 ubuntu ubuntu    28 May  9 08:07 readme.txt
	-rw-rw-r-- 1 ubuntu ubuntu     0 May 12 23:28 test1
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 11 15:48 testfile
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 13 03:36 testfile1
	drwxrwxr-t 2 ubuntu ubuntu  4096 May 13 03:38 tmp
	-rw-rw-r-- 1 ubuntu ubuntu   203 May  9 09:45 wfile

	ubuntu@ip-172-31-7-139:~$ cp -p /usr/bin/passwd ./
	ubuntu@ip-172-31-7-139:~$ ls -l
	total 128
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 12 23:30 apache2.conf
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:47 apache_split
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:51 apache_split2
	drwxrwxr-x 3 ubuntu ubuntu  4096 May 12 23:24 data
	-rw-rw-r-- 1 ubuntu ubuntu     6 May  6 09:01 donefile
	-rw-rw-r-- 1 ubuntu ubuntu   102 May  6 09:01 errorfile
	-rw-rw-r-- 1 ubuntu ubuntu    12 May  6 08:57 file3
	-rw-rw-r-- 1 ubuntu ubuntu    45 May  6 04:15 file_name
	-rwxr-xr-x 1 ubuntu ubuntu 47032 Jul 15  2015 passwd
	-rw-rw-r-- 1 ubuntu ubuntu    28 May  9 08:07 readme.txt
	-rw-rw-r-- 1 ubuntu ubuntu     0 May 12 23:28 test1
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 11 15:48 testfile
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 13 03:36 testfile1
	drwxrwxr-t 2 ubuntu ubuntu  4096 May 13 03:38 tmp
	-rw-rw-r-- 1 ubuntu ubuntu   203 May  9 09:45 wfile

#### 移動或更名檔案或目錄 ####

語法: mv [參數] 來源檔或目錄 目的檔或目錄

- `-b` : 若目的檔存在, 在覆蓋前會將舊有檔案備份(檔名後加~)
- `-f` : --force, 強迫執行, 若目的檔存在, 則會強迫覆蓋
- `-i` : --interactive, 交談模式, 覆蓋舊檔會先詢問使用者
- `-u` : --update, 移動或變更檔名時,若目的檔已存在且檔案更新時間比來源新, 則不執行
- `-v` : 列出詳細過程
- `--help`
- `--version`

將 file1 更名 file2 :

	ubuntu@ip-172-31-7-139:~$ echo test > file1
	ubuntu@ip-172-31-7-139:~$ ls
	apache2.conf   data       file1      passwd      testfile   wfile
	apache_split   donefile   file3      readme.txt  testfile1
	apache_split2  errorfile  file_name  test1       tmp
	ubuntu@ip-172-31-7-139:~$ mv file1 file2
	ubuntu@ip-172-31-7-139:~$ ls -l
	total 132
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 12 23:30 apache2.conf
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:47 apache_split
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:51 apache_split2
	drwxrwxr-x 3 ubuntu ubuntu  4096 May 12 23:24 data
	-rw-rw-r-- 1 ubuntu ubuntu     6 May  6 09:01 donefile
	-rw-rw-r-- 1 ubuntu ubuntu   102 May  6 09:01 errorfile
	-rw-rw-r-- 1 ubuntu ubuntu     5 May 13 03:49 file2
	-rw-rw-r-- 1 ubuntu ubuntu    12 May  6 08:57 file3
	-rw-rw-r-- 1 ubuntu ubuntu    45 May  6 04:15 file_name
	-rwxr-xr-x 1 ubuntu ubuntu 47032 Jul 15  2015 passwd
	-rw-rw-r-- 1 ubuntu ubuntu    28 May  9 08:07 readme.txt
	-rw-rw-r-- 1 ubuntu ubuntu     0 May 12 23:28 test1
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 11 15:48 testfile
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 13 03:36 testfile1
	drwxrwxr-t 2 ubuntu ubuntu  4096 May 13 03:38 tmp
	-rw-rw-r-- 1 ubuntu ubuntu   203 May  9 09:45 wfile

將檔案移動到/tmp下:

	ubuntu@ip-172-31-7-139:~$ mv file2 file1
	ubuntu@ip-172-31-7-139:~$ mv file1 /tmp/
	ubuntu@ip-172-31-7-139:~$ ls -l /tmp/
	total 4
	-rw-rw-r-- 1 ubuntu ubuntu 5 May 13 03:49 file1

將目錄/home/dir1更名為/home/dir2:

	ubuntu@ip-172-31-7-139:~$ mkdir dir1
	ubuntu@ip-172-31-7-139:~$ mv dir1 dir2
	ubuntu@ip-172-31-7-139:~$ ls -l
	total 132
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 12 23:30 apache2.conf
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:47 apache_split
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:51 apache_split2
	drwxrwxr-x 3 ubuntu ubuntu  4096 May 12 23:24 data
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 13 03:53 dir2
	-rw-rw-r-- 1 ubuntu ubuntu     6 May  6 09:01 donefile
	-rw-rw-r-- 1 ubuntu ubuntu   102 May  6 09:01 errorfile
	-rw-rw-r-- 1 ubuntu ubuntu    12 May  6 08:57 file3
	-rw-rw-r-- 1 ubuntu ubuntu    45 May  6 04:15 file_name
	-rwxr-xr-x 1 ubuntu ubuntu 47032 Jul 15  2015 passwd
	-rw-rw-r-- 1 ubuntu ubuntu    28 May  9 08:07 readme.txt
	-rw-rw-r-- 1 ubuntu ubuntu     0 May 12 23:28 test1
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 11 15:48 testfile
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 13 03:36 testfile1
	drwxrwxr-t 2 ubuntu ubuntu  4096 May 13 03:38 tmp
	-rw-rw-r-- 1 ubuntu ubuntu   203 May  9 09:45 wfile

#### rm: 刪除檔案或目錄 ####

語法: rm [參數] 檔案或目錄

- `-d` : --dir, 直接刪除目錄, 而不刪除目錄下的檔案, 這會造成其下檔案失去連結, 並可能須透過fsck方能修復, 一般不建議使用
- `-f` : --force, 強制刪除檔案或目錄
- `-i` : 刪除檔案前, 會先詢問使用者
- `-r` : 將該檔案或目錄底下的所有檔案與子目錄一併處理
- `-v` : 顯示完整的執行畫面
- `--help`
- `--version`

以交談模式刪除檔案:

	ubuntu@ip-172-31-7-139:~$ echo abc1 > abc1
	ubuntu@ip-172-31-7-139:~$ echo abc2 > abc2
	ubuntu@ip-172-31-7-139:~$ ls -l
	total 140
	-rw-rw-r-- 1 ubuntu ubuntu     5 May 13 04:00 abc1
	-rw-rw-r-- 1 ubuntu ubuntu     5 May 13 04:00 abc2
	-rw-rw-r-- 1 ubuntu ubuntu 31745 May 12 23:30 apache2.conf
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:47 apache_split
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 11 19:51 apache_split2
	drwxrwxr-x 3 ubuntu ubuntu  4096 May 12 23:24 data
	drwxrwxr-x 2 ubuntu ubuntu  4096 May 13 03:53 dir2
	-rw-rw-r-- 1 ubuntu ubuntu     6 May  6 09:01 donefile
	-rw-rw-r-- 1 ubuntu ubuntu   102 May  6 09:01 errorfile
	-rw-rw-r-- 1 ubuntu ubuntu    12 May  6 08:57 file3
	-rw-rw-r-- 1 ubuntu ubuntu    45 May  6 04:15 file_name
	-rwxr-xr-x 1 ubuntu ubuntu 47032 Jul 15  2015 passwd
	-rw-rw-r-- 1 ubuntu ubuntu    28 May  9 08:07 readme.txt
	-rw-rw-r-- 1 ubuntu ubuntu     0 May 12 23:28 test1
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 11 15:48 testfile
	-rw-rw-r-- 1 ubuntu ubuntu    75 May 13 03:36 testfile1
	drwxrwxr-t 2 ubuntu ubuntu  4096 May 13 03:38 tmp
	-rw-rw-r-- 1 ubuntu ubuntu   203 May  9 09:45 wfile
	ubuntu@ip-172-31-7-139:~$ rm -i abc*
	rm: remove regular file ‘abc1’? ^C
	rm: remove regular file ‘abc1’? y
	rm: remove regular file ‘abc2’? y

強制刪除test目錄與子目錄下檔案與目錄:

	ubuntu@ip-172-31-7-139:~$ mkdir test
	ubuntu@ip-172-31-7-139:~$ echo test1 > test/test1
	ubuntu@ip-172-31-7-139:~$ ls -R test
	test:
	test1
	ubuntu@ip-172-31-7-139:~$ rm -rf test

#### diff: 比較並顯示檔案差異的部分 ####

語法: diff [參數] 檔案1 檔案2

- `-a` : --text, 強迫比對二進位的檔案
- `-b` : --ignore-space-change, 不比較空白字元
- `-B` : --ignore-blank-lines, 不比較空白行數
- `-c` : --C NUM, --context[=NUM], 顯示相異處的前後內文, 並標示不同之處
- `-d` : --minimal, 使用不同的演算法
- `-H` : 可加速大檔案的比較速度
- `-I` : --ignore-matching-lines=RE, 忽略指定字串的差異
- `-i` : --ignore-case, 不檢查大小寫的不同
- `-l` : --paginate, 將結果使用pr來分頁
- `-n` : --rcs, 將結果以RCS的格式來表示
- `-p` : --show-c-function, 若比較的檔案為C語言, 列出差異所在的函數
- `-q` : --brief, 僅顯示是否有差異, 不顯示其他訊息
- `-r` : --recursive, 遞迴處理, 比較子目錄下的所有檔案
- `-s` : --report-identical-files, 即使沒差異, 也顯示訊息
- `-S 檔案` : --starting-file=FILE, 若是比較目錄, 從指定的檔案開始比較
- `-t` : --expand-tabs, 將結果輸出時, 將tab字元顯示出。預設以空白取代
- `-T` : --initial-tab, 在每行前加上tab
- `-v` : 顯示版本資訊
- `-w` : --ignore-all-space, 忽略所有空白字元
- `-W 寬度` : --width=NUM, 若使用-y參數, 指定欄寬
- `-x 檔案或目錄` : --exclude=PAT, 不比對特定字元
- `-X 檔案` : --exclude-from=FILE, 將特定檔案或目錄存成文字檔, 在執行diff時不比對這些檔案
- `-y` : --side-by-side, 以並列方式顯示比對結果
- `--help`

假設file1、file2、file3檔案內容:

    ubuntu@ip-172-31-7-139:~$ cat file1
    This is line 1.....
    This is line 2.....
    This is line 3.....
    This is line 4.....
    This is line 5.....
    ubuntu@ip-172-31-7-139:~$ cat file2
    This is line 1.....
    This is line 2.....
    it's ok.....
    there is a car.....
    This is line 5.....
    ubuntu@ip-172-31-7-139:~$ cat file3
    This is line 1.....
    This is line 2.....
    This is line 3.....
    This is line 4.....

    This is line 5.....

比對file1與file2:

	ubuntu@ip-172-31-7-139:~$ diff file1 file2
	3,4c3,4
	< This is line 3.....
	< This is line 4.....
	---
	> it's ok.....
	> there is a car.....

比對file1與file2, 並列出前後一行的異同:

	ubuntu@ip-172-31-7-139:~$ diff -c file1 file2
	*** file1       2016-05-13 07:21:11.146812999 +0000
	--- file2       2016-05-13 07:22:11.710812999 +0000
	***************
	*** 1,5 ****
	  This is line 1.....
	  This is line 2.....
	! This is line 3.....
	! This is line 4.....
	  This is line 5.....
	--- 1,5 ----
	  This is line 1.....
	  This is line 2.....
	! it's ok.....
	! there is a car.....
	  This is line 5.....

比對file1與file2, 以並列顯示

	ubuntu@ip-172-31-7-139:~$ diff -y file1 file2
	This is line 1.....                                             This is line 1.....
	This is line 2.....                                             This is line 2.....
	This is line 3.....                                           | it's ok.....
	This is line 4.....                                           | there is a car.....
	This is line 5.....                                             This is line 5.....

#### cat: 列出檔案內容 ####

語法: cat [參數] 檔案名稱

- `-A` : --show-all, 顯示全部, 效果等同於「-vET」參數
- `-b` : --number-nonblank, 在所有非空白列上標示符號
- `-e` : 等同於「-vE」
- `-E` : --show-end, 在每一列的最後標上「$」符號
- `-n` : --number, 在所有的列前標示符號。效果與-b相同, 但會包含空白列
- `-s` : --squeeze-blank, 當連續出現兩列以上空白列, 會僅標示一列空白
- `-t` : 等同於「-vT」
- `-T` : --show-tabs, 將tab鍵(跳格字元)以「^|」來顯示
- `-v` : --show-nonprinting, 除LFD與tab字元以外, 其他控制字元均以「^」表示
- `--help`
- `--version`

顯示/etc/hosts.allow內容:

	ubuntu@ip-172-31-15-54:~$ cat /etc/hosts.allow
	# /etc/hosts.allow: list of hosts that are allowed to access the system.
	#                   See the manual pages hosts_access(5) and hosts_options(5).
	#
	# Example:    ALL: LOCAL @some_netgroup
	#             ALL: .foobar.edu EXCEPT terminalserver.foobar.edu
	#
	# If you're going to protect the portmapper use the name "rpcbind" for the
	# daemon name. See rpcbind(8) and rpc.mountd(8) for further information.
	#

顯示/etc/hosts.allow內容, 並在非空白列上標示符號:

	ubuntu@ip-172-31-15-54:~$ cat -b /etc/hosts.allow
	     1  # /etc/hosts.allow: list of hosts that are allowed to access the system.
	     2  #                   See the manual pages hosts_access(5) and hosts_options(5).
	     3  #
	     4  # Example:    ALL: LOCAL @some_netgroup
	     5  #             ALL: .foobar.edu EXCEPT terminalserver.foobar.edu
	     6  #
	     7  # If you're going to protect the portmapper use the name "rpcbind" for the
	     8  # daemon name. See rpcbind(8) and rpc.mountd(8) for further information.
	     9  #

#### tac: 將檔案內容由尾到頭的顯示 ####

語法: tac [參數] [檔案名稱]

- `-b` : --before, 將間隔的字元放在檔案紀錄前面
- `-r` : --regex, 間隔字元當作一般文字表達
- `-s` : --separator, 用指定的間隔字元取代新增控制字元
- `--help`
- `--version`

將檔案由尾到頭相反顯示：

	ubuntu@ip-172-31-15-54:~$ cat aaa
	line1
	line2
	line3
	line4
	line5
	ubuntu@ip-172-31-15-54:~$ tac aaa
	line5
	line4
	line3
	line2
	line1
	
#### head: 輸出檔案內容最前面的部分 ####

語法: head [參數] 檔案名稱

- `-c 顯示數量` : --bytes=[-]K, 設定顯示數量以byte為單位
- `-n 顯示列數` : --lines=[-]K, 設定顯示列數
- `-q` : --quiet, 不顯示檔案名稱
- `-v` : --verbose, 顯示檔案名稱
- `--help`
- `--version`

顯示/etc/hosts.allow的前三行:

	ubuntu@ip-172-31-15-54:~$ head -n 3 /etc/hosts.allow
	# /etc/hosts.allow: list of hosts that are allowed to access the system.
	#                   See the manual pages hosts_access(5) and hosts_options(5).
	#
	
顯示/etc/hosts.allow的前25個bytes:

	ubuntu@ip-172-31-15-54:~$ head -c 25 /etc/hosts.allow
	# /etc/hosts.allow: list ubuntu@ip-172-31-15-54:~$
	
#### less: 顯示檔案內容 ####

語法: less [參數] [檔案名稱]

- `-e` : --quit-at-eof, 檔案顯示結束後, 自動離開, 不須鍵入q
- `-f` : --force, 強迫開啟非一般檔
- `-g` : --hilite-search, 不特別標示使用搜尋指令的關鍵字
- `-i` : --ignore-case, 搜尋時忽略大小寫
- `-N` : --line-numbers, 在每一行開頭顯示行號
- `-Q` : --quiet, 關閉警告音
- `-s` : --squeeze-blank-lines, 將連續的空行以一行表示
- `-S` : --chop-long-lines, 過長的行不換行顯示
- `-x n` : --tabs=[N[,...]], 將Tab字元取代為n個空白字元

執行less當中的指令說明:

- `b` : 往後捲動一頁
- `d` : 往後捲動半頁
- `h, H, Help` : 顯示說明畫面
- `q` : 離開
- `R` : 重新顯示畫面, 不讀取緩衝區的資料
- `u` : 往前捲動半頁
- `y` : 往前捲動一行
- `Enter鍵` : 捲動一行
- `空白鍵` : 捲動一頁
- `/字串` : 尋找特定字串

使用less查看apache2.conf這個檔案

	ubuntu@ip-172-31-15-54:~$ less apache2.conf
	
一般我們用Space與上下鍵翻閱內容, 並可隨時按 `q` 離開

#### more: 顯示檔案內容 ####

語法: more [參數] [檔案名稱]

- `-c` : 每次顯示全新的一頁。預設會顯示上一頁的最後一行
- `-d` : more會在最後一行顯示說明; 按space鍵繼續、按q離開等等
- `-f` : 只處理正常狀況的行, 太長的行列將不處理
- `+num` : 從第num行開始顯示
- `-num` : 顯示每一頁的行數(num)
- `-p` : 不捲動, 而以整頁表示內容
- `-s` : 若有多行空白, 將以一行顯示

瀏覽apache2.conf, 每次顯示10行:

	ubuntu@ip-172-31-15-54:~$ more -10 apache2.conf
	SA 服務的設定檔。
	#
	#這是Apache伺服器主要設定檔。
	#它包含伺服器的影響伺服器運行的設定指令。
	#參見<URL:http://httpd.ache.org/doc-2.0/>以取得關於這些指令的詳細資訊
	#
	#不要只是簡單的閱讀這些指令資訊而不去理解它。
	#這裡只是做了簡單的說明，如果你沒有參考線上檔，你就會被警告。
	#
	#這些設定指令被分為下面三個部分：
	--More--(1%)
	
#### nl: 加上檔案中的行列編號 ####

語法: nl [參數] [檔案名稱]

- `-i` : --line-increment=NUMBER, 每行的增加數值
- `-s` : --number-separator=STRING, 在編號後面加上的符號
- `-v` : --starting-line-number=NUMBER, 設定起始的數字
- `-w` : --number-width=NUMBER, 設定數字顯示的位置(第幾列)
- `--help`
- `--version`

使用不同的編號位置(第2個):

	ubuntu@ip-172-31-15-54:~$ cat /etc/hosts
	127.0.0.1 localhost

	# The following lines are desirable for IPv6 capable hosts
	::1 ip6-localhost ip6-loopback
	fe00::0 ip6-localnet
	ff00::0 ip6-mcastprefix
	ff02::1 ip6-allnodes
	ff02::2 ip6-allrouters
	ff02::3 ip6-allhosts
	ubuntu@ip-172-31-15-54:~$ nl /etc/hosts -w 2
	 1	127.0.0.1 localhost

	 2	# The following lines are desirable for IPv6 capable hosts
	 3	::1 ip6-localhost ip6-loopback
	 4	fe00::0 ip6-localnet
	 5	ff00::0 ip6-mcastprefix
	 6	ff02::1 ip6-allnodes
	 7	ff02::2 ip6-allrouters
	 8	ff02::3 ip6-allhosts

使用不同的編號位置(第5個):

	ubuntu@ip-172-31-15-54:~$ nl /etc/hosts -w 5
	    1	127.0.0.1 localhost

	    2	# The following lines are desirable for IPv6 capable hosts
	    3	::1 ip6-localhost ip6-loopback
	    4	fe00::0 ip6-localnet
	    5	ff00::0 ip6-mcastprefix
	    6	ff02::1 ip6-allnodes
	    7	ff02::2 ip6-allrouters
	    8	ff02::3 ip6-allhosts

編號每次增加3, 並在編號後面加符號「=」:

	ubuntu@ip-172-31-15-54:~$ nl /etc/hosts -i 3 -s=
	     1=127.0.0.1 localhost

	     4=# The following lines are desirable for IPv6 capable hosts
	     7=::1 ip6-localhost ip6-loopback
	    10=fe00::0 ip6-localnet
	    13=ff00::0 ip6-mcastprefix
	    16=ff02::1 ip6-allnodes
	    19=ff02::2 ip6-allrouters
	    22=ff02::3 ip6-allhosts

#### tail: 顯示檔案最後面的部分 ####

語法: tail [參數] [檔案名稱]

- `-c N` : --bytes=K, 顯示最後Nbytes的資料
- `-f` : --follow[={name|descriptor}], 持續讀取檔案末尾的資料, 通常用於紀錄檔的監控
- `-n 列數` : --lines=K, 顯示指定列數的內容
- `--pid=PID` : 必須跟著-f使用, 當指定的程序號碼PID結束時, 則停止tail的指令
- `-q` : --quiet, 不顯示檔案名稱
- `-s 秒數` : --sleep-interval=N, 必須跟著-f的使用, 每次更新的中間, 休息指定的秒數
- `-v` : --verbose, 顯示檔案的名稱
- `--help`
- `--version`

顯示/var/log/syslog的最後10行:

	ubuntu@ip-172-31-15-54:~$ tail -n 10 /var/log/syslog
	May 14 14:33:14 ip-172-31-15-54 dhclient: DHCPREQUEST of 172.31.15.54 on eth0 to 172.31.0.1 port 67 (xid=0x6ec1748b)
	May 14 14:33:14 ip-172-31-15-54 dhclient: DHCPACK of 172.31.15.54 from 172.31.0.1
	May 14 14:33:15 ip-172-31-15-54 dhclient: bound to 172.31.15.54 -- renewal in 1687 seconds.
	May 14 15:01:22 ip-172-31-15-54 dhclient: DHCPREQUEST of 172.31.15.54 on eth0 to 172.31.0.1 port 67 (xid=0x6ec1748b)
	May 14 15:01:22 ip-172-31-15-54 dhclient: DHCPACK of 172.31.15.54 from 172.31.0.1
	May 14 15:01:22 ip-172-31-15-54 dhclient: bound to 172.31.15.54 -- renewal in 1587 seconds.
	May 14 15:17:01 ip-172-31-15-54 CRON[3807]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
	May 14 15:27:49 ip-172-31-15-54 dhclient: DHCPREQUEST of 172.31.15.54 on eth0 to 172.31.0.1 port 67 (xid=0x6ec1748b)
	May 14 15:27:49 ip-172-31-15-54 dhclient: DHCPACK of 172.31.15.54 from 172.31.0.1
	May 14 15:27:49 ip-172-31-15-54 dhclient: bound to 172.31.15.54 -- renewal in 1452 seconds.

持續的監控/var/log/syslog這個檔案:

	ubuntu@ip-172-31-15-54:~$ tail -f /var/log/syslog
	May 14 14:33:14 ip-172-31-15-54 dhclient: DHCPREQUEST of 172.31.15.54 on eth0 to 172.31.0.1 port 67 (xid=0x6ec1748b)
	May 14 14:33:14 ip-172-31-15-54 dhclient: DHCPACK of 172.31.15.54 from 172.31.0.1
	May 14 14:33:15 ip-172-31-15-54 dhclient: bound to 172.31.15.54 -- renewal in 1687 seconds.
	May 14 15:01:22 ip-172-31-15-54 dhclient: DHCPREQUEST of 172.31.15.54 on eth0 to 172.31.0.1 port 67 (xid=0x6ec1748b)
	May 14 15:01:22 ip-172-31-15-54 dhclient: DHCPACK of 172.31.15.54 from 172.31.0.1
	May 14 15:01:22 ip-172-31-15-54 dhclient: bound to 172.31.15.54 -- renewal in 1587 seconds.
	May 14 15:17:01 ip-172-31-15-54 CRON[3807]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
	May 14 15:27:49 ip-172-31-15-54 dhclient: DHCPREQUEST of 172.31.15.54 on eth0 to 172.31.0.1 port 67 (xid=0x6ec1748b)
	May 14 15:27:49 ip-172-31-15-54 dhclient: DHCPACK of 172.31.15.54 from 172.31.0.1
	May 14 15:27:49 ip-172-31-15-54 dhclient: bound to 172.31.15.54 -- renewal in 1452 seconds.

執行後, 該檔案只要有新的內容, 就會在螢幕輸出, 若要停止, 要必須按`Ctrl+C`

#### tee: 同時輸出到畫面與檔案中 ####

語法: tee [參數] [檔案名稱]

- -a : --append, 將輸入附加在檔案的後頭
- -i : --ignore-interrupts, 忽略中斷的信號
- --help
- --version

使用tee新增一個檔案aaa:

	ubuntu@ip-172-31-15-54:~$ tee aaa
	this is line 1
	this is line 1
	this is line 2
	this is line 2
	^C
	ubuntu@ip-172-31-15-54:~$ cat aaa
	this is line 1
	this is line 2

同時輸出一列文字到檔案與畫面上:

	ubuntu@ip-172-31-15-54:~$ echo "This is a test line" | tee test1.txt
	This is a test line

### 正則化字串搜尋 ###

正則表達式是一個**字元和字串的範本**, 透過多種操作符號來組合與建構類似算術的運算式。透過正規表達式的處理, 可以用簡單的運算式來呈現一組字元或字串, 對於指令的使用或是Shell Script都是方便的工具。

Ex:

1. [a-p]表達字母a到p之間的所有字元
2. 「^ns」表達nslookup、nstat或nsupdate等(開頭為ns)

透過正規表達式, 可減少程式內容的撰寫, 並可方便我們使用指令尋找關鍵字串。

下面列出正規表達式中常用的特殊字元:

- `\` : 跳脫字元
- `.` : 符合任何單個字元, 例如[a.b]符合acb, a1b等
- `*` : 之前的字元可出現零次或多次, 例如「0*1234」符合1234, 01234, 0001234等
- `^` : 字串開始的位置
- `$` : 字串結束的位置
- `\b` : 符合字串邊界的位置, 例如「ee\b」符合bee, 但不符feed
- `\B` : 符合字串, 但不得為邊界, 例如「ee\b」可符合feed, 但不符bee
- `[xyz]` : 符合包含的字元x或y或z
- `[^xyz]` : 符合未包含x或y或z的字元
- `[a-z]` : 符合字元的範圍
- `[^a-z]` : 不符合字元的範圍

注意: 並非所有指令都支持正規表達式, grep是支援正規表達式的標準指令

下面三個範例介紹正規表達式的使用方式和時機:

1. Ex1: 顯示使用字串「root」開頭的行

	使用grep這指令可尋找檔案中的字串, 如果不加正規表達式, 直接使用root作為字串搜尋, 會找出內文中所有包含root的行, 但若加上`^root`, 則僅會顯示root開頭的行:

		ubuntu@ip-172-31-15-54:~$ grep ^root /etc/passwd
		root:x:0:0:root:/root:/bin/bash

2. Ex2: 搜尋字尾符號為「:」的行

		ubuntu@ip-172-31-15-54:~$ grep :$ /etc/group
		root:x:0:
		daemon:x:1:
		bin:x:2:
		...

3. Ex3: 得到一個以「c」開頭且已[h]結尾的所有5位元的英語單詞

		ubuntu@ip-172-31-15-54:~$ grep '\<c...h\>' /usr/share/dict/words
		catch
		clash
		...

## 文書編輯工具 ##

### vi編輯器 ###

vi是Linux以及UNIX上最普遍被使用的編輯器, 目前Linux上面是使用進化過的vim, 它支援更便利的插入模式。

對於一個系統管理者而言, vi是必備的工具之一, 除了普遍性(幾乎所有情形下都可以使用vi)之外, 部分系統設定也必須使用, 如排程設定(crontab -e)、配額設定(edquota -u user)等。

輸入vi, 輸入:q離開此畫面:

	~                                   VIM - Vi IMproved
	~
	~                                    version 7.4.52
	~                               by Bram Moolenaar et al.
	~                Modified by pkg-vim-maintainers@lists.alioth.debian.org
	~                      Vim is open source and freely distributable
	~
	~                               Sponsor Vim development!
	~                    type  :help sponsor<Enter>    for information
	~
	~                    type  :q<Enter>               to exit
	~                    type  :help<Enter>  or  <F1>  for on-line help
	~                    type  :help version7<Enter>   for version info

vi環境共有三種模式, 第一為命令模式, 第二為插入模式, 第三為底線模式, 分述如下:

#### 命令模式 ####

這是一開始進入vi的模式, 在任何模式下, 只要按Esc鍵就會回到這模式(最下方出現檔案名稱或是空白就是命令模式):

	...
	~
	~
	~
	~
	~
	~
	"test1" [New File]                                                     0,0-1         All

#### 輸入模式 ####

按一些特殊命令就可以進入這模式, 如「i」、「I」、「o」、「O」等:

	...
	~
	~
	~
	~
	~
	~
	-- INSERT --                                                           0,1           All

按 `Esc` 鍵即可回到命令模式

在輸入模式下如同一般編輯器, 可插入也可按 `Back space` 刪除, 亦可用上下左右鍵移動游標

#### 底線模式 ####

在命令模式按 `:` 即可進入底線模式。在底線模式按 `Esc` 或 `Ctrl+C` 即可回到命令模式

在底線模式下可在冒號 `:` 輸入指令:

- `wq`
- `w`
- `w!` : 強迫存擋, 如果要寫入唯讀檔, 必須是檔案的擁有者
- `wname` : 另存擋名為 name 的新檔
- `q`
- `q!`
- `e name` : 編輯名稱為 name 的檔案
- `e!` : 再編輯, 捨棄原先的變更
- `n` : 編輯下一個檔案。若使用「`vi file1 file2`」, 在編輯完file1後使用此指令, 即可編輯file2
- `f` : 顯示目前的檔名, 游標所在的行數以及比例
- `!` : 執行外部指令

新增檔案first與內容:

	ubuntu@ip-172-31-15-54:~$ vi first

經驗談: 如果碰到沒有寫入權限但為檔案的擁有者, 如root是/etc/shadow的擁有者但沒寫入權限, 我們就必須加上驚嘆號強制寫入

經驗談: 若輸入「vi abc1 abc2 abc3」, 則會先編輯abc1這個檔案, 編輯完畢才會處理abc2, 處理完畢才會處理abc3

在vi中有命令模式、編輯模式、以及底線模式。這三種模式有以下的關聯:

1. 剛進入vi是命令模式
2. 要離開vi必須進入命令模式或是底線模式。**在命令模式可按ZZ存檔後離開**, 在底線模式按q、q!不存檔離開或wq、wq!存檔後離開
3. 在命令模式可按i、I、o、O等進入編輯模式, 也可按:進入底線模式
4. **在編輯模式下只能進入命令模式**, **在底線模式下也只能進入命令模式**
5. 一般編輯順序: 開始 -> 命令模式 -> 編輯模式 -> 命令模式 -> 底線模式 -> 離開

輸入「vi -o3 first test1 test2」則會將視窗分割為三, 由上到下分別為first test1 test2, 若要切換這三個視窗, 可在命令模式下按 `Ctrl+W` 再按上下鍵切換:

	this is a test file

	這是測試檔

	This file is designed for students.......
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	first [+]                                                              3,13-9         All

	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	test1 [+]                                                              1,0-1          All

	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	~
	test2                                                                  0,0-1          All

更多操作說明:

1. 游標的移動以及換頁 - 【命令模式下】

- `Ctrl + D` : 往下捲動半頁
- `Ctrl + U` : 往上捲動半頁
- `Ctrl + F` : 往下捲動一頁
- `Ctrl + B` : 往上捲動一頁
- `:0` : 移動到檔案的起點
- `:number` : 移動到number行
- `:$` : 移動到檔案的最後一行

2. 刪除字元與行 - 【命令模式下】

- `x` : 刪除游標所在的字元
- `X` : 刪除游標所在的前一個字元
- `D` : 刪除從游標到這一行結束的字元
- `d^` : 刪除從該行開始到游標所在的字元
- `dd` : 刪除本行
- `ndd` : n為數字, 刪除游標所在算起的n行
- `dnw` : 刪除游標所在往右算起的n個單字

3. 新增, 插入 - 【命令模式下】

- `i` : 變成輸入模式, 並由游標所在之處開始插入
- `I` : 變成輸入模式, 並由游標所在之後的非空字元開始插入
- `a` : 變成輸入模式, 並由游標所在之後開始插入
- `A` : 變成輸入模式, 並由這一行的最後開始插入
- `o` : 在游標所在的下方新增一行, 並變成輸入模式
- `O` : 在游標所在的上方新增一行, 並變成輸入模式

4. 複製與貼上 - 【命令模式下】

- `Y` : 複製游標所在的那一行並放至暫存區
- `yy` : 複製游標所在的那一行並放至暫存區
- `nyy` : 複製游標往下算的n行並放至暫存區
- `p` : 複製暫存區的內容到游標所在的下方
- `P` : 複製暫存區的內容到游標所在的上方

5. 尋找與取代 - 【底線模式下】

- `/text` : 往前尋找text這個單字
- `?text` : 往後尋找text這個單字

- [addr]s/from/to/[g] : 取代[addr]這個區域的文字, 將from取代成to.

	[addr]當中包含兩個數字a、b, 以逗點隔開, 表示從a行到b行, 而1,$表示檔案的全部。'g'表示全面的取代, 單一檔案可以不加。

	Ex: 「:2,6s/iptables/new/g」會將2到6行當中的iptables改為new

6. 回復

- u : 回復之前的改變
- U : 回復游標所在這行的改變

### pico ###

pico這文書編輯器, 用法比較像早期的PE2或PE3:

編輯sysctl.conf, pico sysctl.conf:

	  GNU nano 2.2.6               File: /etc/sysctl.conf

	#
	# /etc/sysctl.conf - Configuration file for setting system variables
	# See /etc/sysctl.d/ for additional system variables.
	# See sysctl.conf (5) for information.
	#

	#kernel.domainname = example.com

	# Uncomment the following to stop low-level messages on console
	#kernel.printk = 3 4 1 3

	##############################################################3
	# Functions previously found in netbase
	#

	# Uncomment the next two lines to enable Spoof protection (reverse-path filter)
	# Turn on Source Address Verification in all interfaces to
	# prevent some spoofing attacks
	#net.ipv4.conf.default.rp_filter=1
	#net.ipv4.conf.all.rp_filter=1

	# Uncomment the next line to enable TCP/IP SYN cookies
	# See http://lwn.net/Articles/277146/
	# Note: This may impact IPv6 TCP sessions too
	#net.ipv4.tcp_syncookies=1

	# Uncomment the next line to enable packet forwarding for IPv4
	#net.ipv4.ip_forward=1

	# Uncomment the next line to enable packet forwarding for IPv6
	#  Enabling this option disables Stateless Address Autoconfiguration
	#  based on Router Advertisements for this host
	#net.ipv6.conf.all.forwarding=1


	###################################################################
	# Additional settings - these settings can improve the network
	# security of the host and prevent against some network attacks
	# including spoofing attacks and man in the middle attacks through
	# redirection. Some network environments, however, require that these
	# settings are disabled so review and enable them as needed.
	#
	# Do not accept ICMP redirects (prevent MITM attacks)
	#net.ipv4.conf.all.accept_redirects = 0
	#net.ipv6.conf.all.accept_redirects = 0
	# _or_
	                    [ Read 60 lines (Warning: No write permission) ]
	^G Get Help   ^O WriteOut   ^R Read File  ^Y Prev Page  ^K Cut Text   ^C Cur Pos
	^X Exit       ^J Justify    ^W Where Is   ^V Next Page  ^U UnCut Text ^T To Spell

直接使用游標即可移動, 按`Back space`鍵可刪除字元, 並可直接插入字元, 按 `Ctrl+X` 組合鍵即可儲存並離開。

最下面兩排即為編輯指令的說明, 下面將說明常用的編輯指令: 下面的「^」代表Ctrl按鍵

- `^G` : 顯示help畫面
- `^X` : 關閉正開啟的檔案
- `^O` : 將目前檔案存擋
- `^J` : 檢查正確的段落, 需外掛支援
- `^R` : 將另外檔案插入目前檔案中
- `^W` : 搜尋特定文字
- `^Y` : 移動到前一個畫面
- `^V` : 移動到下一個畫面
- `^K` : 將游標所在的該行刪除, 存到暫存區
- `^U` : 將刪除的該行復原
- `^C` : 顯示游標所在的位置
- `^T` : 如果外掛程式支援, 使用拼字檢查

### nano ###

nano用法類似pico, 可讓我們在遠端登入時編輯檔案, 下面介紹使用方式

#### 進入nano ####

	nano -參數 <filename>

常見參數如下:

- `-B`, `--backup` : 加此參數會有備份功能, 原來檔案將被存為開頭為~的檔案。
- `-D`, `--dos` : 使用dos格式讀寫
- `-M`, `--mac` : 使用Mac的格式
- `-N`, `--noconvert` : 不轉換為dos或Mac的格式
- `-T [num]`, `--tabsize=[num]` : 設定顯示的欄寬。其中num為字元數
- `-V`, `--version` : 顯示nano的版本與版權
- `-k`, `--cut` : 讓^K作為換行標誌
- `-l`, `--nofollow` : 如果寫入的檔案是連結, 那麼原始檔案將不會被變更, 而會產生新檔案
- `-w`, `--nowrap` : 不論任何長度, 將不拆行

#### 編輯指令 ####

當使用nano進入檔案編輯, 如使用nano -w ipt

	  GNU nano 2.2.6               File: /etc/sysctl.conf

	#
	# /etc/sysctl.conf - Configuration file for setting system variables
	# See /etc/sysctl.d/ for additional system variables.
	# See sysctl.conf (5) for information.
	#

	#kernel.domainname = example.com

	# Uncomment the following to stop low-level messages on console
	#kernel.printk = 3 4 1 3

	##############################################################3
	# Functions previously found in netbase
	#

	# Uncomment the next two lines to enable Spoof protection (reverse-path filter)
	# Turn on Source Address Verification in all interfaces to
	# prevent some spoofing attacks
	#net.ipv4.conf.default.rp_filter=1
	#net.ipv4.conf.all.rp_filter=1

	# Uncomment the next line to enable TCP/IP SYN cookies
	# See http://lwn.net/Articles/277146/
	# Note: This may impact IPv6 TCP sessions too
	#net.ipv4.tcp_syncookies=1

	# Uncomment the next line to enable packet forwarding for IPv4
	#net.ipv4.ip_forward=1

	# Uncomment the next line to enable packet forwarding for IPv6
	#  Enabling this option disables Stateless Address Autoconfiguration
	#  based on Router Advertisements for this host
	#net.ipv6.conf.all.forwarding=1


	###################################################################
	# Additional settings - these settings can improve the network
	# security of the host and prevent against some network attacks
	# including spoofing attacks and man in the middle attacks through
	# redirection. Some network environments, however, require that these
	# settings are disabled so review and enable them as needed.
	#
	# Do not accept ICMP redirects (prevent MITM attacks)
	#net.ipv4.conf.all.accept_redirects = 0
	#net.ipv6.conf.all.accept_redirects = 0
	# _or_
	                    [ Read 60 lines (Warning: No write permission) ]
	^G Get Help   ^O WriteOut   ^R Read File  ^Y Prev Page  ^K Cut Text   ^C Cur Pos
	^X Exit       ^J Justify    ^W Where Is   ^V Next Page  ^U UnCut Text ^T To Spell

最底下兩排即為編輯指令的說明, 但編輯指令不只這些, 底下說明常用的編輯指令(^代表Ctrl按鍵)

- `^G`(F1) : 顯示help畫面
- `^X`(F2) : 關閉正開啟的檔案
- `^O`(F3) : 將目前檔案存擋
- `^J`(F4) : 檢查正確的段落, 需外掛支援
- `^R`(F5) : 將另外檔案插入目前檔案中
- `^W`(F6) : 搜尋特定文字
- `^Y`(F7) : 移動到前一個畫面
- `^V`(F8) : 移動到下一個畫面
- `^K`(F9) : 將游標所在的該行刪除, 存到暫存區
- `^U`(F10) : 將刪除的該行復原
- `^C`(F11) : 顯示游標所在的位置
- `^T`(F12) : 如果外掛程式支援, 使用拼字檢查
- `^P` : 上移一行
- `^N` : 下移一行
- `^F` : 往下一個字元移動
- `^B` : 往前一個字元移動
- `^A` : 移動到該行最前面
- `^E` : 移動到該行最後面
- `^L` : 重新整理畫面
- `^D` : 刪除游標所在字元
- `^H` : 刪除游標所在字元
- `^l` : 插入tab字元
- `^\`(F14) : 取代字元, 程式會依序詢問被取代跟取代的字元
- `^M` : 在游標處插入一個enter的訊號
- `^_`(F13) : 到特定行數

事實上, 上述許多功能可被上下左右鍵、backspace、del等按鍵取代, 常用的包括^X、^O、^R、^W、^K、^U、^C、^\、以及^_

## 本章考題練習 ##

### 5.1 ###

1. BD
2. E
3. unset VAR
4. alias
5. AE

### 5.2 ###

1. D
2. c -> z
3. D
4. B -> D
5. C
6. &&
7. C
8. C -> CE

### 5.3.1 ###

1. C
2. E -> A
3. D -> A
4. D
5. D -> C
6. D

### 5.3.2 ###

1. C
2. C
3. B
4. C -> E
5. ? -> dd
6. C -> B
7. AD -> BD

### 5.3.3 ###

1. B
2. ? -> C
3. D
4. B
5. B
6. B

### 5.4 ###

1. B -> F ?
2. D
3. 16dd -> 17dd
4. B
5. q!
6. B -> A ?
7. y -> yy.
8. AD
9. ? -> A