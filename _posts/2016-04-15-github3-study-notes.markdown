---
layout: post
title:  "GitHub Study Notes(Day 30)"
date:   2016-04-15 21:42:00 +0800
categories: [git, github]
---

# Day 21: 修正 commit 過的版本歷史紀錄 Part 3 #

在版本控管過程中, 還有個常見狀況, 那就是當你在一個分支中開發了ㄧ段時間, 但後來決定整個分支都不要了, 不過當中卻有幾個版本還想留下, 這時刪也不是, 合併回來也不是, 該怎麼辦？本篇文章將說明該如何利用 `git cherry-pick` 指令「手動挑出」你想套用的變更。

## 準備本日練習用的版本庫 ##

	ryuutekiMacBook-Pro:Github eden90267$ mkdir git-cherry-pick-demo
	ryuutekiMacBook-Pro:Github eden90267$ cd git-cherry-pick-demo/
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git init
	Initialized empty Git repository in /Users/eden90267/Github/git-cherry-pick-demo/.git/
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ echo 1 > a.txt
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git commit -m "Initial 	commit (a.txt created)"
	[master (root-commit) 5a3f649] Initial commit (a.txt created)
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ echo 2 > a.txt
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git commit -m "Update a.txt to 2"
	[master 6359f04] Update a.txt to 2
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git checkout -b branch1
	Switched to a new branch 'branch1'
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ echo 3 > a.txt
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git commit -m "Update a.txt to 3"
	[branch1 cb9d3c0] Update a.txt to 3
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ echo b > b.txt
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git commit -m "Add b.txt"
	[branch1 ee98d83] Add b.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ echo 4 > a.txt
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git commit -m "Update a.txt to 4"
	[branch1 c9314a3] Update a.txt to 4
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git checkout master
	Switched to branch 'master'
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ echo 3 > a.txt
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git commit -m "Update a.txt to 3"
	[master 3b7be57] Update a.txt to 3
	 1 file changed, 1 insertion(+), 1 deletion(-)


	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git branch
	  branch1
	* master
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git log --oneline
	3b7be57 Update a.txt to 3
	6359f04 Update a.txt to 2
	5a3f649 Initial commit (a.txt created)

## 使用 `git cherry-pick` 命令的注意事項 ##

首先, 你的「工作目錄」必須是乾淨, 工作目錄下的「索引」不能有任何準備要 commit 的檔案(staged files)在裡面, 否則將無法執行。

## 使用 `git cherry-pick` 命令 ##

使用 `git cherry-pick` 跟使用 `git revert` 非常相似, 也是讓你「挑選」任一個或多個版本, 然後套用在目前分支的最新版上, 但主要差異在於「`git revert` 執行的是相反的合併, 而 g`it cherry-pick` 則是重新套用完全相同的變更」, 但一樣都是透過「合併」的方式進行, 所以本篇會專注在講解兩種指令上的差異。

`註`：cherry-pick 的英文是「撿櫻桃」的意思, 代表你可以從其他籃子(分支)「挑」一些好的櫻桃到自己的籃子(分支)裡！

各位在使用 `git cherry-pick` 命令, 最好搭配SourceTree等GUI工具, 查看你想要「挑選」出來的版本, 然後套用在目前的分支上(當然是套用在目前的 `HEAD` 版本之後)。

	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git log branch1 -4
	commit c9314a34c8f34397e484249a7458ae36f2d21351
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 22:17:05 2016 +0800

	    Update a.txt to 4

	commit ee98d83aa5131cd49ba95f2380262c83b65b2927
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 22:16:09 2016 +0800

	    Add b.txt

	commit cb9d3c0687b73cec869a155d56a213e8f7fd1106
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 22:15:51 2016 +0800

	    Update a.txt to 3

	commit 6359f0481ef5978274429ef90fb8b45a2d5fa996
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 22:14:55 2016 +0800

	    Update a.txt to 2

今天想套用 `branch1` 的 `ee98d8 Add b.txt` 這個版本到目前的 master 版本上, 可以執行 `git cherry-pick ee98d8` 命令, 若成功執行, 則會在目前的 master 分支建立一個新版本。

	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git cherry-pick ee98d8
	[master 6fb0611] Add b.txt
	 Date: Fri Apr 15 22:16:09 2016 +0800
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt

	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git log -1
	commit 6fb06118c30072160b96fba49c93c8a3e83e7eed
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 22:16:09 2016 +0800

	    Add b.txt

不過, 與 `git revert` 最大的不同之處, 在於執行完 `git cherry-pick` 命令後, 其建立的版本訊息, 將會與你指定挑選的那些版本一模一樣, 其中包括**Author**與**Date**欄位, 都會一模一樣, 並不會用你在選項中指定的 user.name 與 user.email 參數, 這點須特別注意！

## 使用 git cherry-pick 命令的其他參數 ##

可選擇加上 -x 參數在指令列上, 就會像 git revert 那樣, 自動加上 `(cherry picked from commit ee98d83aa5131cd49ba95f2380262c83b65b2927)` 的訊息：

	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git reset --hard HEAD~1
	HEAD is now at 3b7be57 Update a.txt to 3
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git cherry-pick ee98d8 -x
	[master 51ceb2b] Add b.txt
	 Date: Fri Apr 15 22:16:09 2016 +0800
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	ryuutekiMacBook-Pro:git-cherry-pick-demo eden90267$ git log -1
	commit 51ceb2b8203fa342eeb08ccc043db9826f28b0ea
	Author: eden90267 <eden90267@gmail.com>
	Date:   Fri Apr 15 22:16:09 2016 +0800

	    Add b.txt

	    (cherry picked from commit ee98d83aa5131cd49ba95f2380262c83b65b2927)

不過, 做這動作前請先思考, 你這次挑選的版本是不是只有「本地才有的分支」上挑選的, 如果是的話, 這樣的紀錄可能會造成其他人的混淆, 因為他們查不到該版本的任何資訊。這在使用遠端儲存庫的情境比較會碰到。

如果希望建立版本前先編輯訊息: `git cherry-pick ee98d8 -e`

如果不想建立版本, 講套用其變更: `git cherry-pick ee98d8 -n`, 可再加上自己的修改後, 執行 `git commit` 建立版本。透過 `-n` 參數, 這次建立的版本就會留下自己的 Author & Date 資訊！

## 今日小結 ##

- git reset --hard "HEAD~1"
- git cherry-pick [commit_id]
- git cherry-pick [commit_id] -e
- git cherry-pick [commit_id] -x
- git cherry-pick [commit_id] -n

---

# Day 22: 修正 commit 過的版本歷史紀錄 Part 4 (Rebase) #

我們之前講了三種不同修正版本的方法, 嚴格上來說 `git revert` 與 `git cherry-pick` 並不算「修正版本歷史紀錄」, 而是套用先前曾經 commit 過的版本, 看是「重新套用」或「反向套用」的差別而已。本篇文章將要說明Git中的Rebase機制, 這個所謂的 Rebase 機制就是真的用來修改 commit 紀錄的功能了, 其功能重要且強大。

## 準備本日練習用的版本庫 ##

	ryuutekiMacBook-Pro:Github eden90267$ mkdir git-rebase-demo
	ryuutekiMacBook-Pro:Github eden90267$ cd git-rebase-demo/
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git init
	Initialized empty Git repository in /Users/eden90267/Github/git-rebase-demo/.git/
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo 1 > a.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Initial commit (a.txt created)"
	[master (root-commit) 183cb06] Initial commit (a.txt created)
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo 2 > a.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Update a.txt to 2"
	[master 591f890] Update a.txt to 2
	 2 files changed, 1 insertion(+), 1 deletion(-)
	 create mode 100644 null
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git checkout -b branch1
	Switched to a new branch 'branch1'
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo b > b.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Add b.txt"
	[branch1 4eb46b8] Add b.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo c > c.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Add c.txt"
	[branch1 b3d3cad] Add c.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 c.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo 333 > c.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Update c.txt to 333"
	[branch1 6b20aa3] Update c.txt to 333
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo d > d.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Add d.txt"
	[branch1 304ecde] Add d.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 d.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git checkout master
	Switched to branch 'master'
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo 3 > a.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Update a.txt to 3"
	[master abfad95] Update a.txt to 3
	 1 file changed, 1 insertion(+), 1 deletion(-)

## 使用 `git rebase` 命令的注意事項 ##

首先, 「工作目錄」必須是乾淨, 工作目錄下的「索引」不能有任何準備要commit的檔案(staged files)在裡面, 否則將無法執行。

再來, 也是最重要的, 如果你的分支是從遠端儲存庫下載來的, 請千萬不要透過Rebase修改版本歷史紀錄, 否則你將會無法將修改後的版本送到遠端儲存庫！

## Rebase是什麼？ ##

"Re-"與"Base"複合字, 這裡的"Base"代表「基礎版本」的意思, 表示你想要重新修改特定分支的「基礎版本」, 把另外一個分支的變更, 當成我這個分支的基礎。

先做一個簡單的示範, 大概做幾件事：

1. 切換至 `branch1` 分支: `git ckeckout branch1`
2. 然後執行Rebase動作, 把 `master` 當成我們的基礎版本: `git rebase master`

以下範例：

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git branch
	  branch1
	* master
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git checkout branch1
	Switched to branch 'branch1'
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git rebase master
	First, rewinding head to replay your work on top of it...
	Applying: Add b.txt
	Applying: Add c.txt
	Applying: Update c.txt to 333
	Applying: Add d.txt

請注意執行完 `git rebase master` 之後的顯示訊息, 他說先將我們 `branch1` 分支中的最新版本(head)倒帶(**rewind**)到跟 `master` 一樣的分支起點, 然後再重新套用(**replay**)指定的 `master` 分支中所有版本。英文的**on top of it**代表的是讓 `branch1` 分支原本的變更套用在 `master` 上面, 所謂的「上面」代表是先套用 `master` 的版本, 然後才套用 `branch1` 的版本(詳見上面範例 `Applying:` 那幾行)。

套完後看看SourceTree的版本線圖, 有沒有很神奇, 版本線圖變成一直線了！

是否覺得「分支」的感覺不見了？事實上, 分支並沒有改變, 而是這幾個版本的「套用順序」被修改了。目前版本圖狀況如同以下指令的執行順序：

1. 建立 Initail commit(a.txt created), 同時建立 master 分支
2. 建立 Update a.txt to 2
3. 建立 Update a.txt to 3
4. 建立並切換至 `branch1` 分支
5. 然後不斷 commit 到 Add d.txt 這個版本

所以, 這其實還是「兩個分支」, 並沒有被合併成一個！**千萬別認為**這張圖只有一條線, 所以只有一個分支。

有分支, 就有合併, 現在如果想要把 `branch1` 的變更, 套用到 `master` 分支上, 在使用過 Rebase 之後, 你會有兩種合併的方式：

## 1. 透過一般合併指令, 並觸發 Git 的快轉機制(Fast-forward) ##

先切換到 `master` 分支, 然後直接執行 `git merge branch1` , 這時會引發Git的快轉機制(Fast-forward)。所謂的「快轉機制」, 就是Git得知這個合併的過程, 其實會依序套用 `branch1` 原本就有的變更, 所以在合併時會直接修改 `master` 分支的 `HEAD` 參照絕對名稱, 直接移動到 `branch1` 的 `HEAD` 那個版本。

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git branch
	* branch1
	  master
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git checkout master
	Switched to branch 'master'
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git merge branch1
	Updating abfad95..e542fcd
	Fast-forward
	 b.txt | 1 +
	 c.txt | 1 +
	 d.txt | 1 +
	 3 files changed, 3 insertions(+)
	 create mode 100644 b.txt
	 create mode 100644 c.txt
	 create mode 100644 d.txt

最後我們得到的線圖還是一直線, 但可看到 `master` 的分支已經移動到跟 `branch1` 一樣了。

## 2. 透過 --no-ff 參數, 停用 Git 的快轉機制 ##

先切換到 `master` 分支, 然後直接執行 `git merge branch1 --no-ff`

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git branch
	  branch1
	* master
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git merge branch1 --no-ff
	Merge made by the 'recursive' strategy.
	 b.txt | 1 +
	 c.txt | 1 +
	 d.txt | 1 +
	 3 files changed, 3 insertions(+)
	 create mode 100644 b.txt
	 create mode 100644 c.txt
	 create mode 100644 d.txt

當你合併時指定停用Git的快轉機制, 那就代表「不允許快轉」的意思。也代表, 他會強迫你打算合併那個 `branch1` 先建立一個分支, 然後最後再合併回 `master`, 也代表我們再次變更 `branch1` 的版本線圖。最終, 看到的版本線圖會有兩條線, 比剛剛一直線還漂亮！

最後, `branch1` 用不到的話, 就可以把這個分支給刪除: `git branch -d branch1`。

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git branch -d branch1
	Deleted branch branch1 (was e542fcd).
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git branch
	* master

來比對一下, 最一開始建立的初始版本進行合併, 原 `branch1` 是從 **Update a.txt to 2 **這一版開始分支的, 透過 Rebase 之後, 分支的起點不太一樣了, 而是改由 **Update a.txt to 3** 這個分支開始, 是不是很有趣！

## 今日小結 ##

- git rebase master
- git merge branch1
- git merge branch1 --no-ff
- git branch -d branch1

---

# Day 23: 修正 commit 過的版本歷史紀錄 Part 5 #

上一篇談到Rebase是用來將現有兩個分支進行「重新指定基礎版本」, 執行Rebase後, 也會改掉原本分支的起點(分支點移動了), 所以導致版本線圖發生變化。不過Rebase可以做到的不只這樣, 他還可以用來修改特定分支線上任何一個版本的版本資訊。

## 準備本日練習用的版本庫 ##

	ryuutekiMacBook-Pro:~ eden90267$ cd Github/
	ryuutekiMacBook-Pro:Github eden90267$ mkdir git-rebase-demo
	mkdir: git-rebase-demo: File exists
	ryuutekiMacBook-Pro:Github eden90267$ mkdir git-rebase-demp
	ryuutekiMacBook-Pro:Github eden90267$ rmdir git-rebase-demp/
	ryuutekiMacBook-Pro:Github eden90267$ clear
	ryuutekiMacBook-Pro:Github eden90267$ mkdir git-rebase-demo
	ryuutekiMacBook-Pro:Github eden90267$ cd git-rebase-demo/
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git init
	Initialized empty Git repository in /Users/eden90267/Github/git-rebase-demo/.git/
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo 1 > a.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Initial commit (a.txt created)"
	[master (root-commit) c159e18] Initial commit (a.txt created)
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo 2 > a.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Update a.txt to 2"
	[master a0c0b03] Update a.txt to 2
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git checkout -b branch1
	Switched to a new branch 'branch1'
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo b > b.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Add b.txt"
	[branch1 a58e098] Add b.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo c > c.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Add c.txt"
	[branch1 d8e7168] Add c.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 c.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo 333 > c.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Update c.txt to 333"
	[branch1 aeefc04] Update c.txt to 333
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo d > d.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Add d.txt"
	[branch1 9010401] Add d.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 d.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git checkout master
	Switched to branch 'master'
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo 3 > a.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Update a.txt to 3"
	[master 6c7cbe1] Update a.txt to 3
	 1 file changed, 1 insertion(+), 1 deletion(-)

## 使用 git rebase 命令的注意事項 ##

還是必須重申一次！「工作目錄乾淨」, 「索引」不能有任何準備要commit的檔案(staged files), 否則指令將會無法執行。

若分支是從遠端儲存庫下載下來的, 請**千萬不要**透過Rebase修改版本歷史紀錄, 否則將無法將修改後的版本送到遠端儲存庫！

## Rebase能做的事 ##

上篇文章講的, 你可以將某個分支當成自己目前分支的「基礎版本」。除了這件事以外, 你還可用修改某個分支中「特定一段」歷程的紀錄, 可做的事包括：

1. 調換commit的順序
2. 修改commit的訊息
3. 插入一個commit
4. 編輯一個commit
5. 拆解一個commit
6. 壓縮一個commit, 且保留訊息紀錄
7. 壓縮一個commit, 但丟棄版本紀錄
8. 刪除一個commit

這八件事, 可完整看出Rebase修正歷史版本紀錄的強大威力, 接下來就逐一介紹Rebase幫我們修訂版本。

## 1. 調換 commit 的順序 ##

首先, 先切換到 `branch1` 分支

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git checkout branch1
	Switched to branch 'branch1'
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git branch
	* branch1
	  master

到SourceTree查看版本線圖, 然後先決定這個分支中想執行的Rebase的起點, 決定後直接在SourceTree複製該版本的**絕對名稱**。在該版本按下滑鼠右鍵, 再點選**Copy SHA to Clipboard**即可將id複製到剪貼簿中

選 `Update a.txt to 2`

然後執行 git rebase 39cd446e65f50467ec604704e6d08ad0eb81a630 -i

跳出編輯器, 並讓你編輯一系列「指令」：

	pick d57449a Add b.txt
	pick ab053d5 Add c.txt
	pick 1186209 Update c.txt to 333
	pick 3cdac55 Add d.txt

	# Rebase 39cd446..3cdac55 onto 39cd446 (4 command(s))
	#
	# Commands:
	# p, pick = use commit
	# r, reword = use commit, but edit the commit message
	# e, edit = use commit, but stop for amending
	# s, squash = use commit, but meld into previous commit
	# f, fixup = like "squash", but discard this commit's log message
	# x, exec = run command (the rest of the line) using shell
	#
	# These lines can be re-ordered; they are executed from top to bottom.
	#
	# If you remove a line here THAT COMMIT WILL BE LOST.
	#
	# However, if you remove everything, the rebase will be aborted.
	#
	# Note that empty commits are commented out
	~
	~

你如果什麼都不改, 就是執行這一系列 pick 等動作, 必須了解是這些指令的格式, 與其意。

先看第一行, 區分成三個欄位, 分別以「空白字元」間隔, 分別如下：

1. `pick`代表的是「命令」
2. `d57449a` 代表的是要使用的commit物件編號
3. 剩下文字則是這版本的紀錄訊息摘要

若有看過【Day 22: 修正 commit 過的版本歷史紀錄 Part 4】, 應該知道Rebase執行, 會先將目前 `branch1` 分支的最新版本(head)倒帶(rewind)到你這次指定的分支起點(rewinding head), 在這例子, 我們指定的分支起點是  `39cd446e65f50467ec604704e6d08ad0eb81a630` 這個節點。

我們用 `git log` 查看目前的版本紀錄, 我們指定的那個版本, 並不在我們Rebase指令清單中。該指令清單, 由上至下分別是「最舊版」到「最新版」的順序, 跟我們 `git log` 執行的顯示順序剛好相反。

	pick d57449a Add b.txt
	pick ab053d5 Add c.txt
	pick 1186209 Update c.txt to 333
	pick 3cdac55 Add d.txt

這裡的 pick 代表的功能是 `use commit` , 也就是我們要用這個版本來commit新版本。也就是上篇文章講到的**重新套用**(replay)這個字, 所以這幾個指令, 就是讓你在分支「倒帶」之後, 重新用這幾個版本套用一次版本變更, 而且重新套用的過程會沿用當時版本的變更紀錄。

現在先嘗試「調換 commit 的順序」, 只要很簡單的修改這份文字檔, 把版本前後順序對調即可:

	pick ab053d5 Add c.txt
	pick d57449a Add b.txt
	pick 1186209 Update c.txt to 333
	pick 3cdac55 Add d.txt

然後存檔退出, 看指令最後執行結果如何, 可發現, 這兩個版本真的對調了

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git rebase 39cd446e65f50467ec604704e6d08ad0eb81a630 -i
	Successfully rebased and updated refs/heads/branch1.
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git log --oneline
	2a56208 Add d.txt
	9cff35e Update c.txt to 333
	a650ba9 Add b.txt
	3e7e741 Add c.txt
	39cd446 Update a.txt to 2
	bab19ab Initial commit (a.txt created)

這裡有一點必須特別注意, 那就是從 `39cd446e65f50467ec604704e6d08ad0eb81a630` 這個版本開始, 所有後續版本的 commit 絕對名稱全部都不一樣了, 這代表我們在Rebase的過程會重新建立許多新的commit物件。那舊的到哪裡去了？其實全部都還在, 只是找不到罷了, 全部都躺在Git物件儲存庫中, 只要你知道這些版本的絕對名稱, 可以隨時取出！(git reflog命令)

SourceTree看一下版本線圖, 感覺上跟之前的差很多, 但其實只有這兩個版本調換而已, 線圖不太一樣的原因是時間序改變了, 應該是SourceTree顯示邏輯跟時間序有關, 才會讓這線圖變得跟之前差這麼多。

## 2. 修改 commit 的訊息 ##

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git rebase 39cd446e65f50467ec604704e6d08ad0eb81a630 -i

	pick 3e7e741 Add c.txt
	pick a650ba9 Add b.txt
	reword 9cff35e Update c.txt to 333
	pick 2a56208 Add d.txt

想修改 `9cff35e` 這個版本訊息, 把前面 `pick` 改成 `reword` 即可。

然後存檔退出, Git接著會開始重新 pick 這些版本進行套用, 但套用到 reword這個命令, 會重新開啟一次文字編輯器, 讓你可在此時變更版本訊息文字, 改成： `Update: c.txt is changed to 333`

	[detached HEAD 7c12022] Update: c.txt is changed to 333
 	Date: Sun Apr 17 23:44:21 2016 +0800
 	1 file changed, 1 insertion(+), 1 deletion(-)
	Successfully rebased and updated refs/heads/branch1.

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git log --oneline
	c154d34 Add d.txt
	7c12022 Update: c.txt is changed to 333
	a650ba9 Add b.txt
	3e7e741 Add c.txt
	39cd446 Update a.txt to 2
	bab19ab Initial commit (a.txt created)

該版本與**後續版本**的commit物件編號也會不一樣了, 不一樣代表這兩個是新的commit物件。

## 3. 插入一個commit ##

在執行一次 `git rebase 39cd446e65f50467ec604704e6d08ad0eb81a630 -i`

	pick 3e7e741 Add c.txt
	pick a650ba9 Add b.txt
	pick 7c12022 Update: c.txt is changed to 333
	pick c154d34 Add d.txt

想在 `7c12022` 版本之後「插入一個新版本」, 只要在 `7c12022` 這行前面的 pick 改成 edit 即可讓Rebase在重新套用的過程中「暫停」在這個版本, 然後讓你可對這版本進行編輯動作。

	pick 3e7e741 Add c.txt
	pick a650ba9 Add b.txt
	edit 7c12022 Update: c.txt is changed to 333
	pick c154d34 Add d.txt

然後存檔退出, 接著Git會開始執行套用, 等執行到 `7c12022` 這個版本時, 套用的動作會被中斷, 並提示你可執行 `git commit --amend` 重新執行一次 commit動作：

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git rebase 	39cd446e65f50467ec604704e6d08ad0eb81a630 -i
	Stopped at 7c12022e993b9d813b684e966ab00acccc0609e7... Update: c.txt is changed to 333
	You can amend the commit now, with

		git commit --amend

	Once you are satisfied with your changes, run

		git rebase --continue

接下來直接在這階段「建立新版本」：

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ echo test > z.txt
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git status
	rebase in progress; onto 39cd446
	You are currently editing a commit while rebasing branch 'branch1' on '39cd446'.
	  (use "git commit --amend" to amend the current commit)
	  (use "git rebase --continue" once you are satisfied with your changes)

	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)

		new file:   z.txt

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git commit -m "Add z.txt"
	[detached HEAD f611c51] Add z.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 z.txt

接著執行 `git rebase --continue` 讓Rebase指令繼續完成。

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git rebase --continue
	Successfully rebased and updated refs/heads/branch1.

	ryuutekiMacBook-Pro:git-rebase-demo eden90267$ git log --oneline
	b42073b Add d.txt
	f611c51 Add z.txt
	7c12022 Update: c.txt is changed to 333
	a650ba9 Add b.txt
	3e7e741 Add c.txt
	39cd446 Update a.txt to 2
	bab19ab Initial commit (a.txt created)

## 4. 編輯一個commit ##

編輯一個commit如同【插入一個commit】, 版本修正 `edit` 命令, 就可利用 `git commit --amend` 重新執行一次 commit 動作, 等同編輯了某個版本紀錄。

## 5. 拆解一個commit ##

拆解一個commit紀錄, 代表的是你想把「某一個commit紀錄」變成兩筆記錄, 其實這動作跟【插入一個commit幾乎完全一樣】。查別在你只要把編輯中的那個版本, 將某些檔案從「索引」狀態中移除, 然後執行 `git commit --amend` 就可建立一個新版。然後再執行 `git add .` , `git commit`, 即可將原本一個版本變更兩個版本。

## 6. 壓縮一個commit, 且合併訊息紀錄 ##

壓縮一個commit, 代表這幾個版本中, 有個版本訊息有點多餘, 而且可把這個版本的變更合併到「上一個版本」, 那麼可修改Rebase指令, 把 `pick` 修改為 `squash` 即可。

透過壓縮方式, 被套用 `squash` 命令的版本, 其「版本記錄訊息」會被自動加入到「上一個版本」的訊息中。

## 7. 壓縮一個commit, 但丟棄版本紀錄 ##

想合併兩個版本變更, 但不需要合併紀錄訊息, 那麼可修改Rebase指令, 把 `pick` 修改為 `fixup` 即可。

## 8. 刪除一個commit ##

刪除一個commit是最簡單的, 只要直接把要刪除這幾行 `pick` 命令給移除即可。

## 今日小結 ##

透過 git rebase 可以有效幫你「重整版本」, 不但讓你的 Git 版本紀錄更加易懂, 也更有邏輯。這樣的好處, 在多人開發的Git專案尤其明顯。

- git rebase [commit_id] -i
- git commit --[amend | continue]

---

# Day 24: 使用GitHub遠端儲存庫 - 入門篇 #

GitHub是目前最多人採用的Git線上管理平台, 它包含完整的Git遠端儲存庫實作, 還有完整的議題追蹤機制與報表, 更有成千上萬的開源專案都在GitHub進行版本控管。本篇文章主要講述GitHub最基本的使用方式。

GitHub建立專案有兩種不同方式。

## 將本地儲存庫的變更上傳到遠端儲存庫的方法 ##

本地變更送上GitHub有好幾種不同方式：

1. 在GitHub建立一個「沒有版本」的空白Git儲存庫, 然後透過 `git clone` 取得遠端儲存庫, 再建立版本後上傳。
2. 在GitHub建立一個「沒有版本」的空白Git儲存庫, 然後直接將現有的本地Git儲存庫上傳到指定的GitHub專案
3. 在GitHub建立一個「有初始化版本」的Git儲存庫, 然後透過 `git clone` 取得遠端儲存庫, 再建立版本後上傳
4. 在GitHub建立一個「有初始化版本」的Git儲存庫, 然後直接將現有的本地Git儲存庫上傳到指定的GitHub專案

嚴格說起來應該只有兩種：

1. 透過 `git clone` 取得遠端儲存庫, 再建立版本後上傳

	「沒有版本」的空白儲存庫, 連預設的 `master` 分支都沒有, 所以用預設的指令無法上傳到遠端儲存庫。所以在指令操作上有兩種用法。

2. 直接將現有的本地Git儲存庫上傳到指定的GitHub專案

	因為通常一個Git儲存庫只會有一個「初始commit物件」, 如果本地儲存庫已經建立了幾個版本, 代	表在你的本地儲存庫中已經有了自己的「初始commit物件」, 這將與GitHub建立的「有初始化版本」	的Git儲存庫相互衝突, 所以上傳的指令也會有些不同。

## 透過 `git clone` 取得遠端儲存庫, 再建立版本後上傳 ##

這方法最簡單, 因還沒有本地儲存庫的存在, 甚至連工作目錄都沒有, 所以直接利用git clone即可把專案下載。使用GitHub, 最簡單方法是利用GitHub for Windows工具。你只要點擊**Clone in Desktop**按鈕, 即可自動啟動GitHub for Windows工具幫你下載Git專案。

	ryuutekiMacBook-Pro:Github eden90267$ git clone https://github.com/eden90267/sandbox-empty.git
	Cloning into 'sandbox-empty'...
	warning: You appear to have cloned an empty repository.
	Checking connectivity... done.
	ryuutekiMacBook-Pro:Github eden90267$ git clone https://github.com/eden90267/sandbox-initialized.git
	Cloning into 'sandbox-initialized'...
	remote: Counting objects: 5, done.
	remote: Compressing objects: 100% (4/4), done.
	remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0
	Unpacking objects: 100% (5/5), done.
	Checking connectivity... done.

`sandox-empty`這專案為例, 這是一個「沒有版本」的空白Git儲存庫, 在GitHub上的遠端儲存庫是完全空的, 連預設的 `master` 分支都沒有, 所以下達 `git push` 要加上 `-u` 參數, 才能成功把本地儲存庫上傳到GitHub上的遠端儲存庫, 其指令 `git push -u origin master`

	ryuutekiMacBook-Pro:Github eden90267$ cd sandbox-empty/
	ryuutekiMacBook-Pro:sandbox-empty eden90267$ git log
	fatal: bad default revision 'HEAD'
	ryuutekiMacBook-Pro:sandbox-empty eden90267$ git status
	On branch master

	Initial commit

	nothing to commit (create/copy files and use "git add" to track)
	ryuutekiMacBook-Pro:sandbox-empty eden90267$ echo a > a.txt
	ryuutekiMacBook-Pro:sandbox-empty eden90267$ git add .
	ryuutekiMacBook-Pro:sandbox-empty eden90267$ git commit -m "Initial commit"
	[master (root-commit) a809a35] Initial commit
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:sandbox-empty eden90267$ git push -u origin master
	Username for 'https://github.com': eden90267
	Password for 'https://eden90267@github.com':
	Counting objects: 3, done.
	Writing objects: 100% (3/3), 206 bytes | 0 bytes/s, done.
	Total 3 (delta 0), reused 0 (delta 0)
	To https://github.com/eden90267/sandbox-empty.git
	 * [new branch]      master -> master
	Branch master set up to track remote branch master from origin.

	ryuutekiMacBook-Pro:Github eden90267$ cd sandbox-initialized/
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git log
	commit bf43336363e37c51a4b345e82dae5a564a1df7f0
	Author: eden90267 <eden90267@gmail.com>
	Date:   Mon Apr 18 12:19:04 2016 +0800

	    Initial commit
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git branch
	* master
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ echo a > a.txt
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git add .
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git commit -m "Add a.txt"
	[master e357acf] Add a.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git push origin master
	Username for 'https://github.com': eden90267
	Password for 'https://eden90267@github.com':
	Counting objects: 3, done.
	Delta compression using up to 8 threads.
	Compressing objects: 100% (2/2), done.
	Writing objects: 100% (3/3), 259 bytes | 0 bytes/s, done.
	Total 3 (delta 1), reused 0 (delta 0)
	To https://github.com/eden90267/sandbox-initialized.git
	   bf43336..e357acf  master -> master

當第二次建立版本, 直接執行 `git push` 就會自動上傳成功：

	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git log --oneline
	e357acf Add a.txt
	bf43336 Initial commit
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ echo b > b.txt
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git add .
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git commit -m "Add b.txt"
	[master cabf663] Add b.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git push
	warning: push.default is unset; its implicit value has changed in
	Git 2.0 from 'matching' to 'simple'. To squelch this message
	and maintain the traditional behavior, use:

	  git config --global push.default matching

	To squelch this message and adopt the new behavior now, use:

	  git config --global push.default simple

	When push.default is set to 'matching', git will push local branches
	to the remote branches that already exist with the same name.

	Since Git 2.0, Git defaults to the more conservative 'simple'
	behavior, which only pushes the current branch to the corresponding
	remote branch that 'git pull' uses to update the current branch.

	See 'git help config' and search for 'push.default' for further information.
	(the 'simple' mode was introduced in Git 1.7.11. Use the similar mode
	'current' instead of 'simple' if you sometimes use older versions of Git)

	Username for 'https://github.com': eden90267
	Password for 'https://eden90267@github.com':
	Counting objects: 3, done.
	Delta compression using up to 8 threads.
	Compressing objects: 100% (2/2), done.
	Writing objects: 100% (3/3), 259 bytes | 0 bytes/s, done.
	Total 3 (delta 1), reused 0 (delta 0)
	To https://github.com/eden90267/sandbox-initialized.git
	   e357acf..cabf663  master -> master


不過, 當執行簡單版本的 `git push` 會出現一段提示, 告訴你要設定 `push.default` 這個選項, 因這種簡寫的 `git push` 方法, Git的預設行為將會在Git 2.0之後發生改變, 建議你透過設定 `push.default` 選項的方式明確指定 push 的方法。建議設定成 `simple` , 以利跟日後的Git指令列工具的預設值相同。

	git config --global push.default simple

設定好後, 下次執行 git push 就不會再出現提示訊息了。

	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git config --global 	push.default simple
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ echo c > c.txt
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git add .
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git commit -m "Add c.txt"
	[master 2883bfb] Add c.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 c.txt
	ryuutekiMacBook-Pro:sandbox-initialized eden90267$ git push
	Username for 'https://github.com': eden90267
	Password for 'https://eden90267@github.com':
	Counting objects: 3, done.
	Delta compression using up to 8 threads.
	Compressing objects: 100% (2/2), done.
	Writing objects: 100% (3/3), 261 bytes | 0 bytes/s, done.
	Total 3 (delta 1), reused 0 (delta 0)
	To https://github.com/eden90267/sandbox-initialized.git
	   cabf663..2883bfb  master -> master

## 直接將現有的本地 Git 儲存庫上傳到指定的 GitHub 專案 ##

	ryuutekiMacBook-Pro:~ eden90267$ cd Github/
	ryuutekiMacBook-Pro:Github eden90267$ mkdir sandbox-empty2
	ryuutekiMacBook-Pro:Github eden90267$ cd sandbox-empty2/
	ryuutekiMacBook-Pro:sandbox-empty2 eden90267$ git init
	Initialized empty Git repository in /Users/eden90267/Github/sandbox-empty2/.git/
	ryuutekiMacBook-Pro:sandbox-empty2 eden90267$ echo a > a.txt
	ryuutekiMacBook-Pro:sandbox-empty2 eden90267$ git add .
	ryuutekiMacBook-Pro:sandbox-empty2 eden90267$ git commit -m "Initial commit"
	[master (root-commit) 935e445] Initial commit
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:sandbox-empty2 eden90267$ cd ..
	ryuutekiMacBook-Pro:Github eden90267$ mkdir sandbox-initialized2
	ryuutekiMacBook-Pro:Github eden90267$ cd sandbox-initialized2/
	ryuutekiMacBook-Pro:sandbox-initialized2 eden90267$ git init
	Initialized empty Git repository in /Users/eden90267/Github/sandbox-	initialized2/.git/
	ryuutekiMacBook-Pro:sandbox-initialized2 eden90267$ echo a > a.txt
	ryuutekiMacBook-Pro:sandbox-initialized2 eden90267$ git add .
	ryuutekiMacBook-Pro:sandbox-initialized2 eden90267$ git commit -m "Initial commit"
	[master (root-commit) 81a684a] Initial commit
 	1 file changed, 1 insertion(+)
 	create mode 100644 a.txt

以 `sandbox-empty2` 專案為例, 由於本地儲存庫跟GitHub上的遠端儲存庫完全沒關連, 所以必須告訴Git遠端儲存庫在哪。這時可輸入 `git remote add origin http://github.com/eden90267/sandbox-empty2.git` 建立一個名為 `origin` 的參照名稱, 並指向 `http://github.com/eden90267/sandbox-empty2.git` 位址, 也就是我們在GitHub上的遠端儲存庫位址：

	ryuutekiMacBook-Pro:sandbox-empty2 eden90267$ git remote add origin 	https://github.com/eden90267/sandbox-empty2.git
	fatal: remote origin already exists.
	ryuutekiMacBook-Pro:sandbox-empty2 eden90267$ git branch
	* master
	ryuutekiMacBook-Pro:sandbox-empty2 eden90267$ git push -u origin master
	Username for 'https://github.com': eden90267
	Password for 'https://eden90267@github.com':
	Branch master set up to track remote branch master from origin.
	Everything up-to-date

再以 sandbox-initialized2 專案為例, 請記得複製正確的URL位址：

	ryuutekiMacBook-Pro:sandbox-initialized2 eden90267$ git remote add origin 	https://github.com/eden90267/sandbox-initialized2.git
	ryuutekiMacBook-Pro:sandbox-initialized2 eden90267$ git push origin master
	Username for 'https://github.com': eden90267
	Password for 'https://eden90267@github.com':
	To https://github.com/eden90267/sandbox-initialized2.git
	 ! [rejected]        master -> master (fetch first)
	error: failed to push some refs to 'https://github.com/eden90267/sandbox-initialized2.git'
	hint: Updates were rejected because the remote contains work that you do
	hint: not have locally. This is usually caused by another repository pushing
	hint: to the same ref. You may want to first integrate the remote changes
	hint: (e.g., 'git pull ...') before pushing again.
	hint: See the 'Note about fast-forwards' in 'git push --help' for details.

可發現, 這次的 `git push` 動作被GitHub拒絕了！因為不能把兩個無關的Git版本直接上傳到GitHub上的遠端儲存庫。

解決方法： 只要把遠端的儲存庫 `master` 分支, 成功合併回我本地的分支, 即可建立兩個不同版本庫之間的關聯, 這樣就可以把本地的 `master` 分支推送到GitHub上遠端的 `master` 分支了。

將遠端儲存庫 master 分支取回, 並合併到本地儲存庫的 master 分支, 有兩個方法：

1. 使用 `git pull origin master` 指令
2. 使用 git fetch 指令後執行 git merge origin/master 合併動作(這方法後續文章詳述)

		ryuutekiMacBook-Pro:sandbox-initialized2 eden90267$ git pull origin master
		warning: no common commits
		remote: Counting objects: 5, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0
		Unpacking objects: 100% (5/5), done.
		From https://github.com/eden90267/sandbox-initialized2
		 * branch            master     -> FETCH_HEAD
		 * [new branch]      master     -> origin/master
		Merge made by the 'recursive' strategy.
		 .gitignore |  12 +++
		 LICENSE    | 339 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
		 README.md  |   1 +
		 3 files changed, 352 insertions(+)
		 create mode 100644 .gitignore
		 create mode 100644 LICENSE
		 create mode 100644 README.md
		ryuutekiMacBook-Pro:sandbox-initialized2 eden90267$ git push origin master
		Username for 'https://github.com': eden90267
		Password for 'https://eden90267@github.com':
		Counting objects: 5, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (3/3), done.
		Writing objects: 100% (5/5), 494 bytes | 0 bytes/s, done.
		Total 5 (delta 1), reused 0 (delta 0)
		To https://github.com/eden90267/sandbox-initialized2.git
	   	0d9817b..728b15e  master -> master

今日小結：

如果透過GitHub for Windows操作, 要上傳到GitHub只要簡單按下sync按鈕, 就可自動上傳, 連帳密都不用輸入！

- git push origin master
- git push -u origin master
- git pull origin master
- git config --global push.default simple
- git push
- git fetch
- git merge origin/master

---

# Day 25: 使用 GitHub 遠端儲存庫 - 觀念篇 #

上篇學習了 `git clone`, `git pull`, `git push`, 本篇文章談述遠端儲存庫的其他細節。

## 與遠端儲存庫有關的指令 ##

- `git clone`

	將遠端儲存庫複製到本地, 並建立**工作目錄**與**本地儲存庫**(就是 `.git` 資料夾)

- `git pull`

	將遠端儲存庫的最新版下載回來, 下載的內容包含完整的物件儲存庫(object storage)。並將遠端分支合併到本地分支。(將 `origin/master` 遠端分支合併到 `master` 本地分支)

	所以一個 `git pull` 動作, 相等於以下兩段指令:

	   	git fetch
    	git merge origin/master

- `git push`

	將本地儲存庫中目前分支的所有相關物件**推送**到遠端儲存庫中

- `git fetch`

	將遠端儲存庫的最新版下載回來, 下載內容包含完整的物件儲存庫(object storage)。這命令不包含「合併」分支的動作

- `git ls-remote`

	顯示特定遠端儲存庫的參照名稱。包含**遠端分支**與**遠端標籤**。

## 關於追蹤分支的概念 ##

之前學習「本地分支」,　現在多出了「遠端分支」，事情就相對複雜些。

版本控管加上了遠端儲存庫之後，原本分支就可被拆成四種不同的概念:

1. 遠端追蹤分支

	這分支位於遠端, 目的是用來追蹤分支的變化情形。通常遠端分支妳是存取不到的。

2. 本地追蹤分支

	當執行 `git clone` 複製一個遠端容器回來後, 所有遠端追蹤分支會被下載回來, 並相對應的建立起一個同名的**本地追蹤分支**。

	以複製JQuery在GitHub上的專案為例:

		C:\Users\eden_liu\Documents\GitHub> git config --system http.sslverify false
		C:\Users\eden_liu\Documents\GitHub> git clone https://github.com/jquery/jquery.git
		Cloning into 'jquery'...
		remote: Counting objects: 40810, done.
		remote: Total 40810 (delta 0), reused 0 (delta 0), pack-reused 40810
		Receiving objects: 100% (40810/40810), 24.39 MiB | 4.80 MiB/s, done.
		Resolving deltas: 100% (29016/29016), done.
		Checking connectivity... done.
		C:\Users\eden_liu\Documents\GitHub> cd .\jquery
		C:\Users\eden_liu\Documents\GitHub\jquery [master]> git branch -a
		* master
	  	  remotes/origin/1.12-stable
  		  remotes/origin/2.2-stable
  		  remotes/origin/HEAD -> origin/master
  		  remotes/origin/killphp
  		  remotes/origin/master

	remote/* 就是「本地追蹤分支」

3. 本地分支

	在透過 `git branch` 指令執行時所顯示的分支, 就是「本地分支」, 這些分支存在本地端, 而這些分支又常被稱為 **主題分支**(Topic Branch)或 **開發分支**(Development Branch), 就是因為這些分支預設不會被推送到遠端儲存庫, 主要用作開發用途

4. 遠端分支

	遠端分支就是遠端儲存庫的分支。如果用GitHub, 是無法存取的。

	雖說「概念上」可分這四項, 但其實這些分支就只是**參照名稱**而已, 而這裡的「追蹤分支」主要用來跟遠端的分支做對應, 你不應該直接在這些分支上建立版本(雖還是可這樣做, 但強烈不建議亂搞), 而是把這些「本地追蹤分支」視為是一種「唯讀」的分支。

## 註冊遠端儲存庫 ##

上篇有提過可以透過 `git remote` 指令手動加入一個「遠端儲存庫」:

    git remote add origin https://github.com/eden90267/sandbox-empty2.git

這個 `origin` 名稱是在Git版本控管中慣用的預設遠端分支的參照名稱, **主目的用來代表一個遠端儲存庫的URL位址**。

不過, 事實上可在你的工作目錄中, 建立多個遠端儲存庫的參照位址。例如 `sandbox-empty2` 為例, 先複製一份回來, 然後透過 `git remote -v` 可列出目前註冊工作目錄裡的遠端儲存庫資訊。

	C:\Users\eden_liu\Documents\GitHub> git clone https://github.com/eden90267/sandox-empty2.git
	Cloning into 'sandbox-empty2'...
	warning: You appear to have cloned an empty repository.
	Checking connectivity... done.
	C:\Users\eden_liu\Documents\GitHub> cd .\sandbox-empty2
	C:\Users\eden_liu\Documents\GitHub\sandbox-empty2 [master]> git remote -v
	origin  https://github.com/eden90267/sandbox-empty2.git (fetch)
	origin  https://github.com/eden90267/sandbox-empty2.git (push)

如果我們額外將jQuery的遠端儲存庫也一併下載回來, 則可用以下指令先行註冊一個名稱起來

    git remote add jquery https://github.com/jquery/jquery.git

最後再用 `git fetch` 指令把完整的jQuery遠端儲存庫一併下載回來:

    C:\Users\eden_liu\Documents\GitHub\sandbox-empty2 [master]> git remote add jquery https://github.com/jquery/jquery.git
    C:\Users\eden_liu\Documents\GitHub\sandbox-empty2 [master]> git remote -v
    jquery  https://github.com/jquery/jquery.git (fetch)
    jquery  https://github.com/jquery/jquery.git (push)
    origin  https://github.com/eden90267/sandbox-empty2.git (fetch)
    origin  https://github.com/eden90267/sandbox-empty2.git (push)
    C:\Users\eden_liu\Documents\GitHub\sandbox-empty2 [master]> git fetch jquery
    remote: Counting objects: 40180, done.
    remote: Total 40180 (delta 0), reused 0 (delta 0), pack-reused 40180
    Receiving objects: 100% (40180/40180), 20.92 MiB | 2.77 MiB/s, done.
    Resolving deltas: 100% (28671/28671), done.
    From https://github.com/jquery/jquery
	 * [new branch]      1.12-stable -> jquery/1.12-stable
	 * [new branch]      2.2-stable -> jquery/2.2-stable
	 * [new branch]      killphp    -> jquery/killphp
	 * [new branch]      master     -> jquery/master
	 * [new tag]         1.0        -> 1.0
	 * [new tag]         1.0.1      -> 1.0.1
	 * [new tag]         1.0.2      -> 1.0.2
	 * [new tag]         1.0.3      -> 1.0.3
	 * [new tag]         1.0.4      -> 1.0.4
	 * [new tag]         1.0a       -> 1.0a
	 * [new tag]         1.1        -> 1.1
	 * [new tag]         1.1.1      -> 1.1.1
	 * [new tag]         1.1.2      -> 1.1.2
	 * [new tag]         1.1.3      -> 1.1.3
	 * [new tag]         1.1.3.1    -> 1.1.3.1
	 * [new tag]         1.1.3a     -> 1.1.3a
	 * [new tag]         1.1.4      -> 1.1.4
	 * [new tag]         1.10.0-beta1 -> 1.10.0-beta1
	 * [new tag]         1.11.0-beta1 -> 1.11.0-beta1
	 * [new tag]         1.1a       -> 1.1a
	 * [new tag]         1.1b       -> 1.1b
	 * [new tag]         1.2        -> 1.2
	 * [new tag]         1.2.1      -> 1.2.1
	 * [new tag]         1.2.2      -> 1.2.2
	 * [new tag]         1.2.2b     -> 1.2.2b
	 * [new tag]         1.2.2b2    -> 1.2.2b2
	 * [new tag]         1.2.3a     -> 1.2.3a
	 * [new tag]         1.2.3b     -> 1.2.3b
	 * [new tag]         1.2.4      -> 1.2.4
	 * [new tag]         1.2.4a     -> 1.2.4a
	 * [new tag]         1.2.4b     -> 1.2.4b
	 * [new tag]         1.2.5      -> 1.2.5
	 * [new tag]         1.3.1rc1   -> 1.3.1rc1
	 * [new tag]         1.3b1      -> 1.3b1
	 * [new tag]         1.3b2      -> 1.3b2
	 * [new tag]         1.3rc1     -> 1.3rc1
	 * [new tag]         1.4.3rc1   -> 1.4.3rc1
	 * [new tag]         1.4.3rc2   -> 1.4.3rc2
	 * [new tag]         1.4.4rc1   -> 1.4.4rc1
	 * [new tag]         1.4.4rc2   -> 1.4.4rc2
	 * [new tag]         1.4.4rc3   -> 1.4.4rc3
	 * [new tag]         1.4a1      -> 1.4a1
	 * [new tag]         1.4a2      -> 1.4a2
	 * [new tag]         1.4rc1     -> 1.4rc1
	 * [new tag]         1.5.1rc1   -> 1.5.1rc1
	 * [new tag]         1.5.2rc1   -> 1.5.2rc1
	 * [new tag]         1.5b1      -> 1.5b1
	 * [new tag]         1.5rc1     -> 1.5rc1
	 * [new tag]         1.6.1rc1   -> 1.6.1rc1
	 * [new tag]         1.6.2rc1   -> 1.6.2rc1
	 * [new tag]         1.6.3rc1   -> 1.6.3rc1
	 * [new tag]         1.6.4rc1   -> 1.6.4rc1
	 * [new tag]         1.6b1      -> 1.6b1
	 * [new tag]         1.6rc1     -> 1.6rc1
	 * [new tag]         1.7.1rc1   -> 1.7.1rc1
	 * [new tag]         1.7.2b1    -> 1.7.2b1
	 * [new tag]         1.7.2rc1   -> 1.7.2rc1
	 * [new tag]         1.7b1      -> 1.7b1
	 * [new tag]         1.7b2      -> 1.7b2
	 * [new tag]         1.7rc1     -> 1.7rc1
	 * [new tag]         1.8b1      -> 1.8b1
	 * [new tag]         1.8b2      -> 1.8b2
	 * [new tag]         1.8rc1     -> 1.8rc1
	 * [new tag]         1.9.0b1    -> 1.9.0b1
	 * [new tag]         1.9.0rc1   -> 1.9.0rc1
	 * [new tag]         2.0.0-beta3 -> 2.0.0-beta3
	 * [new tag]         2.0.0b1    -> 2.0.0b1
	 * [new tag]         2.0.0b2    -> 2.0.0b2
	 * [new tag]         2.1.0-beta1 -> 2.1.0-beta1

可看到, 我們事實上可以在一個Git工作目錄中, 加入許多相關或不相關的遠端儲存庫, 這些複製回來的**完整儲存庫**, 都包含這些儲存庫中所有物件與變更歷史, 這些Git物件隨時都可以靈活運用。不過, 通常我們註冊多個遠端儲存庫的機會不多, 除非你想抓其他團隊成員的版本庫回來查看內容。

這些註冊進工作目錄的遠端儲存庫設定資訊, 都儲存在 `.git\config` 設定檔中, 其內容：

	[remote "origin"]
		url = https://github.com/eden90267/sandbox-empty.git
		fetch = +refs/heads/*:refs/remotes/origin/*
	[remote "jquery"]
		url = https://github.com/jquery/jquery.git
		fetch = +refs/heads/*:refs/remotes/jquery/*
		
這個 `[remote "origin"]` 區段的設定, 包含遠端儲存庫的代表名稱 `origin`, 還有兩個重要參數, 分別是 `url` 與 `fetch`, 所代表意義是：「遠端儲存庫URL位址在 `https://github.com/eden90267/sandbox-empty.git`, 然後 fetch 所指定的則是一個**參照名稱對應規格**(refspec)」。

## 何謂參照名稱對應規格 ##

	+refs/heads/*:refs/remotes/origin/*

格式概略分4塊：

- `+`

	設定 `+` 加號, 代表傳輸資料時, 不會特別使用安全性確認機制
	
- `refs/heads/*`

	「來源參照規格」, 代表一個位於**遠端儲存庫**的**遠端分支**, 而 `*` 星號代表 `refs/heads/` 這個路徑下[所有的遠端參照]。
	
- `:`

	用來區分「來源分支」與「目的分支」
	
- `refs/remotes/origin/*`

	「目的參照規格」, 代表一個位於**本地儲存庫**的**本地追蹤分支**, 而 `*` 星號代表 `refs/remote/origin/` 這個路徑下[所有的本地參照]。
	
當定義好這份refspec對應規格後, 主要影響到的是 `git fetch` 與 `git push` 這兩個遠端儲存庫的操作。

`git fetch` 就是把遠端儲存庫的相關物件取回, 但要取得哪些遠端分支的物件？ 就是透過這份refspec的定義, 他才知道。當你執行 `git fetch` 或 `git fetch origin`的時候, 他會先透過URL連到遠端儲存庫, 然後找出「來源參照規格」的那些遠端分支(`refs/heads/*`), 取回後放入「目的參照規格」的那些本地追蹤分支(`refs/remote/origin/*`)。

查詢遠端儲存庫有哪些分支, 可執行 `git ls-remote` 或 `git ls-remote origin` 即可列出：

	ryuutekiMacBook-Pro:sandbox-empty2 eden90267$ git ls-remote
	From https://github.com/eden90267/sandbox-empty.git
	a809a3578360928ad3760e525d02889186dc2588		HEAD
	a809a3578360928ad3760e525d02889186dc2588		refs/heads/master
	
如果把fetch的refspec修改成以下這樣, 那麼除 `master` 以外的遠端分支, 就不會被下載：

	fetch = +refs/heads/master:refs/remotes/origin/master
	
如果想明確下載特定幾個分支, 可重複定義好幾個不同的 fetch 參照規格(refspec):

	[remote "origin"]
		url = https://github.com/eden90267/sandbox-empty.git
		fetch = +refs/heads/master:refs/remotes/origin/master
		fetch = +refs/heads/TestBranch:refs/remotes/origin/TestBranch
		
另外, 在我們透過 `git remote add [URL]` 建立遠端儲存庫設定時, 並沒有 `push` 參照規格, default如下：

	push = +refs/heads/*:refs/heads/*
	
所代表意思, 當執行 `git push`, Git指令會參考這份 `push` 的參照規格, 讓你將本地儲存庫在 `refs/heads/*` 底下的所有分支與標籤, 全部推送到相對應遠端儲存庫的 `refs/heads/*` 參照名稱下。

最後, 無論執行 `git push` 或 `git fetch`, 在不特別加參數的情況, Git預設就是用 `origin` 當成遠端儲存庫, 並使用 `origin` 的參照規格。

## 本地分支與遠端儲存庫之間的關係 ##

我們已經知道, 一個工作目錄下的本地儲存庫, 可能定義有多個遠端儲存庫。所以當想將 **非 `master` 分支** 透過 git push 推送到遠端, Git可能不知道你到底想推送到哪裡, 所以要另外定義本地分支與遠端儲存庫之間的關係。

例：建立一個 `FixForCRLF` 本地分支, 直接透過 `git push` 無法推送成功, 必須輸入完整的 `git push origin FixForCRLF` 指令才能將本地分支推上去, 原因在於你並沒有設定「本地分支」與「遠端儲存庫」之間的預設對應。

若將**本地分支**建立起跟**遠端儲存庫**的對應關係, 只要在 `git push` 的時候加上 `--set-upstream` 參數, 即可將本地分支註冊進 `.git\config` 設定檔, 之後再用 `git push` 就可以順利自動推上去。

執行 `git push --set-upstream origin FixForCRLF` 同時, 會在 .git\config 設定檔增加以下內容：

	[branch "FixForCRLF"]
		remote = origin
		merge = refs/heads/FixForCRLF
		
可從設定檔的格式發現, 有兩個屬性 `remote` 與 `merge`, 所代表意義是：「當想要本地的 `FixForCRLF` 分支推送到遠端儲存庫時, 預設的遠端儲存庫為 `origin` 這個, 然後推送的時候要將本次變更合併到 `refs/heads/FixForCRLF` 這個遠端分支裡。」

一開始執行git clone, Git預設就會幫我們建立好 `master` 分支的對應關係, 所以針對master 分支操作, 不需額外加上 --set-upstream就能使用。其分支定義內容：

	[branch "master"]
		remote = origin
		merge = refs/heads/master
		
## 今日小結 ##

refspec參照規格, 這一段學會之後才有機會設定更加符合自己或團隊需要的設定, 但建議還是不要修改預設值, 以免把大家搞糊塗了。

- git remote -v
- git branch -r
- git branch -a
- git branch
- git push
- git ls-remote

---

# Day 26: 多人在同一個遠端儲存庫中進行版控 #

一個人用版本控管, 只能當作是原始碼歷史備份工具, 在大多情況下, 版本控管機制都是設計給多人共同使用的, 尤其Git這套分散式版本控管系統, 更是設計給成千上萬人都能順利使用的版本控管工具。不過在多人使用的情況下, 通常多多少少也會帶來一些副作用, 多跟少的問題。在Git版控中, 多人同時間進行版控的策略有好幾種, 今回介紹大家共用一個遠端儲存庫的使用方式與問題解決方法。

## 建立多人使用的遠端儲存庫與工作目錄 ##

	ryuutekiMacBook-Pro:~ eden90267$ cd Github/
	ryuutekiMacBook-Pro:Github eden90267$ mkdir myproject.git
	ryuutekiMacBook-Pro:Github eden90267$ cd myproject.git/
	ryuutekiMacBook-Pro:myproject.git eden90267$ git init --bare
	Initialized empty Git repository in /Users/eden90267/Github/myproject.git/
	
假設兩個開發人員準備開發一個新專案 myproject, 分別是 User1 與 User2 這兩位。
	
	ryuutekiMacBook-Pro:Github eden90267$ git clone /Users/eden90267/Github/myproject.git User1WD
	Cloning into 'User1WD'...
	warning: You appear to have cloned an empty repository.
	done.
	ryuutekiMacBook-Pro:Github eden90267$ cd User1WD/
	ryuutekiMacBook-Pro:User1WD eden90267$ echo a > a.txt
	ryuutekiMacBook-Pro:User1WD eden90267$ git add .
	ryuutekiMacBook-Pro:User1WD eden90267$ git commit -m "Add a.txt"
	[master (root-commit) 5f98743] Add a.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:User1WD eden90267$ git push origin master
	Counting objects: 3, done.
	Writing objects: 100% (3/3), 203 bytes | 0 bytes/s, done.
	Total 3 (delta 0), reused 0 (delta 0)
	To /Users/eden90267/Github/myproject.git
	 * [new branch]      master -> master

	ryuutekiMacBook-Pro:Github eden90267$ git clone /Users/eden90267/Github/	myproject.git User2WD
	Cloning into 'User2WD'...
	done.
	ryuutekiMacBook-Pro:Github eden90267$ cd User2WD/
	
現在已準備好一個「多人」使用的版控環境, 並共用一個遠端儲存庫。

## 遠端儲存庫的基本開發流程 ##

User1先聲奪人, 搶先建立版本也推送遠端儲存庫：

	ryuutekiMacBook-Pro:Github eden90267$ cd User1WD/
	ryuutekiMacBook-Pro:User1WD eden90267$ echo b > b.txt
	ryuutekiMacBook-Pro:User1WD eden90267$ git add .
	ryuutekiMacBook-Pro:User1WD eden90267$ git commit -m "Add b.txt"
	[master 0165668] Add b.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	ryuutekiMacBook-Pro:User1WD eden90267$ git push origin master
	Counting objects: 3, done.
	Delta compression using up to 8 threads.
	Compressing objects: 100% (2/2), done.
	Writing objects: 100% (3/3), 259 bytes | 0 bytes/s, done.
	Total 3 (delta 0), reused 0 (delta 0)
	To /Users/eden90267/Github/myproject.git
	   5f98743..0165668  master -> master

這時User2的工作目錄有兩個分支, 一個是本地的 master 分支, 另一個是 origin/master 本地追蹤分支。但User2現在的origin/master並沒有得到遠端儲存庫的最新版, 而且User2並不知道User1已經將他手邊的版本推送到遠端儲存庫了, 所以還是繼續自己的開發作業, 也在他自己的工作目錄中建立一個版本。但準備將版本推送到遠端儲存庫時, 發現他的推送作業被遠端儲存庫拒絕了！原因在於存在遠端儲存庫的初始版本後, 已經擁有一個新版本, 他不允許另一個人建立一個多重的版本歷史, 所以拒絕你將本地版本推送上去。

	ryuutekiMacBook-Pro:User1WD eden90267$ cd ..
	ryuutekiMacBook-Pro:Github eden90267$ cd User2WD/
	ryuutekiMacBook-Pro:User2WD eden90267$ echo c > c.txt
	ryuutekiMacBook-Pro:User2WD eden90267$ git add .
	ryuutekiMacBook-Pro:User2WD eden90267$ git commit -m "Add c.txt"
	[master 6555c0d] Add c.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 c.txt
	ryuutekiMacBook-Pro:User2WD eden90267$ git push origin master
	To /Users/eden90267/Github/myproject.git
	 ! [rejected]        master -> master (fetch first)
	error: failed to push some refs to '/Users/eden90267/Github/myproject.git'
	hint: Updates were rejected because the remote contains work that you do
	hint: not have locally. This is usually caused by another repository pushing
	hint: to the same ref. You may want to first integrate the remote changes
	hint: (e.g., 'git pull ...') before pushing again.
	hint: See the 'Note about fast-forwards' in 'git push --help' for details.
	
遇到這問題不用緊張, Git很擅長處理這種狀況。User2現在要做的事, 就是先把遠端儲存庫中的新物件取回, 如下指令：

	ryuutekiMacBook-Pro:User2WD eden90267$ git fetch
	remote: Counting objects: 3, done.
	remote: Compressing objects: 100% (2/2), done.
	remote: Total 3 (delta 0), reused 0 (delta 0)
	Unpacking objects: 100% (3/3), done.
	From /Users/eden90267/Github/myproject
	   5f98743..0165668  master     -> origin/master
	   
這時可看到版本移動了, 代表已經成功改變 `origin/master` 的參照位置到最新的 Add b.txt 這個版本。

現在要做的事把 `origin/master` 版本的變更「合併」回自己的 master 本地分支：

	ryuutekiMacBook-Pro:User2WD eden90267$ git merge origin/master
	Merge made by the 'recursive' strategy.
	 b.txt | 1 +
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	 
這樣就可以將遠端儲存庫中 master 遠端分支的所有版本套用到自己的 master 分支上, 也代表現在可嘗試把本地修改過的變更版本推送到遠端儲存庫了

	ryuutekiMacBook-Pro:User2WD eden90267$ git push origin master
	Counting objects: 5, done.
	Delta compression using up to 8 threads.
	Compressing objects: 100% (4/4), done.
	Writing objects: 100% (5/5), 556 bytes | 0 bytes/s, done.
	Total 5 (delta 0), reused 0 (delta 0)
	To /Users/eden90267/Github/myproject.git
	   0165668..ac45cc4  master -> master
	   
下次又遇到類似的狀況, 可改用 `git pull` 指令幫我們一次做到 `git fetch` 與 `git merge origin/master` 這個動作, 這動作相對會簡單許多。

就這樣不斷周而復始, 完成多人協同作業的步驟。

## 今日小結 ##

無法避免的, 執行 git merge origin/master 或 git pull 的過程中, 還是很可能會出現合併衝突的現象, 遇到這種情形還是必須手動處理並協調解決衝突, 但這已經是多人使用 Git 版本控管中最簡單的使用方式。

如果今天發生了衝突狀況, 而又不知該如何解決, 因版本尚未被成功合併, 所以可執行以下指令「重置」到目前的 `HEAD` 版本：

	git reset --hard HEAD
	
如果成功合併, 但又想反悔這次的合併動作, 還是可執行以下指令「重置」到合併前的版本狀態：

	git reset --hard ORIG_HEAD
	
整理下本日學到的Git指令與參數：

- git init --bare
- git clone [repo_url][dir]
- git add .
- git commit -m "message"
- git push origin master
- git fetch
- git merge orgin/master
- git pull
- git reset --hard HEAD
- git reset --hard ORIG_HEAD

---

# Day 27: 透過分支在同一個遠端儲存庫中進行版控 #

Git遠端儲存庫多人協作的的情況下, 應善加利用分支將不同用途的原始碼分別進行版本管理。

## 建立多人使用的遠端儲存庫與工作目錄 ##

	ryuutekiMacBook-Pro:~ eden90267$ cd Github/
	ryuutekiMacBook-Pro:Github eden90267$ git clone git@github.com:eden90267/sandbox-multi-branch.git
	Cloning into 'sandbox-multi-branch'...
	Warning: Permanently added the RSA host key for IP address '192.30.252.122' to the list of known hosts.
	remote: Counting objects: 4, done.
	remote: Compressing objects: 100% (3/3), done.
	remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
	Receiving objects: 100% (4/4), done.
	Checking connectivity... done.
	ryuutekiMacBook-Pro:Github eden90267$ cd sandbox-multi-branch/
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git log
	commit 94607c11df02ca1d827e71927e18d6a2371bc65e
	Author: eden90267 <eden90267@gmail.com>
	Date:   Wed Apr 20 23:29:34 2016 +0800

	    Initial commit
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git branch -a
	* master
	  remotes/origin/HEAD -> origin/master
	  remotes/origin/master
	  
此時我們的 `.git\config` 內容如下：

	[core]
		repositoryformatversion = 0
		filemode = true
		bare = false
		logallrefupdates = true
		ignorecase = true
		precomposeunicode = true
	[remote "origin"]
		url = git@github.com:eden90267/sandbox-multi-branch.git
		fetch = +refs/heads/*:refs/remotes/origin/*
	[branch "master"]
		remote = origin
		merge = refs/heads/master

## 常見的分支名稱 ##

`master`: 目前系統的「穩定版本」, 必須乾淨且高品質的原始碼版本。會要求所有人都不要用這個分支建立任何版本, 真要建立版本, 一定會透過「合併」的方式來進行操作, 以確保版本能夠更容易被追蹤。

進入開發階段時, 通常會再從 `master` 分支建立起另一個 `develop` 分支, 用來作為「開發分支」, 也就是所有人都會在這個分支上進行開發, 但這時候或許會產生一些衝突的情形, 因大家都在同分支上進行版本控管。

	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git branch
	* master
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git checkout -b develop
	Switched to a new branch 'develop'
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git branch
	* develop
	  master
	  
開發過程中可能因為需求變更, 被指派開發些新功能,可能新功能變動性大, 或小功能測試用途, 不想因為開發這些功能而影響到大家的開發作業, 所以這時會選擇再建立起一個「新功能分支」, 專門用來存放新增功能的程式碼版本。這測試用的「功能分支」, 通常建立在develop之上。這分支命名, 實務上通常會取名為 `feature/[branch_name]` , 例：`feature/aspent_identity`。

	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git branch
	* develop
	  master
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git checkout -b feature/aspnet_identity
	Switched to a new branch 'feature/aspnet_identity'
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git branch
	  develop
	* feature/aspnet_identity
	  master
	  
如果發現開發過程中, 「正式機」(prod env)出現嚴重錯誤, 但在「開發分支」裡又包含一些尚未完成的功能, 這時你可能會從 master 分支緊急建立一個「修正分支」, 通常的命名為 `hotfix/[branch_name]`, 例：`hotfix/bugs_in_membership`。

	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git branch
	  develop
	* feature/aspnet_identity
	  master
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git checkout master
	Switched to branch 'master'
	Your branch is up-to-date with 'origin/master'.
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git branch
	  develop
	  feature/aspnet_identity
	* master
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git checkout -b hotfix/	bugs_in_membership
	Switched to a new branch 'hotfix/bugs_in_membership'
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git branch
	  develop
	  feature/aspnet_identity
	* hotfix/bugs_in_membership
	  master
	  
如果你發現目前的 `master` 分支趨於穩定版本, 那麼你可能會想替目前的 `master` 分支建立起一個「標籤物件」或稱「標示物件」(annotated tag), 那麼可先切換到 `master` 分支後輸入 `git tag 1.0.0-beta1 -a -m "V1.0.0-beta1 created"` 即可建立一個名為 `1.0.0-beta` 的標示標籤, 並透過 `-m` 賦予標籤一個說明訊息。

	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git branch
	  develop
	  feature/aspnet_identity
	* hotfix/bugs_in_membership
	  master
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git checkout master
	Switched to branch 'master'
	Your branch is up-to-date with 'origin/master'.
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git tag 1.0.0-beta1 -a -m "V1.0.0-beta1 created"
	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git tag
	1.0.0-beta1
	
## 將本地分支送上儲存庫 ##

目前為止我們已經建立好幾個分支與標籤, 用SourceTree來看, 目前還看不出分支的版本線圖, 畢竟我們還沒建立任何版本, 但該有的分支已經被成功建立。

不過, 這些分支都僅儲存在本地儲存庫中, 團隊中所有其他人都無法得到你建立的這些分支, 如果要將這些分支的參照名稱推送到遠端儲存庫, 可以使用 `git push --all`

	yuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git push --all
	Total 0 (delta 0), reused 0 (delta 0)
	To git@github.com:eden90267/sandbox-multi-branch.git
	 * [new branch]      develop -> develop
	 * [new branch]      feature/aspnet_identity -> feature/aspnet_identity
	 * [new branch]      hotfix/bugs_in_membership -> hotfix/bugs_in_membership

不過只下達 `--all` 參數是不夠的, 可能還要加上 `--tags` 參數, 才能將標示標籤也一併送到遠端儲存庫。

	ryuutekiMacBook-Pro:sandbox-multi-branch eden90267$ git push --tags
	Counting objects: 1, done.
	Writing objects: 100% (1/1), 162 bytes | 0 bytes/s, done.
	Total 1 (delta 0), reused 0 (delta 0)
	To git@github.com:eden90267/sandbox-multi-branch.git
	 * [new tag]         1.0.0-beta1 -> 1.0.0-beta1

## 請團隊成員下載遠端儲存庫所有物件 ##

這時大家就能夠透過 `git fetch --all --tags` 將所有物件取回, 包含所有物件參照與標籤參照。

	ryuutekiMacBook-Pro:Github eden90267$ git clone git@github.com:eden90267/	sandbox-multi-branch.git sandbox-multi-branch-user2
	Cloning into 'sandbox-multi-branch-user2'...
	remote: Counting objects: 5, done.
	remote: Compressing objects: 100% (4/4), done.
	remote: Total 5 (delta 0), reused 1 (delta 0), pack-reused 0
	Receiving objects: 100% (5/5), done.
	Checking connectivity... done.
	ryuutekiMacBook-Pro:Github eden90267$ cd sandbox-multi-branch-user2/
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git fetch --all --tags
	Fetching origin

## 開始各自進行不同的分支開發 ##

User2 的本地分支只有 `master` 而已, 跟原本建立的那個工作目錄有些不一樣。之前【Day 25: 使用Github遠端儲存庫 - 觀念篇】文章中不是提到說「**把這些「本地追蹤分支」視為是一種「唯讀」的分支**」嗎？沒有本地分支要怎樣進行？

Git早幫我們想好了, 可直接執行 `git checkout hotfix/bugs_in_membership` 將這個「本地追蹤分支」給取出來(checkout)。

	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git branch -a
	* master
	  remotes/origin/HEAD -> origin/master
	  remotes/origin/develop
	  remotes/origin/feature/aspnet_identity
	  remotes/origin/hotfix/bugs_in_membership
	  remotes/origin/master
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git checkout hotfix/bugs_in_membership
	Branch hotfix/bugs_in_membership set up to track remote branch hotfix/bugs_in_membership from origin.
	Switched to a new branch 'hotfix/bugs_in_membership'
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git branch -a
	* hotfix/bugs_in_membership
	  master
	  remotes/origin/HEAD -> origin/master
	  remotes/origin/develop
	  remotes/origin/feature/aspnet_identity
	  remotes/origin/hotfix/bugs_in_membership
	  remotes/origin/master
	  
在取出 `hotfix/bugs_in_membership` 這個「本地追蹤分支」後, Git會自動幫你建立起一個同名的「本地分支」, 所以你根本不用擔心有沒有本地分支的情形。

	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git status
	On branch hotfix/bugs_in_membership
	Your branch is up-to-date with 'origin/hotfix/bugs_in_membership'.
	nothing to commit, working directory clean
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ echo %date% %time% > a.txt
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git add .
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git commit -m "Add a.txt"
	[hotfix/bugs_in_membership e17e6b0] Add a.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git push origin hotfix/bugs_in_membership
	Counting objects: 3, done.
	Delta compression using up to 8 threads.
	Compressing objects: 100% (2/2), done.
	Writing objects: 100% (3/3), 311 bytes | 0 bytes/s, done.
	Total 3 (delta 0), reused 0 (delta 0)
	To git@github.com:eden90267/sandbox-multi-branch.git
	   94607c1..e17e6b0  hotfix/bugs_in_membership -> hotfix/bugs_in_membership
	   
目前為止, 推送出去的, 只有 `hotfix/bugs_in_membership` 這個分支的版本而已, 並沒有將變更「合併」回 `master` 分支。這樣操作代表意思是, 你將變更放上遠端儲存庫, 目的是為了將變更可讓其人看到, 也可取回繼續修改, 就跟【Day 26：多人在同一個遠端儲存庫中進行版控】的版控流程一樣。

想合併回去 `master`：

	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git branch -a
	* hotfix/bugs_in_membership
	  master
	  remotes/origin/HEAD -> origin/master
	  remotes/origin/develop
	  remotes/origin/feature/aspnet_identity
	  remotes/origin/hotfix/bugs_in_membership
	  remotes/origin/master
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git checkout master
	Switched to branch 'master'
	Your branch is up-to-date with 'origin/master'.
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git merge hotfix/	bugs_in_membership
	Updating 94607c1..e17e6b0
	Fast-forward
	 a.txt | 1 +
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:sandbox-multi-branch-user2 eden90267$ git push
	Total 0 (delta 0), reused 0 (delta 0)
	To git@github.com:eden90267/sandbox-multi-branch.git
	   94607c1..e17e6b0  master -> master
	   
## 今日小結 ##

- git push --all --tags
- git fetch --all --tags
- git branch -a
- git checkout hotfix/bugs_in_membership
- git push origin hotfix/bugs_in_membership

---

# Day 28: 了解 GitHub 的 fork 與 pull request 版控流程 #

GitHub採用forks與pull request的流程, 讓你可做到基本的權限控管。

## 設定 GitHub 專案的權限控管 - 個人帳號 ##

在GitHub個人帳戶下, 並沒什麼權限控管的機制, 所以只要你授權特定人為協同開發人員(Collaborators), 他就能自由Push與Pull專案原始碼。

由於你沒辦法設定更細的Git遠端儲存庫權限, 所以只要被指派的人, 就能存取完整的Git遠端儲存庫, 大家都能任意分支進行push、pull或刪除分支等動作, 要是團隊有天兵, 就會是災難。

## 設定 GitHub 專案的權限控管 - 組織帳號 ##

在GitHub的組織帳戶下, 就可設定人員群組(Teams), 你就可在群組上設定更細的權限:

- Pull Only
- Push & Pull
- Push, Pull & Administrative

## 使用Fork功能 ##

英文Fork字面翻譯是「叉子」的意思, 好像你用叉子去把食物叉起來, 直接把菜挪放到你自己的盤子, 所謂「菜」就是你要複製的GitHub專案, 而「盤子」就是你的GitHub帳號。

ex: [https://github.com/doggy8088/DataDictionaryCreator](https://github.com/doggy8088/DataDictionaryCreator), Fork button

該專案已經在你自己的帳號下, 所以此時你已經可以把該專案當成「自己的遠端儲存庫」在用, 完全沒有讀寫權限的問題。

因為Git是個分散式版本控管系統, 只要你有fetch權限, 基本上就可以抓到該版本庫的完整變更歷程。

## 使用 Fork 過的 Git 遠端儲存庫 ##

版控方面, 使用上幾乎跟自己的Git遠端儲存庫沒什麼兩樣, 且你也有完整的歷史紀錄。請記得這份資料是[https://github.com/doggy8088/DataDictionaryCreator](https://github.com/doggy8088/DataDictionaryCreator)複製過來就好。

	C:\Users\eden_liu\Documents\GitHub> cd .\DataDictionaryCreator
	C:\Users\eden_liu\Documents\GitHub\DataDictionaryCreator [master]> echo TEST > test.md
	C:\Users\eden_liu\Documents\GitHub\DataDictionaryCreator [master +1 ~0 -0 !]> git add .
	C:\Users\eden_liu\Documents\GitHub\DataDictionaryCreator [master +1 ~0 -0]> git
	commit -m "Add a test.md for test purpose"
	[master deff343] Add a test.md for test purpose
	 1 file changed, 0 insertions(+), 0 deletions(-)
	 create mode 100644 test.md
	C:\Users\eden_liu\Documents\GitHub\DataDictionaryCreator [master]> git push
	warning: push.default is unset; its implicit value has changed in
	Git 2.0 from 'matching' to 'simple'. To squelch this message and maintain the traditional behavior, use:

	  git config --global push.default matching

	To squelch this message and adopt the new behavior now, use:

	  git config --global push.default simple

	When push.default is set to 'matching', git will push local branches to the remote branches that already exist with the same name.

	Since Git 2.0, Git defaults to the more conservative 'simple' behavior, which only pushes the current branch to the corresponding remote branch that 'git pull' uses to update the current branch.

	See 'git help config' and search for 'push.default' for further information.
	(the 'simple' mode was introduced in Git 1.7.11. Use the similar mode 'current' instead of 'simple' if you sometimes use older versions of Git)

	Warning: Permanently added the RSA host key for IP address '192.30.252.131' to the list of known hosts.
	Counting objects: 3, done.
	Delta compression using up to 8 threads.
	Compressing objects: 100% (2/2), done.
	Writing objects: 100% (3/3), 293 bytes | 0 bytes/s, done.
	Total 3 (delta 1), reused 0 (delta 0)
	To git@github.com:eden90267/DataDictionaryCreator.git
	   c29aaab..deff343  master -> master

## 使用 pull request 將變更合併回當初的 GitHub 專案 ##

透過 `eden90267` , 有建立新的commit, push並推回GitHub。 現在要把儲存在 `eden90267` 帳號下的專案「合併」回 `doggy8088` 帳號下的 `DataDictionaryCreator` 專案, 這時因為是跨帳號的, 所以必須利用 pull request 才能把變更「合併」回去。

註: 這裡pull request照字面翻譯是「拉取要求」的意思, 代表要以 `eden90267` 的身分, 請 `doggy8088` 把我的變更給拉回去(`git pull`), 但你不能強迫對方拉(`pull`), 所以必須拜託(`request`)對方拉, 所以才叫pull request。

要用 `eden90267` 的身分, 連到 `https://github.com/doggy8088/DataDictionaryCreator` 這頁, 然後點選 **Pull Requests** 頁籤。

選擇兩個版本(兩個 commit 物件), GitHub才能建立patch檔案, 也才能知道要合併哪些東西回去。但你選不到自己fork過的版本, 因此你要點選**compare across forks**。

然後就可以選擇到自己fork過的專案與分支了!不過, 這一步要特別注意不要選錯, 你的版本較新, 所以應該要把右邊的版本選擇你的, GitHub才知道從 `doggy8088/DataDictionaryCreator` 的 `master` 分支, 到 `eden90267/DataDictionaryCreator` 的 `master` 分支, 到底發生哪些版本變化。

最後會看到有哪些檔案以及那些地方變更了, 然後就可按下 **Click to create a pull request for this comparison** 建立起一個 pull request:

最後, 先看一下右上角有個**Able to merge**的地方, 會預先告訴你合併結果, 顯示你的版本跟目前 `doggy8088:master` 的版本是否有衝突發生。如果都沒有, 再輸入些說明文字給原作者( `doggy8088` ), 並按下 **Send pull request** 即可完成。

**註**: Github for Mac;Github for Windows 已增加pull request功能。

## 接受 **pull request** 的要求, 確認合併回自己的 **GitHub** 專案 ##

最後一個步驟, 是讓原作者(`doggy8088`)去看有誰傳送了一個pull request給自己。

按下**Merge pull request**即可完成合併工作。

## 今日小結 ##

應該大致上能了解fork與pull request的存在, 最主要就是「權限」以及「版本庫隔離」的需求。一個上千人的專案(Linux Kernel), 如果所有人都能存取主要的遠端儲存庫, 那不是很恐怖?!

不過在一般企業, 你不一定要這樣做, 畢竟操作步驟確實繁瑣了些。實際運用就靠自己判斷了。

---

# Day 29: 如何將 Subversion 專案匯入到 Git 儲存庫 #

轉換簡單四步驟：

## 準備使用者清單對應檔 ##

## 將 SVN 專案取出並轉換成 Git 工作目錄 ##

## 轉換 SVN 的忽略清單(即 svn:ignore 屬性) ##

## 將專案推送到遠端儲存庫 ##

---

# Day 30: 分享工作中幾個好用的 Git 操作技巧 #

## 如何讓 git pull / push / fetch 不用輸入帳號、密碼 ##

原使用**HTTPS**通訊協定取得Git儲存庫。但事實上可改用**SSH**選擇當成取得Git遠端儲存庫的網址。

使用HTTP通訊協定來存取GitHub上的遠端儲存庫, 由於無法記憶帳號密碼, 所以每次執行遠端儲存庫的指令都一定會被要求輸入帳號、密碼。但改用SSH的話, 就可透過預先定義好的金鑰來進行身份驗證。

第一次使用Git for Windows會自動上傳GitHub的**SSH Key**專區。因為github_rsa、github_rsa.pub這一對金鑰的存在。所以在Git Shell底下操作Git指令, 所有針對遠端儲存庫的操作, 都不需要再次輸入帳密了。

因GitHub for Windows工具已把SSH金鑰上傳到GitHub網站了。

如果希望「命令提示字元」下也可以不用輸入帳號密碼認證, 可將 `%USERPROFILE%\.ssh` 資料夾下的兩個檔案分別把 `github_rsa` 複製一份改名成 `id_rsa`, 然後把 `github_rsa.pub` 複製一份 `id_rsa.pub`, 即可完成設定！

可用 `git remote set-url origin git@github.com:doggy8088/frontend-tools.git` 將現有遠端 URL 改成 SSH 的通訊協定。

如此一來, 就不用再需要驗證帳號密碼了。

## 如何讓操作 Bitbucket 遠端儲存庫時, 也可以不用輸入帳號、密碼 ##

github_rsa.pub value copy to Bitbucket SSH Setting.

## 如何還原一個 `git rebase` 變動 ##

`git merge` 還原：`git reset --hard ORIG_HEAD`, 即可還原本次合併的變更。但 `git rebase` 怎辦？因為` git rebase` 會將版本線圖改變, 而且分支結構也會發生變化, 感覺難作, 但其實非常簡單。

	ryuutekiMacBook-Pro:Github eden90267$ git clone git@github.com:eden90267/CSS-Guidelines.git
	Cloning into 'CSS-Guidelines'...
	Warning: Permanently added the RSA host key for IP address '192.30.252.130' to the list of known hosts.
	remote: Counting objects: 692, done.
	remote: Total 692 (delta 0), reused 0 (delta 0), pack-reused 692
	Receiving objects: 100% (692/692), 341.68 KiB | 103.00 KiB/s, done.
	Resolving deltas: 100% (362/362), done.
	Checking connectivity... done.
	ryuutekiMacBook-Pro:Github eden90267$ cd CSS-Guidelines/
	ryuutekiMacBook-Pro:CSS-Guidelines eden90267$ git reflog
	d972a71 HEAD@{0}: clone: from git@github.com:eden90267/CSS-Guidelines.git
	ryuutekiMacBook-Pro:CSS-Guidelines eden90267$ git log --oneline -5
	d972a71 Point to new home
	a69b734 Merge pull request #29 from dahyun-yoon/patch-2
	be20df6 Update README.md
	4d8a623 Merge branch 'master' of github.com:csswizardry/CSS-Guidelines
	8c6b709 Custom domain
	ryuutekiMacBook-Pro:CSS-Guidelines eden90267$ git rebase -i 8c6b709
	Successfully rebased and updated refs/heads/master.
	ryuutekiMacBook-Pro:CSS-Guidelines eden90267$ git reflog
	65f0761 HEAD@{0}: rebase -i (finish): returning to refs/heads/master
	65f0761 HEAD@{1}: rebase -i (pick): Point to new home
	4b212f3 HEAD@{2}: rebase -i (pick): Update README.md
	d9d5f1f HEAD@{3}: rebase -i (pick): Add german translation
	0b5a3e3 HEAD@{4}: rebase -i (pick): Add space before opening bracket, too.
	41e2fd6 HEAD@{5}: rebase -i (pick): Add spaces between property: value; in CSS
	8c6b709 HEAD@{6}: rebase -i (start): checkout 8c6b709
	d972a71 HEAD@{7}: clone: from git@github.com:eden90267/CSS-Guidelines.git
	ryuutekiMacBook-Pro:CSS-Guidelines eden90267$ git reset --hard HEAD@{7}
	HEAD is now at d972a71 Point to new home
	ryuutekiMacBook-Pro:CSS-Guidelines eden90267$ git reflog
	d972a71 HEAD@{0}: reset: moving to HEAD@{7}
	65f0761 HEAD@{1}: rebase -i (finish): returning to refs/heads/master
	65f0761 HEAD@{2}: rebase -i (pick): Point to new home
	4b212f3 HEAD@{3}: rebase -i (pick): Update README.md
	d9d5f1f HEAD@{4}: rebase -i (pick): Add german translation
	0b5a3e3 HEAD@{5}: rebase -i (pick): Add space before opening bracket, too.
	41e2fd6 HEAD@{6}: rebase -i (pick): Add spaces between property: value; in CSS
	8c6b709 HEAD@{7}: rebase -i (start): checkout 8c6b709
	d972a71 HEAD@{8}: clone: from git@github.com:eden90267/CSS-Guidelines.git
	
## 取得遠端儲存庫的統計資訊 ##

`git clone`, 抓回完整的版本紀錄, 這代表我們可隨時在本地儲存庫統計版本資訊。

例如想取得版本庫中所有人的 commit 次數統計：`git shortlog -sne` 即可列出每個人的commit次數, 而且加上 `-n` 參數還可按照 commit 數量進行降幂排序：

	ryuutekiMacBook-Pro:CSS-Guidelines eden90267$ git shortlog -sne
	    68  Harry Roberts <harry@csswizardry.com>
	     2  Matteo Nicoletti <matteo.nicoletti@webdev.it>
	     2  Matt Bainton <matt@inlikealion.com>
	     1  dahyun-yoon <bongy12357@naver.com>
	     1  Jack Rugile <jack@jackrugile.com>
	     1  Mike <mike@mikepennisi.com>
	     1  Tim Vandecasteele <tim.vandecasteele@gmail.com>
	     1  Marian Friedmann <marian.friedmann@gmail.com>
	     
也可利用 `git shortlog` 顯示出每個人最近 commit 過的歷史紀錄。

	ryuutekiMacBook-Pro:CSS-Guidelines eden90267$ git shortlog
	Harry Roberts (68):
	      Iitial commit
	      Adding readme and other guideline text
	      Update URL to BSkyB
	      Fix typo in README
	      Update doc URL
	      Update README intro
	      Update README intro
	      Embolden sharing details
	      Update copyright date
	      Add closing sentence to preprocessor section.
	      Tidy up link to CSS perf article.
	      Correct syntax for conditional comment.
	      Fix typo
	      Add note about syntax rules.
	      Use more robust URL in tweet link.
	      Use more robust URL in tweet link
	      Use more robust URL in tweet link
	      Merge pull request #3 from jugglinmike/typo
	      Add section on key selectors
	      Add section on key selectors
	      Remove Twitter link
	      Reformat and add BEM section
	      Complete rewrite
	:
	
也可利用GitHub上**Graph**頁面提供的統計資訊：

這裡主要提供六種報表：

- Contributors(本專案貢獻者的活躍程度)
- Commit Activiti(每一天commit活動數量)
- Code Frequency(原始碼異動服務, 可看出每天新增與刪除的程式碼行數)
- Punchcard(每一天的commit數量, 用打卡方式顯示)
- Network(pull request 與 merge 圖形表示)
- Members(所有 fork 此專案的 github 成員)

## 從工作目錄清除不在版本庫的中的檔案 ##

Git工作目錄, 通常會產生些不必要的檔案, 這些檔案通常都會透過 `.gitignore` 檔案, 讓Git自動忽略這些檔案。想將這些額外的檔案都給刪掉, 可透過 `git clean -f` 強迫刪除這些檔案。

不過, 執行前, 還是建議用 `git clean -n` 看一下, 這個指令會列出他「預計」會刪除哪些檔案, 等你確認後再執行 `git clean -f` 即可刪除檔案。

## 刪除遠端分支 ##

如果你將「本地分支」透過 `git push origin FixForCRLF` 指令建立起「遠端分支」, 也代表會在遠端被建立一個名為 `FixForCRLF` 的分支, 想本地下指令刪除遠端追蹤分支(在遠端分支前面加個 `:` 而已)：

	git push origin :FixForCRLF
	
另一個同樣目的的指令, 看起來較直覺：

	git push origin --delete FixForCRLF

※ 使用要注意, 如果有人下載過這個遠端分支的話, 他就再也無法透過 `git push` 把變更推送上來了

## 找出改壞程式的兇手 ##

替原始碼做版控, 最有效就是每個檔案每一行都可以進行詳細追蹤, 今天如果程式發生異常, 且要找到哪一行, 就可透過 `git blame` 指令, 幫你找出真正改壞的兇手, 並去看他為什麼改壞。

	git blame [filename]

	git blame -L [開始行數],[結束行數] [filename]

ex:

	git blame README.md
	
	git blame -L 8, 16 ViewModels/MemberViewModel.cs
	
	git blame -L 4, Models/Product.cs
	
實例測試：

	ryuutekiMacBook-Pro:CSS-Guidelines eden90267$ git blame README.md
	54b7932e (Harry Roberts 2012-10-06 08:26:34 +0100 1) # General CSS notes, advice and guidelines
	ece70ccd (Harry Roberts 2012-03-10 10:53:40 +0000 2)
	3bdcff28 (Harry Roberts 2012-10-26 10:03:27 +0200 3) ---
	3bdcff28 (Harry Roberts 2012-10-26 10:03:27 +0200 4)
	d972a71a (Harry Roberts 2014-08-18 16:35:13 +0100 5) **The guidelines have moved: [cssguidelin.es](http://cssguidelin.es/)**
	
## 今日小結 ##

一些小技巧, 需要的時候就會用到。