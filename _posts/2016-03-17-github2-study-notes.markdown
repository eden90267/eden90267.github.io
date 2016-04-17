title:  "GitHub Study Notes(Day 19)"
# Day 18: 修正commit過的版本歷史紀錄 Part1 #

Git屬於分散式版本控管機制, 對於版本控管方面沒有太多的權限設計, 跟其他如Subversion或TFVC這類版控系統相比, Git提供更多「修正版本紀錄」的機制, 讓你「分享」版本給其他人時候, 能做預先整理。

## 版本控管基本原則 ##

進行版控, 維持良好的版本紀錄有助追蹤每個版本的更新歷程, 但我們很難有機會, 也不太想去追蹤某專案中軟體開發的進程, 許多專案累積版本記錄數量多達數千筆, 誰有這種閒功夫去追查歷史？

然而實務上, 當軟體bug發生時, 就需要去追蹤特定臭蟲的歷史紀錄, 以查出該臭蟲真正發生原因, 這時就是版本控管帶來最大價值的時候。

一些控管原則分享：

- 做一個小功能修改就建立版本, 這樣才容易追蹤變更
- 千萬不要累積一大堆修改後才建立一個「大版本」
- 有邏輯、有順序的修正功能, 確保相關的版本修正可按順序提交(commit), 這樣才便於追蹤

但哪個人能確保團隊所有人都能時刻照上述原則進行版控？哪個人不是「想到哪改到哪」？所以, 需要有一套「修改版本」的機制, 讓版本提交到遠端伺服器上的時候, 已經是完美的版本狀態。

## 修正 commit 歷史紀錄的理由 ##

到目前為止, 我們還沒提到關於「遠端儲存庫」的細節, 所以大部分的Git操作都還專注在本地端, 也就是工作目錄下的版本管控, 這儲存庫就位於 `.git/` 目錄下。之後遠端儲存庫的應用, 到時就不只一個人擁有儲存褲, 所需注意細節也就更多。

同一份儲存庫有多人共用情況下, 若有人任意竄改版本, 那麼Git版本控管會無法正常運作。

那什麼樣的使用情境會需要修改版本紀錄？

假設有[A] -> [B] -> [C]三個版本：

- 可能[C]版本你發現commit錯了, 必須刪除這一版本所有變更
- 你可能commit了之後才發現[C]這個版本其實只有測試程式碼, 你也想刪除它
- 其中有些版本紀錄訊息有錯字, 想修正訊息文字, 但不影響版本變更歷程
- 你可能想把這些版本的commit順序調整為 [A] -> [C] -> [B], 讓版本演進更有邏輯性
- 你發現[B]這個版本忘記加入一個重要的檔案就commit了, 你想事後補救這次變更
- 在你打算「分享」分支出去時, 發現程式碼有瑕疵, 你可以修改完再分享出去

## 修正 commit 歷史紀錄的注意事項 ##

Git保留了「修改版本歷史紀錄」的機制, 主希望你能在「自我控管版本」到一定程度後, 自己整理一下版本紀錄的各種資訊, 好讓你將版本「發布」出去後, 讓其他人能夠更清楚理解你對這些版本到底做了哪些修改。

所以, 修改版本歷史紀錄時, 有些事情必須特別注意：

- 一個儲存庫可以有許多分支(預設分支名 `master` )
- 分享Git原始碼最小單位是以「分支」為單位
- 你可任意修改某分支線上的版本, 只要你還沒「分享」給其他人
- **當你「分享」特定分支給其他人之後, 這些「已分享」的版本歷史紀錄就別再改了！**

## 準備本日練習用的版本庫 ##

之前【Day 4: 常用的Git版本控管指令】學過 `git reset` 的用法, 主要用來**重置目前的工作目錄**。不過, 相同的指令, 也可用來修正版本歷史紀錄。

	ryuutekiMacBook-Pro:Github eden90267$ mkdir git-reset-demo
	ryuutekiMacBook-Pro:Github eden90267$ cd git-reset-demo/
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ ls
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git init
	Initialized empty Git repository in /Users/eden90267/Github/git-reset-demo/.git/
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ ls
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ echo . > a.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ ls
	a.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git commit -m "Initial commit (a.txt created)"
	[master (root-commit) 2fc8078] Initial commit (a.txt created)
 	1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ echo 1 > a.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git commit -m "Update a.txt"
	[master ebcb219] Update a.txt
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ echo 1 > b.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git commit -m "Add b.txt"
	[master b611485] Add b.txt
 	1 file changed, 1 insertion(+)
 	create mode 100644 b.txt
 	
以上建立了三個版本, 執行 `git log`：

	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git log
	commit b611485fd43bf0c946080c2a36a5805c34fd32fd
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:22 2016 +0800

	    Add b.txt

	commit ebcb21993d852f0d8c14d9723db3b89d14a8b28e
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:01 2016 +0800

	    Update a.txt

	commit 2fc8078b6ba276cae9f1545754f7a1e96b78aa5c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:29:38 2016 +0800

	    Initial commit (a.txt created)
	    
## 刪除最近一次的版本 ##

用文字表達這三個版本的順序：

	[2fc807] -> [ebcb21] -> [b61148]
	
想把最後一個版本刪除, 變成：

	[2fc807] -> [ebcb21]
	
可執行 `git reset --hard "HEAD^"` , 即可刪除 `HEAD` 這個版本：**請注意**：在命令提示字元下,  `^` 是特殊符號, 所以必須用雙引號括起來！ 

	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git reset --hard "HEAD^"
	HEAD is now at ebcb219 Update a.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git log
	commit ebcb21993d852f0d8c14d9723db3b89d14a8b28e
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:01 2016 +0800

	    Update a.txt

	commit 2fc8078b6ba276cae9f1545754f7a1e96b78aa5c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:29:38 2016 +0800

	    Initial commit (a.txt created)
	    
此時可看見, 原本最新版被刪除。事實上, 原本你感覺被刪除的版本, 其實一直儲存在Git物件儲存區(object storage), 也就是這筆資料一直躺在 `.git\objects\` 目錄下。我們還是可用 `git show b61148` 取得該版本(即 commit 物件)的詳細資料。

	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git show b611
	commit b611485fd43bf0c946080c2a36a5805c34fd32fd
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:22 2016 +0800

	    Add b.txt

	diff --git a/b.txt b/b.txt
	new file mode 100644
	index 0000000..d00491f
	--- /dev/null
	+++ b/b.txt
	@@ -0,0 +1 @@
	+1
	
## 刪除最近一次的版本, 但保留最後一次的變更 ##

還記得嗎？無論你對Git儲存庫做了什麼事, 都是可以還原的, 只要執行 `git reset --hard ORIG_HEAD` 即可。

	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git reset --hard ORIG_HEAD
	HEAD is now at b611485 Add b.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git log
	commit b611485fd43bf0c946080c2a36a5805c34fd32fd
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:22 2016 +0800

	    Add b.txt

	commit ebcb21993d852f0d8c14d9723db3b89d14a8b28e
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:01 2016 +0800

	    Update a.txt

	commit 2fc8078b6ba276cae9f1545754f7a1e96b78aa5c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:29:38 2016 +0800

	    Initial commit (a.txt created)
	    
另一個刪除版本的技巧, 則是「刪除最近一次的變更, 但留下最後一次版本變更的異動內容」, 可執行 `git reset --soft "HEAD^"`:

	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git reset --soft "HEAD^"
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git log
	commit ebcb21993d852f0d8c14d9723db3b89d14a8b28e
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:01 2016 +0800

	    Update a.txt

	commit 2fc8078b6ba276cae9f1545754f7a1e96b78aa5c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:29:38 2016 +0800

	    Initial commit (a.txt created)
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git status
	On branch master
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)

		new file:   b.txt
		
這代表, 你可以保留最後一次的變更, 再加上一些變更後, 重新執行一次 `git commit` 一次, 並重新設定一個新的紀錄訊息。

## 重新提交一次最後一個版本(即 `HEAD` 版本) ##

如果你發現不小心執行了 `git commit` , 但還有檔案忘記加進去( `git add [filepath]` )或只是紀錄訊息寫錯, 想重新補上, 執行 `git commit --amend`。這個動作, 會把目前紀錄在索引中的變更檔案, 全部添加到當前最新版之中, 並要求修改原本的紀錄訊息。

	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git reset --hard ORIG_HEAD
	HEAD is now at b611485 Add b.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git status
	On branch master
	Add b.txt
	nothing to commit, working directory clean
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git log
	commit b611485fd43bf0c946080c2a36a5805c34fd32fd
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:22 2016 +0800

	    Add b.txt

	commit ebcb21993d852f0d8c14d9723db3b89d14a8b28e
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:01 2016 +0800

	    Update a.txt

	commit 2fc8078b6ba276cae9f1545754f7a1e96b78aa5c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:29:38 2016 +0800

	    Initial commit (a.txt created)
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ echo 1 > c.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git commit --amend
	[master 4278cfe] Add b.txt Add c.txt
	 Date: Thu Apr 14 23:30:22 2016 +0800
	 2 files changed, 2 insertions(+)
	 create mode 100644 b.txt
	 create mode 100644 c.txt
	ryuutekiMacBook-Pro:git-reset-demo eden90267$ git log
	commit 4278cfe7a2d42504387840899c1730143838a4d5
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:22 2016 +0800

 	   Add b.txt
 	   Add c.txt

	commit ebcb21993d852f0d8c14d9723db3b89d14a8b28e
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:30:01 2016 +0800

	    Update a.txt

	commit 2fc8078b6ba276cae9f1545754f7a1e96b78aa5c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 23:29:38 2016 +0800

	    Initial commit (a.txt created)
	    
值得注意, 最新版的 `HEAD` 已經是完全不同的 commit 物件了, 所以用 `git log` 所看到的 commit 物件絕對名稱已經跟之前不一樣了。

## 今日小結 ##

今天簡單學到如何對【最新版】(`HEAD`)進行版本的變更, 大多用在不小心 `git commit` 錯的情況, 事實上還有更多調整版本歷史紀錄的方式, 之後文章會出現。

重新整理本日學到的Git指令與參數：

- git reset --hard "HEAD^"
- git reset --soft "HEAD^"
- git reset --hard ORIG_HEAD
- git commit --amend

---

## Day 19: 設定 .gitignore 忽略清單 ##

在開發專案時, 工作目錄下可能經常有新的檔案產生(IDE工具產生的那些暫存檔案或快取檔案), 可能有許多檔案並不需要列入版本控管, 所以要排除這些檔案, 稱之「忽略清單」。

## 關於.gitignore ##

在Git, 是透過 `.gitignore` 檔案來定義「忽略清單」, 主目的是排除那些不需要版控的檔案, 列在裡面的檔名或路徑(可包含萬用字元), 都不會出現在 `git status` 的結果中, 自然也不會在執行 git add 的時候被加入。不過, 這僅限於 `Untracked file` 而已, 那些已經列入版控的檔案 (`staged file`), 不受 `.gitignore` 檔案控制。

## 透過GitHub建立預設的忽略清單 ##

建立先的儲存庫, 可讓你選擇GitHub預先幫你定義好的忽略清單, 以下是選擇Java的忽略清單：

	*.class

	# Mobile Tools for Java (J2ME)
	.mtj.tmp/

	# Package Files #
	*.jar
	*.war
	*.ear

	# virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
	hs_err_pid*
	
可參考其他語言

## 今日小結 ##

今天的 `.gitignore` 檔案, 幾乎每個專案都會用到, 算是使用Git時一個必備的重要檔案。

---

## Day 20: 修正 commit 過的版本歷史紀錄Part2 ##

在版本控管過程中, 還有個常見的狀況, 那就是當執行了多個版本, 才發現前面幾個版本改錯了, 例如不小心把測試中的程式碼也commit進去, 導致目前這個版本發生了問題, 這時就必須瞭解本篇所說明的內容。

## 準備本日練習用的版本庫 ##

	ryuutekiMacBook-Pro:Github eden90267$ mkdir git-revert-demo
	ryuutekiMacBook-Pro:Github eden90267$ cd git-revert-demo/
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ ls
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git init
	Initialized empty Git repository in /Users/eden90267/Github/git-revert-	demo/.git/
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ echo 1 > a.txt
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git commit -m "Initial commit (a.txt created)"
	[master (root-commit) 97c6d99] Initial commit (a.txt created)
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ echo 2 > a.txt
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git commit -m "Update a.txt"
	[master 2ca8456] Update a.txt
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ echo 1 > b.txt
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git commit -m "Add b.txt"
	[master 987ab2d] Add b.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	 
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git log
	commit 987ab2dedc4bb8ea5624d781a082207b82376522
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:05:17 2016 +0800

	    Add b.txt

	commit 2ca84563a627efcfdab7a0f90c50e8b3fd3b401c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:04:50 2016 +0800

 	   Update a.txt

	commit 97c6d99548057f7adcc29688a21fbd4c4a3336b2
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:02:12 2016 +0800

	    Initial commit (a.txt created)
	    
假設這個時候, 我們上一個版本( `HEAD~` 或 `HEAD^` 或 `2ca845` )被改錯了, 你希望可以將**該版本**還原就好, 而非把版本重置到第一版再重改一次, 那麼可以試試 `git revert` 指令, 他可把某個版本的變更, 透過「**相反**」的步驟把變更給還原。

## 使用 `git revert` 命令 ##

	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git log
	commit 987ab2dedc4bb8ea5624d781a082207b82376522
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:05:17 2016 +0800

	    Add b.txt

	commit 2ca84563a627efcfdab7a0f90c50e8b3fd3b401c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:04:50 2016 +0800

	    Update a.txt

	commit 97c6d99548057f7adcc29688a21fbd4c4a3336b2
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:02:12 2016 +0800

	    Initial commit (a.txt created)
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git show 2ca8
	commit 2ca84563a627efcfdab7a0f90c50e8b3fd3b401c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:04:50 2016 +0800

	    Update a.txt

	diff --git a/a.txt b/a.txt
	index d00491f..0cfbf08 100644
	--- a/a.txt
	+++ b/a.txt
	@@ -1 +1 @@
	-1
	+2
	
**請注意**: 執行 git revert 命令前, 請先確保工作目錄是乾淨的！如果有改到一半的檔案, 建議可透過 `git stash` 建立暫存版本。

相反步驟來還原： `git revert 2ca8` 這個指令, 執行成功會額外再建立一個新版本。

	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git revert 2ca8
	[master 80ea124] Revert "Update a.txt"
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git log
	commit 80ea124ce98678e25d71f5812dc8f4d91bb88aed
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:35:01 2016 +0800

	    Revert "Update a.txt"

	    This reverts commit 2ca84563a627efcfdab7a0f90c50e8b3fd3b401c.

	commit 987ab2dedc4bb8ea5624d781a082207b82376522
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:05:17 2016 +0800

	    Add b.txt

	commit 2ca84563a627efcfdab7a0f90c50e8b3fd3b401c
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:04:50 2016 +0800

	    Update a.txt

	commit 97c6d99548057f7adcc29688a21fbd4c4a3336b2
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:02:12 2016 +0800

執行過程中會讓你編輯最後要 commit 的訊息, 預設會加上 `Revert` 字樣, 還有第三行的地方加上 `This reverts commit xxxx` 告訴你說這個版本主要目的是從 `xxxx` 版本還原的。

	ryuutekiMacBook-Pro:git-revert-demo eden90267$ git show 80ea
	commit 80ea124ce98678e25d71f5812dc8f4d91bb88aed
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 05:35:01 2016 +0800

 	   Revert "Update a.txt"

 	   This reverts commit 2ca84563a627efcfdab7a0f90c50e8b3fd3b401c.

	diff --git a/a.txt b/a.txt
	index 0cfbf08..d00491f 100644
	--- a/a.txt
	+++ b/a.txt
	@@ -1 +1 @@
	-2
	+1
	
## 使用 git revert 命令失敗的情況 ##

事實上, 這個 `git revert` 是執行了「合併」的動作, 來證明這個過程。

    C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master]>type a.txt
    C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master]>echo 3 > a.txt
    C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master]>git add .
    C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master]>git commit -m "Update a.txt to '3'"
	C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master]> git log
	commit 464171e44426c7a342fb780dd622b4df3c5474d2
	Author: Eden Liu <eden90267@gmail.com>
	Date:   Fri Apr 15 13:37:36 2016 +0800

	    Update a.txt to '3

	commit 790bbd0da0ecdf0e39e4c5beb7e3ea4f8904f2bf
	Author: Eden Liu <eden90267@gmail.com>
	Date:   Fri Apr 15 13:35:24 2016 +0800

	    Revert "Update a.txt"

	    This reverts commit 7cfd95e5aec48467872ae99618aac6ee208e4fb5.

	commit 3b0770762561e0233aea5efd3e3d4e40bd3033f8
	Author: Eden Liu <eden90267@gmail.com>
	Date:   Fri Apr 15 13:34:37 2016 +0800

	    Add b.txt

	commit 7cfd95e5aec48467872ae99618aac6ee208e4fb5
	Author: Eden Liu <eden90267@gmail.com>
	Date:   Fri Apr 15 13:34:09 2016 +0800

	    Update a.txt

	commit 635c0e49e0989f386e11bbea2ac94f53c4c8f6da
	Author: Eden Liu <eden90267@gmail.com>
	Date:   Fri Apr 15 13:33:48 2016 +0800

	    Initial commit (a.txt created)

這時如果再重執行一次 `git revert 7cfd` , 試圖再還原一次, 會看到是「合併」失敗才會有的衝突情形:

	C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master]> git revert 7cfd
	warning: Cannot merge binary files: a.txt (HEAD vs. parent of 7cfd95e... Update a.txt)
	error: could not revert 7cfd95e... Update a.txt
	hint: after resolving the conflicts, mark the corrected paths
	hint: with 'git add <paths>' or 'git rm <paths>'
	hint: and commit the result with 'git commit'
	C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master +0 ~0 -0 !1 | +0 ~0 -0 !1]> git diff --text HEAD HEAD^
	diff --git a/a.txt b/a.txt
	index 00b30ed..ca90535 100644
	--- a/a.txt
	+++ b/a.txt
	@@ -1,2 +1,2 @@
	-<FF><FE>3^@^M^@
	+<FF><FE>1^@^M^@
	 ^@
	\ No newline at end of file

(實際測試果結果並未發生衝突, 而是 `a.txt` 保持原樣 `3` 的內容..只是沒有直接建立新Revert commit, 應該跟Gt版本有關)

發生本次衝突原因在於, 想還原的 `7cfd` 這個版本, 變更原是 `1` -> `2`, 還原內容是 `2` -> `1` , 但現在內容卻是 `3` 而不是 `2` , 因而發生衝突狀況。

合併衝突的解決:

1. 手動編輯 `a.txt`
2. 使用SourceTree工具, 自行選出一個想要的版本。

## 使用 `git revert` 命令套用變更, 但不執行 commit 動作 ##

使用 `git revert` 時, 預設若執行成功, 會直接建立一個commit版本, 如果希望執行git revert後還想再添加些內容, 再自行簽入, 可使用以下步驟:

假設要還原 `3b077` 這個版本, 這版本其實是新增一個 b.txt 檔案而已, 用 `git show 3b077` 顯示其變更狀況, 會看到有一行 `--- /dev/null` 其實就是代表 `3b077` 這一版之前沒這個檔案, 代表這依檔案在這版本才新增進來, 而且內容為 `1`。這也代表, 如果要成功「還原」(revert)這版, 目前 `b.txt` 內容必須為 1 , 然後才會成功執行「刪除」的動作。

	C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master]> git show --text 3b077
	commit 3b0770762561e0233aea5efd3e3d4e40bd3033f8
	Author: Eden Liu <eden90267@gmail.com>
	Date:   Fri Apr 15 13:34:37 2016 +0800

	    Add b.txt

	diff --git a/b.txt b/b.txt
	new file mode 100644
	index 0000000..ca90535
	--- /dev/null
	+++ b/b.txt
	@@ -0,0 +1,2 @@
	+<FF><FE>1^@^M^@
	+^@
	\ No newline at end of file

輸入 `git revert -n 3b077` , 沒提示任何文字, 但事實上 `b.txt` 這檔案已經成功被刪除了。

	C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master]> git status
	On branch master
	nothing to commit, working directory clean
	C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master]> git revert -n 3b077

	C:\Users\eden_liu\Documents\GitHub\git-revert-demo [master +0 ~0 -1]> git status

	On branch master
	You are currently reverting commit 3b07707.
	  (all conflicts fixed: run "git revert --continue")
	  (use "git revert --abort" to cancel the revert operation)

	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)

	        deleted:    b.txt

這時候, 索引狀態已經被更新, 但還是可以繼續修改這個版本, 直到你想完成本次動作。上圖可看到有兩個執行選項

1. `git revert --continue` 代表你已經完成所有操作, 並且建立一個新版本, 就像 `git commit` 一樣
2. `git revert --abort` 代表你準備放棄這次復原的動作, 執行這個命令會讓所有變更狀態還原, 也就是刪除的檔案又會被加回來。

**請注意**: 當 `git revert -n` 執行完後, 並不是用 `git commit` 建立版本喔!

## 今日小結 ##

今天介紹的「還原」版本的機制, 其實是透過「新增一個版本」的方式把變更的內容改回來, 而且透過這種方式, 你可以透過版本歷史紀錄中明確找出你到底是針對哪幾個版本進行還原的。另外就是這個「還原」過程, 其實跟「合併」的過程非常類似, 發生衝突時的解決方法也都如出一轍。

本日學到的Git指令與參數:

- git show [commit_id]
- git revert [commit_id]
- git revert -n [commit_id]

另外還有些參數沒介紹到, 建議可用 git help revert 查詢完整用法:

- git revert -s [commit_id] (在訊息內容加上目前使用者的簽署名稱)
- git revert -e [commit_id] (在完成版本之前顯示編輯訊息的視窗)