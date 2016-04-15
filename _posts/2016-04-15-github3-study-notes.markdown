---
layout: post
title:  "GitHub Study Notes(Day 22)"
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