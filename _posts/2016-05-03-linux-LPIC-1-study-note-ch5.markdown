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

透過指令也能管理檔案, 包括顯示檔案的資訊, 檔案的複製、移動、貼上, 以及檔案間的比較
