---
layout: post
title:  "GitHub Study Notes(Day 20)"
date:   2016-03-17 17:32:00 +0800
categories: [git, github]
---

# Day 11: 認識Git物件的一般參照與符號參照 #

認識Git的「絕對名稱」後, 接下來是介紹Git版控過程中最常用到的「參照名稱」。

## 認識物件的參照名稱 ##

參照名稱(ref), 簡單說就是Git物件的一個「指標」, 或是相對於「絕對名稱」的另一個「好記名稱」, 用一個預先定義或你自行定義的名稱來代表某一個Git物件。

「分支名稱」、「HEAD(代表最新版本)」, 以及之後學到的「標籤名稱」, 這些都是「參照名稱」, 總之就是為了好記而已。

以「分支名稱」為例, 說明一下「參照名稱」的實體結構為何。以下為例, `git branch`取得所有分支名稱, 可看到有三個分支, 其三個分支名稱就是一個「參照名稱」, 這代表三個「參照名稱」分別對應到Git物件儲存庫中的三個commit物件。

以下範例可看到這些參照名稱其實件是一個檔案而已, 所有「本地分支」的參照名稱皆位於`.get\refs\heads`目錄下:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git branch
  master
* newbranch1
  newbranch2
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> dir .git\refs\h
eads


    目錄: C:\Users\eden_liu\Documents\GitHub\git-branch-demo\.git\refs\heads


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---       2016/3/15  下午 04:55         41 master
-a---       2016/3/15  下午 05:22         41 newbranch1
-a---       2016/3/15  下午 04:58         41 newbranch2

~~~

以下範例, 分支的「參照名稱」會指向分支的「最新版」, 打開`.git\refs\heads\newbranch1`檔案內容, 可看出這是一個純文字檔而已, 而且指向版本紀錄中的「最新版」。再以`git cat-file -p 65f0`取得該commit物件的內容, 以及用`git show --text 65f0`取得該版本的變更紀錄, 藉此證明這些檔案就是「參照名稱」的主要用途。

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git branch
  master
* newbranch1
  newbranch2
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git log --prett
y=oneline
65f026a35a9e62bb9855cb81b5de265f37b0956c Add b.txt in newbranch1
e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff a.txt: set 1 as content
d7b3014a945337c214cdbcd0b768be74fa55532e Initial commit
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> type .git\refs\
heads\newbranch1
65f026a35a9e62bb9855cb81b5de265f37b0956c
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 65f0
tree dd395ea8ce2ef870e0d451e07e397dce8bf5bd1a
parent e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
author Eden Liu <eden90267@atlassian.com> 1458033730 +0800
committer Eden Liu <eden90267@atlassian.com> 1458033730 +0800

Add b.txt in newbranch1
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git show --text
 65f0
commit 65f026a35a9e62bb9855cb81b5de265f37b0956c
Author: Eden Liu <eden90267@atlassian.com>
Date:   Tue Mar 15 17:22:10 2016 +0800

    Add b.txt in newbranch1

diff --git a/b.txt b/b.txt
new file mode 100644
index 0000000..fb655ae
--- /dev/null
+++ b/b.txt
@@ -0,0 +1,2 @@
+<FF><FE>n^@e^@w^@b^@r^@a^@n^@c^@h^@1^@^M^@
+^@
\ No newline at end of file

~~~

再透過指令看看使用「絕對名稱」與「參照名稱」讀取特定commit物件的內容, 證明兩個版本執行結果為相同:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> type .git\refs\
heads\newbranch1
65f026a35a9e62bb9855cb81b5de265f37b0956c
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 newbranch1
tree dd395ea8ce2ef870e0d451e07e397dce8bf5bd1a
parent e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
author Eden Liu <eden90267@atlassian.com> 1458033730 +0800
committer Eden Liu <eden90267@atlassian.com> 1458033730 +0800

Add b.txt in newbranch1
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 65f0
tree dd395ea8ce2ef870e0d451e07e397dce8bf5bd1a
parent e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
author Eden Liu <eden90267@atlassian.com> 1458033730 +0800
committer Eden Liu <eden90267@atlassian.com> 1458033730 +0800

Add b.txt in newbranch1

~~~


## 關於 .git/refs/ 目錄 ##

上述範例其實已經看出, 所有「參照名稱」都是個檔案, 而且一律放在`git/refs/`目錄下。Git的參照名稱所放置的目錄位置, 主要有三個:

- 本地分支: `.git/refs/heads/`
- 遠端分支: `.git/refs/remotes/`
- 標籤: `.git/refs/tags/`

再舉個simple, 建立一個分支名稱`f2e`, 其本地分支的「參照名稱」會建立`.git/refs/heads/f2e`檔案:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git branch f2e
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> dir .git\refs\h
eads


    目錄: C:\Users\eden_liu\Documents\GitHub\git-branch-demo\.git\refs\heads


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---       2016/3/21  下午 04:01         41 f2e
-a---       2016/3/15  下午 04:55         41 master
-a---       2016/3/15  下午 05:22         41 newbranch1
-a---       2016/3/15  下午 04:58         41 newbranch2

~~~

事實上`f2e`全名為`refs/heads/f2e`才對, 只是Git允許你輸入簡寫, 方便快速輸入參照名稱而已。如下三種「參照名稱」的方式都是可行的:

- `git cat-file -p 65f0`
- `git cat-file -p refs/heads/f2e`
- `git cat-file -p heads/f2e`
- `git cat-file -p f2e`

當輸入「參照名稱」的「簡稱」, 預設Git會依照以下順序搜尋適當的參照名稱, 只要找到對應檔案, 就會立刻傳回檔案內容的「物件絕對名稱」:

- .git/<參照簡稱>
- .git/refs/<參照簡稱>
- .git/refs/tags/<參照簡稱;標籤名稱>
- .git/refs/heads/<參照簡稱;本地分支名稱>

	-->**e.g. 指令: `git cat-file -p f2e`, 到這邊就找到了, 以下就不繼續搜尋**

- .git/refs/remotes/<參照簡稱>
- .git/refs/remotes/<參照簡稱;遠端分支名稱>/HEAD

## 認識物件的符號參照名稱(symref) ##

符號參照名稱(symref)其實也是參照名稱(ref)的一種, 只是內容不同而已。如下例可看出其差異, 「符號參照」會指向另一個「參照名稱」, 並且內容以`ref:`開頭:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> type .git\HEAD
ref: refs/heads/newbranch1
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> type .git\refs\
heads\newbranch1
65f026a35a9e62bb9855cb81b5de265f37b0956c
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 65f0
tree dd395ea8ce2ef870e0d451e07e397dce8bf5bd1a
parent e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
author Eden Liu <eden90267@atlassian.com> 1458033730 +0800
committer Eden Liu <eden90267@atlassian.com> 1458033730 +0800

Add b.txt in newbranch1

~~~

在Git工具中, 預設會維護一些特別的符號參照, 方便我們快速取得常用的commit物件, 且這些物件預設都會儲存在`.git/`目錄下。這些符號參考有四個:

- HEAD
  - 永遠會指向「工作目錄」中所設定的「分支」當中的「最新版」。
  - 所以當在這分支執行`git commit`後, 這個`HEAD`符號參照也會更新成該分支最新版的那個commit物件。
- ORIG_HEAD
  - 簡單說為HEAD這個commit物件的「前一版」, 經常用來復原上一次的版本變更。
- FETCH_HEAD
  - 使用遠端儲存庫時, 可能會使用`git fetch`指令取回所有遠端儲存庫的物件。這個FETCH_HEAD符號參考則會記錄遠端儲存庫中每個分支的HEAD(最新版)的「絕對名稱」。
- MERGE_HEAD
  - 當你執行合併工作, 「合併來源」的commit物件絕對名稱會被記錄在MERGE_HEAD這個符號參照中。

## 一般參照與符號參照的使用方式 ##

我們知道「參照名稱」有特殊目的, 通常用於「本地分支」、「遠端分支」與「標籤」等情境下, 但事實上可建立任意數量的「自訂參照名稱」, 只要透過`git update-ref`就可自由建立「一般參照」。

企圖建立一個名為`InitialCommit`的一般參照, 並指向Git儲存庫中的第一個版本:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> dir .git\Initia
lCommit
Get-ChildItem : 找不到 'C:\Users\eden_liu\Documents\GitHub\git-branch-demo\.git
\InitialCommit' 路徑，因為它不存在。
位於 行:1 字元:4
+ dir <<<<  .git\InitialCommit
    + CategoryInfo          : ObjectNotFound: (C:\Users\eden_l...t\InitialComm
   it:String) [Get-ChildItem], ItemNotFoundException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.GetCh
   ildItemCommand

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git log
commit 65f026a35a9e62bb9855cb81b5de265f37b0956c
Author: Eden Liu <eden90267@atlassian.com>
Date:   Tue Mar 15 17:22:10 2016 +0800

    Add b.txt in newbranch1

commit e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
Author: Eden Liu <eden90267@atlassian.com>
Date:   Tue Mar 15 16:36:03 2016 +0800

    a.txt: set 1 as content

commit d7b3014a945337c214cdbcd0b768be74fa55532e
Author: Eden Liu <eden90267@atlassian.com>
Date:   Tue Mar 15 16:35:02 2016 +0800

    Initial commit
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 d7b3
tree 7f33f889afd9890916a8321dacf563a28190c065
author Eden Liu <eden90267@atlassian.com> 1458030902 +0800
committer Eden Liu <eden90267@atlassian.com> 1458030902 +0800

Initial commit
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git update-ref
InitialCommit d7b3
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 InitialCommit
tree 7f33f889afd9890916a8321dacf563a28190c065
author Eden Liu <eden90267@atlassian.com> 1458030902 +0800
committer Eden Liu <eden90267@atlassian.com> 1458030902 +0800

Initial commit

~~~

建立完成後, 預設會放在`.git`資料夾下, 且此時用「絕對名稱」與「參照名稱」都能存取特定Git物件的內容

**註**:請記得, 參照名稱可以指向任意Git物件, 並沒有限定非要commit物件不可。

若要建立較為正式的參照名稱, 最好加上`refs/`開頭, ex: `git update-ref refs/InitialCommit [object_id]`。

若要移除一般參照, 可使用 `-d` 選項:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git update-ref
usage: git update-ref [<options>] -d <refname> [<old-val>]
   or: git update-ref [<options>]    <refname> <new-val> [<old-val>]
   or: git update-ref [<options>] --stdin [-z]

    -m <reason>           reason of the update
    -d                    delete the reference
    --no-deref            update <refname> not the one it points to
    -z                    stdin has NUL-terminated arguments
    --stdin               read updates from stdin
    --create-reflog       create a reflog

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git update-ref
-d InitialCommit
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 InitialCommit
fatal: Not a valid object name InitialCommit

~~~

顯示所有參照的方式, 則可使用`git show-ref`指令:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git show-ref
65f026a35a9e62bb9855cb81b5de265f37b0956c refs/heads/f2e
39b9da78e13bb545893b4b9406df2a6c69bb85d2 refs/heads/master
65f026a35a9e62bb9855cb81b5de265f37b0956c refs/heads/newbranch1
254922812bf705a9b1eef8f781d56d645ea9f6ff refs/heads/newbranch2
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git update-ref
refs/InitialCommit d7b3
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git show-ref
d7b3014a945337c214cdbcd0b768be74fa55532e refs/InitialCommit
65f026a35a9e62bb9855cb81b5de265f37b0956c refs/heads/f2e
39b9da78e13bb545893b4b9406df2a6c69bb85d2 refs/heads/master
65f026a35a9e62bb9855cb81b5de265f37b0956c refs/heads/newbranch1
254922812bf705a9b1eef8f781d56d645ea9f6ff refs/heads/newbranch2

~~~

建立與刪除「符號參照」, 可參考下圖`git symbolic-ref`用法, 理解後, 就會發現Git對於參照的用法非常靈活且強大。還有, 執行`git show-ref`只會顯示在`.git\refs`目錄的那些參照, 而且不管是「一般參照」或「符號參照」都一樣。

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> dir .git\refs


    目錄: C:\Users\eden_liu\Documents\GitHub\git-branch-demo\.git\refs


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----       2016/3/21  下午 04:01            heads
d----       2016/3/15  下午 04:32            tags
-a---       2016/3/22  下午 05:11         41 InitialCommit

~~~

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git branch
  f2e
  master
* newbranch1
  newbranch2
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 f2e
tree dd395ea8ce2ef870e0d451e07e397dce8bf5bd1a
parent e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
author Eden Liu <eden90267@atlassian.com> 1458033730 +0800
committer Eden Liu <eden90267@atlassian.com> 1458033730 +0800

Add b.txt in newbranch1
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 refs/heads/f2e
tree dd395ea8ce2ef870e0d451e07e397dce8bf5bd1a
parent e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
author Eden Liu <eden90267@atlassian.com> 1458033730 +0800
committer Eden Liu <eden90267@atlassian.com> 1458033730 +0800

Add b.txt in newbranch1
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git symbolic-re
f f2e_init refs/heads/f2e
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> type .git\f2e_i
nit
ref: refs/heads/f2e
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git symbolic-re
f refs/f2e_init2 refs/heads/f2e
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> type .git\refs\
f2e_init2
ref: refs/heads/f2e
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git show-ref
d7b3014a945337c214cdbcd0b768be74fa55532e refs/InitialCommit
65f026a35a9e62bb9855cb81b5de265f37b0956c refs/f2e_init2
65f026a35a9e62bb9855cb81b5de265f37b0956c refs/heads/f2e
39b9da78e13bb545893b4b9406df2a6c69bb85d2 refs/heads/master
65f026a35a9e62bb9855cb81b5de265f37b0956c refs/heads/newbranch1
254922812bf705a9b1eef8f781d56d645ea9f6ff refs/heads/newbranch2

~~~

可建立參照到以下目錄:

- `.git/<參照簡稱>`
- `.git/refs/<參照簡稱>`
- `.git/refs/tags/<參照簡稱;標籤名稱>`
- `.git/refs/heads/<參照簡稱;本地分支名稱>`
- `.git/refs/remotes/<參照簡稱>`
- `.git/refs/remotes/<參照簡稱;遠端分支名稱>/HEAD`

自訂參照通常會自行建立在前兩個路徑下, 以免分不清跟我們使用「本地分支」、「遠端分支」與「標籤」的使用方式。不過, 你的確可以利用`git update-ref`建立任何自訂參照, 若把自訂參照建立在`refs/heads/Will`的話, 也等同於建立一個名為`Will`的分支, 而Git真的就是那麼單純, 當你越來越了解Git的內部結構, 也會更加喜歡上Git的各種特性。

## 今日小結 ##

「參照名稱(ref)」簡單說就是Git物件的一個「指標」, 用來指向特定Git物件, 所以可以把「參照名稱」想像成Git物件絕對名稱的別名(Alias), 用來幫助記憶。在Git裡, 有許多機制可以幫你控管專案, 例如「分支」、「標籤」等等, 這些機制骨子裡就是靠「參照」完成的。

Git參照名稱又有區分「一般參照」與「符號參照」, 兩者用途一模一樣, 只在內容不太一樣。「符號參照」會指向另一個「一般參照」, 而「一般參照」則是指向一個Git物件的「絕對名稱」。

本日學到的Git指令與參數:

- git branch
- git log --pretty=oneline
- git log --oneline
- git cat-file -p [ref or object_id]
- git show [ref or object_id]
- git update-ref
- git symbolic-ref
- git show-ref

---

# Day 12: 認識Git物件的相對名稱 #

認識了Git物件的「絕對名稱」與「參照名稱」後, 最後來介紹Git版控過程中也很常見到的「相對名稱」。

再複習一次, 在Git版控過程中, 每一個版本代表一個commit物件, 每個commit物件會有一個「絕對名稱」, 該名稱是將內容以SHA1雜湊運算後的一個40字元的字串, 你可以用前4~40個字元來當成該commit物件的識別名稱。版控的過程中, 也可以讓你透過「參照名稱」來代表某一個commit物件, 每個「參照名稱」最終會對應到一個物件的「絕對名稱」。而「參照物件」又區分「一般參照」與「絕對參照」, 其中「一般參照」會直接對應到「絕對名稱」, 而「符號參照」則是對應到另一個「一般參照」。

這篇文章所要介紹則是透過「相對名稱」的表示法, 讓你找到特定commit物件後, 用相對位置來找到其他的「commit物件」。

## 相對名稱的表示法 ##

使用相對名稱其實非常簡單, 這裡有兩個特殊符號必須記得, 一個是 `^` 另一個是 `~` 符號。

如果要找 `HEAD` 的前一個版本, 會使用 `HEAD~` 或 `HEAD~1` 來表示「HEAD這個commit物件的前一版」。**註:**這裡應該很清楚 `HEAD` 是一個Git內建的「符號參照名稱」, 代表目前分支的最新版。

如果要找出 `f2e` 分支的前兩個版本(不含 `f2e` 的 `HEAD` 版本), 則可用 `f2e~2` 或用 `f2e~~`來表示, 這就是最基本的表示方法。

在沒有分支與合併的儲存庫, 關於 `^1` 與 `~1` 所表達的意思是相同的, 都代表「前一版」。但事實上在有分支與合併的儲存庫中, 他們有不同的意義。

這就是最基本的「相對名稱」表示法。

## 關於commit物件彼此間的連結 ##

比較常見的Git儲存庫, 預設只會有一個「根commit物件」, 也就是我們最一開始建立的那個版本, 又稱「初始送交」(Initial Commit)。你也至少要有第一個commit物件後才能開始分支, 所以我們可說: 「在一個Git儲存庫中, 所有的commit物件中, 除第一個commit物件外, 任何其他的commit物件都一定會有一個以上的上層commit物件(parent commit)」。為甚麼可能有「一個以上」的上層commit物件呢?因為你很有可能會合併兩個以上的分支到另一個分支裡,　所以合併完成後的那個commit物件就會有多個parent commit物件。

簡單例子證明, 用 `git cat-file -p [object_id]` 來取得最前面兩筆commit物件的內容, 藉此了解每個commit物件確實一定會有parent屬性, 並指向上層commit物件的絕對名稱, 唯獨第一筆commit物件不會有parent屬性。

~~~ java

C:\Users\eden_liu\Documents\GitHub> cd .\git-branch-demo
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git log
commit 65f026a35a9e62bb9855cb81b5de265f37b0956c
Author: Eden Liu <eden90267@atlassian.com>
Date:   Tue Mar 15 17:22:10 2016 +0800

    Add b.txt in newbranch1

commit e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
Author: Eden Liu <eden90267@atlassian.com>
Date:   Tue Mar 15 16:36:03 2016 +0800

    a.txt: set 1 as content

commit d7b3014a945337c214cdbcd0b768be74fa55532e
Author: Eden Liu <eden90267@atlassian.com>
Date:   Tue Mar 15 16:35:02 2016 +0800

    Initial commit
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 d7b3
tree 7f33f889afd9890916a8321dacf563a28190c065
author Eden Liu <eden90267@atlassian.com> 1458030902 +0800
committer Eden Liu <eden90267@atlassian.com> 1458030902 +0800

Initial commit
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
 e93d
tree 512629789217105b3c6e6a0bfd3be3ee0db3bea1
parent d7b3014a945337c214cdbcd0b768be74fa55532e
author Eden Liu <eden90267@atlassian.com> 1458030963 +0800
committer Eden Liu <eden90267@atlassian.com> 1458030963 +0800

a.txt: set 1 as content

~~~

## 了解相對名稱表示法 `^` 與 `~` 的差異 ##

關於 `~` 的意義, 代表「第一個上層commit物件」的意思。

關於 `^` 代表的意思則是「擁有多個上層commit物件時, 要代表第幾個第一代的上層物件」。

如果有一個「參照名稱」為 C, 若要找到他的第一個上層 commit 物件, 你可以有以下表達方式

- C^
- C^1
- C~
- C~1

如果你要找到它的第二個上層commit物件(在沒有合併情況下), 可以有以下表達方式:

- C^^
- C^1^1
- C~2
- C~~
- C~1~1

但不能用 `C^2` 來表達「第二個上層commit物件」!原因是在沒有合併的情況下, 這個 `C` 只有一個上層物件而已, 你只能用 `C^2` 代表「上一層物件的第二個上層物件」。

以下範例, C這個commit物件有3個上層物件,這代表C物件是透過合併而被建立的, 那麼你要透過「相對名稱」找到每一個路徑, 就必須搭配組合 `^` 與 `~` 的使用技巧, 才能定位到每個你想開啟的版本。

~~~ java

                    C
                    |
     /              |              \
C^1或C^或C^1        C^2            C^3

C^1~1或C^~1或C~2    C^2~1          C^3~1

C^1~2或C^~2或C~3    C^2~2          C^3~2

C^1~3或C^~3或C~4    C^2~3          C^3~3

~~~


## 介紹 `git rev~parse` 指令 ##

在Git for Windows工具裡有個 `git rev-parse` 指令, 透過這個指令可以把任意「參考名稱」或「相對名稱」解析出「絕對名稱」, 雖這工具不是很常用到, 但做Git教學的時候還蠻實用的。

- git rev-parse master
- git rev-parse HEAD
- git rev-parse ORIG_HEAD
- git rev-parse HEAD^
- git rev-parse HEAD~5

## 今日小結 ##

當瞭解了「相對名稱」後, 在Git中表示commit物件的各種方式已經講解完畢, 應可更熟悉Git各版本之間的操作。

整理一下本日學到的Git指令與參數:

- git log
- git cat-file -p [object_id]
- git rev-parse

---

# Day 13: 暫存工作目錄與索引的變更狀態 #

情境是, 某系統開發寫到一半, 結果被老闆或客戶「插單」, 眼前的程式即將完成, 老闆的「急件」又不能拖, 一個未完成的軟體開發狀態外加緊急調整的需求, 這簡直是軟體品質的一大考驗。 如果有這種困擾, 那麼Git可漂亮幫你完成任務。

## 認識git stash指令 ##

我們知道使用Git版控時候, 有區分「工作目錄」與「索引」。工作目錄裡面會有你改到一半還沒改完的檔案(尚未加入索引), 也有新增檔案但還沒加入的檔案(尚未加入索引)。 而放在索引的資料, 則是你打算透過`git commit`建立版本(建立commit物件的內容)。

當功能開發到一半, 被緊急插單一定手忙腳亂, 尤其是手邊正改寫到一半的那些程式碼不知該如何是好。在Git裡有個`git stash`指令, 可以自動幫你把改寫到一半的那些檔案建立一個「特殊的版本」(也是一個commit物件), 我們稱這些版本為stash版本, 或你可以直接稱他為「暫存版」。

## 建立暫存版本 ##

我們手邊改到一半的檔案, 可能會有以下狀態:

- 新增檔案(尚未列入追蹤的檔案)(untracked file)
- 新增檔案(已經加入索引的檔案)(tracked/staged files)
- 修改檔案(尚未加入索引的檔案)(tracked/unstaged files)
- 修改檔案(已經加入索引的檔案)(tracked/staged files)
- 刪除檔案(尚未加入索引的檔案)(tracked/unstaged files)
- 刪除檔案(已經加入索引的檔案)(tracked/staged files)

若要將這些開發到一半的檔案建立一個「暫存版」, 你有兩個選擇:

- `git stash` 會將所有已列入追蹤(tracked)的檔案建立暫存版
- `git stash -u` 會包括所有已追蹤或未追蹤的檔案, 全部都建立成暫存版

**註**: `git stash` 也可以寫成 `git stash save`, 兩個指令的結果是一樣的, 只是`save`參數可以忽略不打而已。

看下簡單例子, 先透過指令快速建立一個擁有兩個版本的Git儲存庫與工作目錄:

~~~ java

C:\Users\eden_liu\Documents\GitHub> mkdir git-stash-demo


    目錄: C:\Users\eden_liu\Documents\GitHub


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----       2016/3/28  下午 04:18            git-stash-demo


C:\Users\eden_liu\Documents\GitHub> cd .\git-stash-demo
C:\Users\eden_liu\Documents\GitHub\git-stash-demo> git init
Initialized empty Git repository in C:/Users/eden_liu/Documents/GitHub/git-stash
-demo/.git/
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> echo . > a.txt
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~0 -0 !]> git add .

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~0 -0]> git commit
-m "Initial commit"
[master (root-commit) 5ec03a6] Initial commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 a.txt
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> echo 1 > a.txt
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +0 ~1 -0]> git add .
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +0 ~1 -0]> git commit
-m "a.txt: set 1 as content"
[master bd5d5e5] a.txt: set 1 as content
 1 file changed, 0 insertions(+), 0 deletions(-)

~~~

目前「工作紀錄」是乾淨的, 沒任何更新到一半的檔案:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git log
commit bd5d5e5fa295b69dd1a915f800fa71b3a2485777
Author: Eden Liu <eden90267@atlassian.com>
Date:   Mon Mar 28 16:49:25 2016 +0800

    a.txt: set 1 as content

commit 5ec03a6bb9fad92c5cfc82e5e28826600ffa01c8
Author: Eden Liu <eden90267@atlassian.com>
Date:   Mon Mar 28 16:19:25 2016 +0800

    Initial commit
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git status
On branch master
nothing to commit, working directory clean
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> dir


    目錄: C:\Users\eden_liu\Documents\GitHub\git-stash-demo


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---       2016/3/28  下午 04:19          8 a.txt

~~~

接著新增一個b.txt, 再將a.txt內容改成2, 如下:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> type a.txt
1
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> echo 2 > a.txt
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +0 ~1 -0]> type a.txt
2
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +0 ~1 -0]> echo TEST >
 b.txt
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> dir


    目錄: C:\Users\eden_liu\Documents\GitHub\git-stash-demo


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---       2016/3/29  上午 09:34          8 a.txt
-a---       2016/3/29  上午 09:35         14 b.txt


C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> type .\b.
txt
TEST
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> dir


    目錄: C:\Users\eden_liu\Documents\GitHub\git-stash-demo


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---       2016/3/29  上午 09:34          8 a.txt
-a---       2016/3/29  上午 09:35         14 b.txt


C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> git statu
s
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   a.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        b.txt

no changes added to commit (use "git add" and/or "git commit -a")

~~~

現在用`git status`得出我們有兩個檔案有變更, 一個是a.txt處於"not staged"狀態, 而b.txt則是"untracked"狀態。

這時, 利用`git stash -u`即可將目前這些變更儲存起來(包含untracked檔案), 儲存完畢後, 這些變更全部都會被重置, 新增的檔案會被刪除、修改的檔案會被還原、刪除的檔案會被加回去, 讓目前在工作目錄中所做的變更全部回復到HEAD狀態。這就是Stash幫我們做的事。

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> git stash
 -u
Saved working directory and index state WIP on master: bd5d5e5 a.txt: set 1 as c
ontent
HEAD is now at bd5d5e5 a.txt: set 1 as content
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git status
On branch master
nothing to commit, working directory clean

~~~

在建立完成「暫存版」之後, Git會順便幫我們建立一個暫存版的「參考名稱」, 而且是「一般參考」, 在`.git\refs\stash`儲存的是一個commit物件的「絕對名稱」。

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> dir .git/refs


    目錄: C:\Users\eden_liu\Documents\GitHub\git-stash-demo\.git\refs


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----       2016/3/29  上午 09:44            heads
d----       2016/3/28  下午 04:18            tags
-a---       2016/3/29  上午 09:44         41 stash


C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> dir .git\refs\stash


    目錄: C:\Users\eden_liu\Documents\GitHub\git-stash-demo\.git\refs


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---       2016/3/29  上午 09:44         41 stash

~~~

用 `git cat-file -p stash` 即可查出該物件的內容, 這時就可發現這其實就是一個有三個parent(上層commit物件)的commit物件:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git cat-file -p stas
h
tree 952a4c3106b2739b4bd504ae23d18365680b2d61
parent bd5d5e5fa295b69dd1a915f800fa71b3a2485777
parent 738db876f6e597e52da5a5a95b6c7e7c87fe78db
parent 4b43117bddc5a49d7ed17b22bd674fdbab827ae0
author Eden Liu <eden90267@atlassian.com> 1459215859 +0800
committer Eden Liu <eden90267@atlassian.com> 1459215859 +0800

WIP on master: bd5d5e5 a.txt: set 1 as content

~~~

有三個parent commit物件意義在於, 這個特殊的暫存版是從另外三個版本併進來的, 然而這三個版本內容, 一樣可以透過相同指令顯示其內容:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git cat-file -p stas
h
tree 952a4c3106b2739b4bd504ae23d18365680b2d61
parent bd5d5e5fa295b69dd1a915f800fa71b3a2485777
parent 738db876f6e597e52da5a5a95b6c7e7c87fe78db
parent 4b43117bddc5a49d7ed17b22bd674fdbab827ae0
author Eden Liu <eden90267@atlassian.com> 1459215859 +0800
committer Eden Liu <eden90267@atlassian.com> 1459215859 +0800

WIP on master: bd5d5e5 a.txt: set 1 as content
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git cat-file -p bd5d

tree 512629789217105b3c6e6a0bfd3be3ee0db3bea1
parent 5ec03a6bb9fad92c5cfc82e5e28826600ffa01c8
author Eden Liu <eden90267@atlassian.com> 1459154965 +0800
committer Eden Liu <eden90267@atlassian.com> 1459154965 +0800

a.txt: set 1 as content
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git cat-file -p 738d

tree 512629789217105b3c6e6a0bfd3be3ee0db3bea1
parent bd5d5e5fa295b69dd1a915f800fa71b3a2485777
author Eden Liu <eden90267@atlassian.com> 1459215857 +0800
committer Eden Liu <eden90267@atlassian.com> 1459215857 +0800

index on master: bd5d5e5 a.txt: set 1 as content
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git cat-file -p 4b43

tree 3e8b3fc8d5753297f904e9987e9f1cb77eb85b3d
author Eden Liu <eden90267@atlassian.com> 1459215858 +0800
committer Eden Liu <eden90267@atlassian.com> 1459215858 +0800

untracked files on master: bd5d5e5 a.txt: set 1 as content

~~~

上述執行結果應該可以從「訊息紀錄」的地方清楚看出這三個版本分別代表那些內容:

1. 原本工作目錄的HEAD版本
2. 原本工作目錄所包含的索引內容
3. 原本工作目錄裡所有未追蹤的內容

也就是說, 他把「原本工作目錄的HEAD版本」先建立兩個暫時的分支, 這兩個分支分別就是「原本工作目錄所包含的索引內容」與「原本工作目錄裡所有未追蹤的內容」之用, 並個別分支建立了一個版本以產生commit物件並且給予預設的log內容。最後把這三個分支, 合併到一個「參照名稱」為`stash`版本(這也是個commit物件)。還不僅如此, 他還把整個「工作內容」強迫重製為HEAD版本, 把這些變更與新增的檔案都給還原, 多的檔案也會被移除。

## 取回暫存版本 ##

由於「工作目錄」已經被重製, 所以變更儲存到stash這裡, 想將暫存檔案取回, 可透過git stash pop重新「合併」回來:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git status
On branch master
nothing to commit, working directory clean
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git stash pop
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   a.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        b.txt

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (4ed2a81277d65f79ab9c30fd3f4a5c68a528dc7c)
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]>

~~~

執行完畢後, 所有當初的工作目錄狀態與索引都會被還原。 事實上Git骨子裡是透過「合併」的功能把這個名為stash的版本給合併回目前分支而已。**最後, 他還會自動將這個stash分支給刪除**, 所以稱他為【暫存版】非常貼切!

## 建立多重暫存版 ##

Git的stash暫存版可以不只一份, 你也可以建立多份暫存檔, 以供後續使用。不過, 在正常的開發情境下, 通常不會有太多暫存檔才對, 會有這種情況發生, 主要有兩種可能:

1. 你的開發習慣太差, 導致累積一堆可能用不到的暫存版
2. 你的老闆或客戶「插單」的問題十分嚴重, 經常改到一版就被迫插單。

延續上個例子, 目前工作目錄的狀態應該是有兩個檔案有變化, 我們用`git status -s`取得工作目錄的狀態(其中-s代表顯示精簡版的狀態):

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> git statu
s -s
 M a.txt
?? b.txt

~~~

現在我們先建立第一個stash暫存版:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> git stash
 save -u
Saved working directory and index state WIP on master: bd5d5e5 a.txt: set 1 as c
ontent
HEAD is now at bd5d5e5 a.txt: set 1 as content

~~~

然後透過 `git stash list` 列出目前所有的 stash 清單, 目前僅一份暫存版:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git stash list
stash@{0}: WIP on master: bd5d5e5 a.txt: set 1 as content

~~~

而且可看到建立暫存版後, 工作目錄是乾淨的。此時我們在建立另一個 `new.txt` 檔案, 並再次建立暫存版:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git status -s
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> echo 1 > new.txt
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~0 -0 !]> git statu
s -s
?? new.txt
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~0 -0 !]> git stash
 save -u
Saved working directory and index state WIP on master: bd5d5e5 a.txt: set 1 as c
ontent
HEAD is now at bd5d5e5 a.txt: set 1 as content

~~~

再一次 `git stash list` 就可以看到目前有兩個版本:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git stash list
stash@{0}: WIP on master: bd5d5e5 a.txt: set 1 as content
stash@{1}: WIP on master: bd5d5e5 a.txt: set 1 as content

~~~

你應該會很納悶, 都沒有自訂的註解, 過幾天不就忘記這兩個暫存檔各自的修改項目嗎?沒錯, 所以你可自訂「暫存檔」的紀錄訊息。 透過 `git stash save -u <message>` 指令, 就可自訂暫存版的註解。

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git stash -h
usage: git stash list [<options>]
   or: git stash show [<stash>]
   or: git stash drop [-q|--quiet] [<stash>]
   or: git stash ( pop | apply ) [--index] [-q|--quiet] [<stash>]
   or: git stash branch <branchname> [<stash>]
   or: git stash [save [--patch] [-k|--[no-]keep-index] [-q|--quiet]
                       [-u|--include-untracked] [-a|--all] [<message>]]
   or: git stash clear
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git stash pop
Already up-to-date!
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        new.txt

nothing added to commit but untracked files present (use "git add" to track)
Dropped refs/stash@{0} (cc28e646acfd445f714003e41fcbf0d7ffcbea76)
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~0 -0 !]> git stash
 save -u "新增 new.txt 檔案"
Saved working directory and index state On master: 新增 new.txt 檔案
HEAD is now at bd5d5e5 a.txt: set 1 as content
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git stash list
stash@{0}: On master: 新增 new.txt 檔案
stash@{1}: WIP on master: bd5d5e5 a.txt: set 1 as content

~~~

這時, 如果你直接執行 `git stash pop` 的話, 他會取回最近的一筆暫存版, 也就是上述例子的 `stash@{0}` 這一項, 並把這一筆刪除。 另一種取回暫存版的方法是透過 `git stash apply` 指令, 唯一差別則是取回該版本(其實是執行合併動作)後, 該暫存版本還會留在stash清單上。

如果你想取回「特定一個暫存版」, 你就必須在最後指名stash id, 例如 `stash@{1}` 這樣的格式。例如如下範例, 我使用 `git stash apply "stash@{1}"` 取回前一個暫存版, 但保留這版stash清單裡:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git stash list
stash@{0}: On master: 新增 new.txt 檔案
stash@{1}: WIP on master: bd5d5e5 a.txt: set 1 as content
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master]> git stash apply "sta
sh@{1}"
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   a.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        b.txt

no changes added to commit (use "git add" and/or "git commit -a")
C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> git stash
 list
stash@{0}: On master: 新增 new.txt 檔案
stash@{1}: WIP on master: bd5d5e5 a.txt: set 1 as content

~~~

如果確定合併正確, 你想刪除 `stash@{1}` 的話, 可透過 `git stash drop "stash@{1}"` 將特定暫存版刪除。

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> git stash
 drop "stash@{1}"
Dropped stash@{1} (8c97e91af591a6a7b9d798768ae3c24f82575ca9)

~~~

如果想清理掉所有暫存版, 直接下達 `git stash clear` 即可全部刪除。

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-stash-demo [master +1 ~1 -0 !]> git stash
 clear

~~~

## 今日小結 ##

Git的stash(暫存版)機制真的非常非常的實用, 尤其是在IT業界插單嚴重的工作環境下, 這功能完全為我們量身打造, Subversion就沒像Git那麼簡單, 一個指令就可以把工作目錄與索引的狀態全部存起來。

stash的核心機制, 其實就是簡單的「分支」與「合併」而已, 由此可知, 整套Git版本控管機制, 大多是以「分支」與「合併」的架構在運作。

重新整理一下本日學到的Git指令與參數:

- git stash
- git stash -u
- git stash save
- git stash save -u
- git stash list
- git stash pop
- git stash apply
- git stash pop "stash@{id}"
- git stash apply "stash@{id}"
- git stash drop "stash@{id}"
- git stash clear

---

# Day 14: Git for Windows 選項設定 #

使用Git for Windows指令列工具絕對比透過GUI工具操作來的有效率, 原因就在於你可以把許多重複的版控工作透過指令自動化, 或將複雜的標準作業流程(SOP)轉化成簡單的指令操作。本章會分享幾則使用Git for Windows指令列工具的小技巧。

## 關於 Git 指令列工具的選項設定 ##

Git for Windows有許多「選項」可以設定, 可以微調指令執行的一些行為。 最常見的, 當然是首次使用Git for Windows的時候, 必須設定`user.name`與`user.email`選項, 沒這兩個選項設定, 你連執行`git commit`的權利都沒有。

列出目前設定在Git for Windows工具下的所有選項, `git config --list`:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git config --li
st
core.symlinks=false
core.autocrlf=true
color.diff=auto
color.status=auto
color.branch=auto
color.interactive=true
help.format=html
http.sslcainfo=C:/Program Files/Git/mingw64/ssl/certs/ca-bundle.crt
diff.astextplain.textconv=astextplain
rebase.autosquash=true
alias.c=commit
alias.co=checkout
alias.dt=difftool
alias.mt=mergetool
alias.praise=blame
alias.ff=merge --ff-only
alias.st=status
alias.sync=!git pull && git push
apply.whitespace=nowarn
core.symlinks=false
core.autocrlf=true
core.editor=gitpad
core.preloadindex=true
core.fscache=true
color.diff=auto
color.status=auto
color.branch=auto
color.interactive=true
color.ui=true
pack.packsizelimit=2g
help.format=html
http.sslcainfo=C:\Program Files (x86)\git\bin\curl-ca-bundle.crt
diff.astextplain.textconv=astextplain
rebase.autosquash=true
credential.helper=!github --credentials
filter.ghcleansmudge.clean=cat
filter.ghcleansmudge.smudge=cat
push.default=upstream
diff.tool=vs2013
diff.algorithm=histogram
difftool.prompt=false
difftool.bc4.cmd="c:/program files (x86)/beyond compare 3/bcomp.exe" "$LOCAL" "$
REMOTE"
difftool.p4.cmd="c:/program files/Perforce/p4merge.exe" "$LOCAL" "$REMOTE"
difftool.vs2012.cmd="c:/program files (x86)/microsoft visual studio 11.0/common7
/ide/devenv.exe" '//diff' "$LOCAL" "$REMOTE"
difftool.vs2013.cmd="c:/program files (x86)/microsoft visual studio 12.0/common7
/ide/devenv.exe" '//diff' "$LOCAL" "$REMOTE"
merge.tool=bc3
mergetool.prompt=false
mergetool.keepbackup=false
mergetool.bc3.cmd="c:/program files (x86)/beyond compare 3/bcomp.exe" "$LOCAL" "
$REMOTE" "$BASE" "$MERGED"
mergetool.bc3.trustexitcode=true
mergetool.p4.cmd="c:/program files/Perforce/p4merge.exe" "$BASE" "$LOCAL" "$REMO
TE" "$MERGED"
mergetool.p4.trustexitcode=false
user.name=Eden Liu
user.email=eden90267@atlassian.com
filter.lfs.clean=git-lfs clean %f
filter.lfs.smudge=git-lfs smudge %f
filter.lfs.required=true
diff.tool=vimdiff
alias.dt=difftool
core.repositoryformatversion=0
core.filemode=false
core.bare=false
core.logallrefupdates=true
core.symlinks=false
core.ignorecase=true
core.hidedotfiles=dotGitOnly

~~~

Linux, Mac與Windows設定, 除了儲存路徑比較不同, 其實指令都是相通。

想查詢所有可用的「選項」有哪些: `git help config`。

## 儲存 Git 選項設定的三個地方 ##

1. **系統層級**( `--system` )(System-level configuration)(**設定於整台電腦, 適用本機所有使用者的預設值**)

	如果要列出所有設定在「系統層級」的「選項」, 可執行 `git config --list --system` 命令, 也就是額外加上 `--system` 即可篩選出關於系統層級的選項設定。

		C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git config --list --system
		alias.c=commit
		alias.co=checkout
		alias.dt=difftool
		alias.mt=mergetool
		alias.praise=blame
		alias.ff=merge --ff-only
		alias.st=status
		alias.sync=!git pull && git push
		apply.whitespace=nowarn
    	[略]

	所有「系統層級」的選項設定預設會儲存在 `C:\Program (x86)\Git\etc\gitconfig` 這個檔案裡。Windows Vista以上版本又啟用「使用者帳戶控制(UAC)」, 路徑是 `%LOCALAPPDATA%\VirtualStore\Program Files (x86)\Git\etc\gitconfig`。會有這層改變出在UAC限制一般程式存取「系統資料夾」中的檔案權限, 導致無法使用 `git config` 寫入選項設定到這個檔案裡。在Windows Vista以上版本, 實作一套**VirtualStore**相容性技術, 讓你的程式試圖寫入檔案的同時, 可寫成功, 不過寫入的路徑卻是不同的, 這點要注意。

	系統管理員執行cmd:
	git config --system core.autocrlf = true
	用戶執行cmd:
	git config -- system core.autocrlf = 不會就是true, 「一般方式執行命令提示字元」與用「系統管理員身分執行命令提示字元」, 所寫入的「系統層級選項」是不能互通的。

	這代表著, 當你在「一般權限」下執行「命令提示字元」, 並透過 `git config --system` 設定「系統層級」選項, 那麼該設定會變成只有「自己」能套用而已, 不會套用到本機其他使用者身上。

2. **使用者層級**( `--global` )(User-level configuration)(**設定於目前登入的使用者**)

	列出「使用者層級」的「選項」: `git config --list --global`

		C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git config --list --global
		user.name=Eden Liu
		user.email=eden90267@atlassian.com
		filter.lfs.clean=git-lfs clean %f
		filter.lfs.smudge=git-lfs smudge %f
		filter.lfs.required=true
		diff.tool=vimdiff
		alias.dt=difftool

	「使用者層級」的選項設定預設會儲存在 `%USERPROFILE%\.gitconfig` 或 `C:\Users\<使用者帳號>\.gitconfig` 這個檔案裡。由於檔案在自己的使用者資料夾下, 沒有像「系統層級」設定時有權限問題。

	一般會把 `user.name` 與 `user.email` 設定在「使用者層級」:

		git config --global user.name "Eden_Liu"
		git config --global user.email "eden90267@gmail.com"

	「個人化」的環境設定都設定在這裡。

3. **儲存區層級**( `--local` )(Repository-level configuration)(**設定於工作目錄下的 `.git\config` 設定檔中**)

	列出「儲存區層級」的「選項」: `git config --list --local`

		C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git config --list --local
		core.repositoryformatversion=0
		core.filemode=false
		core.bare=false
		core.logallrefupdates=true
		core.symlinks=false
		core.ignorecase=true
		core.hidedotfiles=dotGitOnly

	儲存區層級的選項設定預設儲存在你Git工作目錄的 `.git\config` 設定檔中, 這檔案會在你建立本地儲存庫或透過 `git clone` 取得遠端儲存庫時自動建立, 若要定義「特定工作目錄」才要有的選項, 則直接將選項設定儲存在這裡。

## Git 選項設定的套用順序 ##

不同層級下可設定相同名稱的選項, 以下為Git指令列工具在執行時會依據以下優先進行套用:

1. 先套用系統層級(優先權最低)
2. 再套用使用者層級
3. 再套用儲存區層級(優先權最高)

如果Windows Vista以上並啟用UAC, 會多一個讀取與套用判斷:

1. 套用系統層級 `C:\Program Files (x86)\Git\etc\gitconfig`
2. 套用系統層級 `%LOCALAPPDATA%\VirtualStore\Program Files (x86)\Git\etc\gitconfig`
3. 套用使用者層級 `%USERPROFILE%\.gitconfig`
4. 套用儲存區層級 `.git\config`

若想知道目前工作環境最終套用的選項設定有哪些: `git config --list` , 取得「已設定」的選項設定清單:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git config --li
st
core.symlinks=false
core.autocrlf=true
color.diff=auto
color.status=auto
color.branch=auto
color.interactive=true
help.format=html
http.sslcainfo=C:/Program Files/Git/mingw64/ssl/certs/ca-bundle.crt
diff.astextplain.textconv=astextplain
rebase.autosquash=true
alias.c=commit
alias.co=checkout
alias.dt=difftool
alias.mt=mergetool
alias.praise=blame
alias.ff=merge --ff-only
alias.st=status
alias.sync=!git pull && git push
apply.whitespace=nowarn
core.symlinks=false
core.autocrlf=true
core.editor=gitpad
core.preloadindex=true
core.fscache=true
color.diff=auto
color.status=auto
color.branch=auto
color.interactive=true
color.ui=true
pack.packsizelimit=2g
help.format=html
http.sslcainfo=C:\Program Files (x86)\git\bin\curl-ca-bundle.crt
diff.astextplain.textconv=astextplain
rebase.autosquash=true
credential.helper=!github --credentials
filter.ghcleansmudge.clean=cat
filter.ghcleansmudge.smudge=cat
push.default=upstream
diff.tool=vs2013
diff.algorithm=histogram
difftool.prompt=false
difftool.bc4.cmd="c:/program files (x86)/beyond compare 3/bcomp.exe" "$LOCAL" "$
REMOTE"
difftool.p4.cmd="c:/program files/Perforce/p4merge.exe" "$LOCAL" "$REMOTE"
difftool.vs2012.cmd="c:/program files (x86)/microsoft visual studio 11.0/common7
/ide/devenv.exe" '//diff' "$LOCAL" "$REMOTE"
difftool.vs2013.cmd="c:/program files (x86)/microsoft visual studio 12.0/common7
/ide/devenv.exe" '//diff' "$LOCAL" "$REMOTE"
merge.tool=bc3
mergetool.prompt=false
mergetool.keepbackup=false
mergetool.bc3.cmd="c:/program files (x86)/beyond compare 3/bcomp.exe" "$LOCAL" "
$REMOTE" "$BASE" "$MERGED"
mergetool.bc3.trustexitcode=true
mergetool.p4.cmd="c:/program files/Perforce/p4merge.exe" "$BASE" "$LOCAL" "$REMO
TE" "$MERGED"
mergetool.p4.trustexitcode=false
user.name=Eden Liu
user.email=eden90267@gmail.com
filter.lfs.clean=git-lfs clean %f
filter.lfs.smudge=git-lfs smudge %f
filter.lfs.required=true
diff.tool=vimdiff
alias.dt=difftool
core.repositoryformatversion=0
core.filemode=false
core.bare=false
core.logallrefupdates=true
core.symlinks=false
core.ignorecase=true
core.hidedotfiles=dotGitOnly

~~~

## 選項設定的操作方式 ##

- 取得選項設定清單

	`git config --list`

	`git config --list [--system | --global | --local]`

- 取得特定選項值

	`git config [config_section.config_name]`

	`git config user.name`

	`git config user.email`

- 設定特定選項值

	`git config [config_section.config_name] [config_value]`

	`git config user.name "Eden Liu"`

	`git config user.email "eden90267@gmail.com"`

- 刪除特定的選項值

	`git config --unset [--system | --global | --local] [config_section.config_name]`

## 選項設定檔的內容結構 ##

PATH: C:\Users\eden_liu (**使用者層級**)

~~~ java

[user]
	name = Eden Liu
	email = eden90267@gmail.com
[filter "lfs"]
	clean = git-lfs clean %f
	smudge = git-lfs smudge %f
	required = true
[diff]
	tool = vimdiff
[alias]
	dt = difftool

~~~

`[`與`]`所包含的是「區段名稱」, 代表某一群設定檔的分類, 其他就是key/value的對應設定。

## 常用選項設定 ##

- 設定指令別名(Alias)

		git config --global alias.co   checkout
		git config --global alias.ci   commit
		git config --global alias.st   status
		git config --global alias.sts  "status -s"
		git config --global alias.br   branch
		git config --global alias.re   remote
		git config --global alias.di   diff
		git config --global alias.type "cat-file -t"
		git config --global alias.dump "cat-file -p"
		git config --global alias.lo   "log --oneline"
		git config --global alias.ll "log --pretty=format:'%h %ad | %s%d [%Cgreen%an%Creset]' --graph --date=short"
		git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset %ad |%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset [%Cgreen%an%Creset]' --abbrev-commit --date=short"

	`git st`, `git sts`, `git lo`, `git ll`或`git lg`就可完成

- 指定預設文字編輯器

	預設 Git for Windows 執行 `git commit` 的時候, 會開啟Vim編輯器, 切換成記事本:

	`git config --global core.editor notepad.exe`

	如果想指定Notepad++:

	`git config --global core.editor "\"C:\Program Files (x86)\Notepad++\notepad++.exe\""`

- 直接編輯設定檔

	想直接從指令列開啟編輯設定檔的話:

		git config --edit --system
		git config --edit --global
		git config --edit --local

	如此一來省去開啟檔案總管, 並找到路徑後再開啟檔案的繁瑣步驟。

- 自動辨識 CRLF 字元

	在Windows底下, 建議打開core.autocrlf選項, 讓Git將檔案儲存進物件儲存區(object storage), 可以自動過濾所有CR字元( `\r` ), 以利Git專案能更容易跨平台, 讓在Linux與Windows平台做開發的人都能順利使用Git版本控管。

	`git config --global core.autocrlf true`

- 自動訂正打錯的參數

	`git config --system color.ui. auto`

- 自訂commit訊息範本

	團隊中, 希望大家共用一個「文字範本」, 好讓大家在 `git commit` 的時候能填寫必要的欄位或資訊。

	如果要指令 `commit` 訊息範本, 必須先建立一個文字範本檔案, eg. G:\git-commit-template.txt

	接著透過以下指令, 設定commit訊息範本的路徑:

	`git config --local commit.template "G:\git-commit-template.txt"`

	git-commit-template.txt content:


		摘要:
		模組:
		議題:


	用 `--local` 參數, 原因是「訊息範本」有時候是跟著專案走的, 不同專案可能想套不同的訊息範本。

## 今日小結 ##

整理本日Git指令與參數:

- git config --list
- git config --list [--system | --global | --local]
- git config --edit [--system | --global | --local]
- git config [config_section.config_name]
- git config [config_section.config_name] "[config_value]"
- git config --unset [--system | --global | --local] [config_section.config_name]
- git config [--system | --global | --local] alias.[alias name] [命令]
- git config --local commit.template "[PATH]"

---

# Day 15: 標籤 - 標記版本控制過程中的重要事件 #

在使用Git版本控管的過程中, 會產生大量的版本, 隨寒暑易節、物換星移, 在這眾多版本中, 定有些值得紀錄的幾個重要版本, 這就是「標籤」(Tag)能幫作的事。

## 關於標籤(Tag)的基本概念 ##

基本上標籤的用途就是用來標記某一個「版本」或稱作「commit物件」, 以一個「好記的名稱」來幫助記憶【某個】版本。

Git標籤(Tag)擁有兩種型態:

- 輕量標籤(lightweight tag)
- 標示標籤(annotated tag)

【Day 11: 認識Git物件的一般參照與符號參照】有提到「一般參照」包含「標籤名稱」, 這就是所謂的「輕量標籤」。 「輕量標籤」可以說是某個commit版本的「別名」而已, 是一種「相對名稱」。

「標示標籤」則是一種Git物件, 【Day 6: 解析Git資料結構-物件結構】提到, Git包含4種物件類型, 分別是Blob, Tree, Commit與Tag物件, 這裡講的「標示標籤」就是Tag物件。Tag物件會儲存在Git的物件儲存區當中(`.git\objects\`目錄下), 並會關聯到另一個commit物件, 建立「標示標籤」時還能像建立commit物件時一樣包含「版本訊息」。 在內建的Git標籤機制中, 甚至還可利用GnuPG金鑰對Tag物件加以簽章, 以確保訊息的「不可否認性」。

這兩種標籤類型, 看來都像某個commit物件的「別名」, 但這兩種標籤在使用上只有些微的差異而已。大部分使用情境, 都會用「標示標籤」來建立「標籤物件」並給予「版本訊息」, 因為這種「標籤」才是Git中「永久的物件」。(儲存到物件儲存庫中的Git物件都是不變的, 只有索引是變動的)

## 了解輕量標籤(lightweight tag)的使用方式 ##

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> dir .git\refs\t
    ags
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git log -1
    commit 65f026a35a9e62bb9855cb81b5de265f37b0956c
    Author: Eden Liu <eden90267@atlassian.com>
    Date:   Tue Mar 15 17:22:10 2016 +0800

    Add b.txt in newbranch1
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git tag
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git tag 1.0.0-a
    lpha
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> dir .git\refs\t
    ags


    目錄: C:\Users\eden_liu\Documents\GitHub\git-branch-demo\.git\refs\tags


    ModeLastWriteTime Length Name
    ----------------- ------ ----
    -a---   2016/3/31  下午 03:37 41 1.0.0-alpha


    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> type .git\refs\
    tags\1.0.0-alpha
    65f026a35a9e62bb9855cb81b5de265f37b0956c

提示: 所有在 `.git\refs\` 下的檔案都是個「參考名稱」。

- 列出所有標籤: git tag
- 建立輕量標籤: git tag [tagname]
- 刪除輕量標籤: git tag [tagname] -d

想看「輕量標籤」的內容, 可以透過 `git cat-file -p [tagname]` 取得。想看其物件類型, 可透過 `git cat-file -t [tagname]` 取得。

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
     1.0.0-alpha
    tree dd395ea8ce2ef870e0d451e07e397dce8bf5bd1a
    parent e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
    author Eden Liu <eden90267@atlassian.com> 1458033730 +0800
    committer Eden Liu <eden90267@atlassian.com> 1458033730 +0800

    Add b.txt in newbranch1
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -t
     1.0.0-alpha
    commit

**請注意**: 輕量標籤不是個Git物件, 該Tag名稱取得的是commit物件的內容, 而且該名稱所查出的物件類型是commit物件。

## 了解標示標籤(annotated tag)的使用方式 ##

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git log -1
    commit 65f026a35a9e62bb9855cb81b5de265f37b0956c
    Author: Eden Liu <eden90267@atlassian.com>
    Date:   Tue Mar 15 17:22:10 2016 +0800

    Add b.txt in newbranch1
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git tag
    1.0.0-alpha
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git tag 1.0.0-b
    eta -a -m "Beta version"
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git tag
    1.0.0-alpha
    1.0.0-beta
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
     1.0.0-beta
    object 65f026a35a9e62bb9855cb81b5de265f37b0956c
    type commit
    tag 1.0.0-beta
    tagger Eden Liu <eden90267@gmail.com> 1459410468 +0800

    Beta version
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -t
     1.0.0-beta
    tag

與「輕量標籤」相異的地方:

1. 執行git tag, 所有標籤都會混在一起, 看不出標籤類型
2. 建立「標示標籤」要加上 -a 參數
3. 建立「標示標籤」一定要加上「版本訊息」, 跟執行 `git commit` 一樣都有 `-m` 參數可用。
4. `git cat-file -p 1.0.0-beta` 時, 可看出此物件內容跟commit物件稍有不同
5. `git cat-file -p 1.0.0-beta` 時, `type` 講的是上一行的 `object` 的物件類型, 這代表你也可以把任何Git物件建立成一個標籤物件。
6. `git cat-file -t 1.0.0-beta` 時, 得到的是tag物件類型。

標示標籤的指令用法, 跟輕量標籤一模一樣, 差別只有 `-a` 參數而已。(如果只有使用 `-m` 他會隱含 `-a` 也幫你加上)

※ 預設 `git tag [tagname] -a` 會將當前的 HEAD 版本建立成「標籤物件」, 如果要其他特定物件: `git tag [tagname] [object_id]`。

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git tag
    1.0.0-alpha
    1.0.0-beta
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git rev-parse 1
    .0.0-alpha
    65f026a35a9e62bb9855cb81b5de265f37b0956c
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git rev-parse 1
    .0.0-beta
    2dfc941fa00ee6624540270e51a9058141abbbed
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -t
     65f0
    commit
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -t
     2dfc
    tag
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
     2dfc
    object 65f026a35a9e62bb9855cb81b5de265f37b0956c
    type commit
    tag 1.0.0-beta
    tagger Eden Liu <eden90267@gmail.com> 1459410468 +0800

    Beta version
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git cat-file -p
     65f0
    tree dd395ea8ce2ef870e0d451e07e397dce8bf5bd1a
    parent e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
    author Eden Liu <eden90267@atlassian.com> 1458033730 +0800
    committer Eden Liu <eden90267@atlassian.com> 1458033730 +0800

    Add b.txt in newbranch1
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git lg
    git: 'lg' is not a git command. See 'git --help'.

    Did you mean this?
    log
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git config --gl
    obal alias.lg "log --graph --pretty=format:'%Cred%h%Creset %ad |%C(yellow)%d%Cre
    set %s %Cgreen(%cr)%Creset [%Cgreen%an%Creset]' --abbrev-commit --date=short"
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git lg
    * 65f026a 2016-03-15 | (HEAD -> newbranch1, tag: 1.0.0-beta, tag: 1.0.0-alpha, f
    2e, refs/f2e_init2) Add b.txt in newbranch1 (2 weeks ago) [Eden Liu]
    * e93d0dc 2016-03-15 | a.txt: set 1 as content (2 weeks ago) [Eden Liu]
    * d7b3014 2016-03-15 | (refs/InitialCommit) Initial commit (2 weeks ago) [Eden L
    iu]

重新整理本日學到的指令:

- git tag
- git tag [tagname]
- git tag [tagname] -a
- git tag [tagname] -a -m "<commit content>"
- git tag [tagname] -d

# Day 16: 善用版本日誌git reflog追蹤變更軌跡 #

學習Git版控的指令不難, 但弄清楚Git到底對我的儲存庫做了什麼事, 還真不容易。當一步步了解Git的核心與運作原理, 自然能有效掌握Git儲存庫中的版本變化。本篇來說說Git如何記錄我們每一版的變更軌跡。

## 了解版本紀錄的過程 ##

清楚理解Git基礎原理與物件結構, 便能了解版本紀錄的過程。我們在版控的過程中盡情commit建立版本, 但如果有天某版本壞掉了, 或因執行一些合併或重置等動作導致版本消失,該如何?

還好Git裡面, 有一套嚴謹的紀錄機制, 且這套機制非常開放, 紀錄的檔案都是文字格式, 還蠻容易了解。接下來說明版本紀錄的過程。

`.git/`有個`logs`目錄, 這個`logs`資料夾下有個`HEAD`檔案, 這檔案紀錄「當前分支」的版本變更紀錄:

開啟該內容:

    0000000000000000000000000000000000000000 d7b3014a945337c214cdbcd0b768be74fa55532e Eden Liu <eden90267@atlassian.com> 1458030902 +0800	commit (initial): Initial commit
    d7b3014a945337c214cdbcd0b768be74fa55532e e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff Eden Liu <eden90267@atlassian.com> 1458030963 +0800	commit: a.txt: set 1 as content
    e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff 39b9da78e13bb545893b4b9406df2a6c69bb85d2 Eden Liu <eden90267@atlassian.com> 1458032141 +0800	commit: Create b.txt with content 'master' in the master branch
    39b9da78e13bb545893b4b9406df2a6c69bb85d2 39b9da78e13bb545893b4b9406df2a6c69bb85d2 Eden Liu <eden90267@atlassian.com> 1458032263 +0800	checkout: moving from master to newbranch2
    39b9da78e13bb545893b4b9406df2a6c69bb85d2 254922812bf705a9b1eef8f781d56d645ea9f6ff Eden Liu <eden90267@atlassian.com> 1458032332 +0800	commit: Modify b.txt with content 'newbranch2' in the newbranch2 branch
    254922812bf705a9b1eef8f781d56d645ea9f6ff e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff Eden Liu <eden90267@atlassian.com> 1458032501 +0800	checkout: moving from newbranch2 to newbranch1
    e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff 39b9da78e13bb545893b4b9406df2a6c69bb85d2 Eden Liu <eden90267@atlassian.com> 1458032596 +0800	checkout: moving from newbranch1 to master
    39b9da78e13bb545893b4b9406df2a6c69bb85d2 e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff Eden Liu <eden90267@atlassian.com> 1458033168 +0800	checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff
    e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff Eden Liu <eden90267@atlassian.com> 1458033686 +0800	checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff to newbranch1
    e93d0dc14653f8b72c1a2fbb3da4327fd06e7cff 65f026a35a9e62bb9855cb81b5de265f37b0956c Eden Liu <eden90267@atlassian.com> 1458033730 +0800	commit: Add b.txt in newbranch1
    65f026a35a9e62bb9855cb81b5de265f37b0956c 39b9da78e13bb545893b4b9406df2a6c69bb85d2 Eden Liu <eden90267@atlassian.com> 1458034229 +0800	checkout: moving from newbranch1 to master
    39b9da78e13bb545893b4b9406df2a6c69bb85d2 65f026a35a9e62bb9855cb81b5de265f37b0956c Eden Liu <eden90267@atlassian.com> 1458034272 +0800	checkout: moving from master to newbranch1
    65f026a35a9e62bb9855cb81b5de265f37b0956c 254922812bf705a9b1eef8f781d56d645ea9f6ff Eden Liu <eden90267@atlassian.com> 1458034286 +0800	checkout: moving from newbranch1 to newbranch2
    254922812bf705a9b1eef8f781d56d645ea9f6ff 65f026a35a9e62bb9855cb81b5de265f37b0956c Eden Liu <eden90267@atlassian.com> 1458034348 +0800	checkout: moving from newbranch2 to newbranch1
    65f026a35a9e62bb9855cb81b5de265f37b0956c 254922812bf705a9b1eef8f781d56d645ea9f6ff Eden Liu <eden90267@atlassian.com> 1458034352 +0800	checkout: moving from newbranch1 to newbranch2
    254922812bf705a9b1eef8f781d56d645ea9f6ff 39b9da78e13bb545893b4b9406df2a6c69bb85d2 Eden Liu <eden90267@atlassian.com> 1458034355 +0800	checkout: moving from newbranch2 to master
    39b9da78e13bb545893b4b9406df2a6c69bb85d2 65f026a35a9e62bb9855cb81b5de265f37b0956c Eden Liu <eden90267@atlassian.com> 1458540022 +0800	checkout: moving from master to newbranch1
    65f026a35a9e62bb9855cb81b5de265f37b0956c 65f026a35a9e62bb9855cb81b5de265f37b0956c Eden Liu <eden90267@atlassian.com> 1458547623 +0800	checkout: moving from newbranch1 to f2e
    65f026a35a9e62bb9855cb81b5de265f37b0956c 65f026a35a9e62bb9855cb81b5de265f37b0956c Eden Liu <eden90267@atlassian.com> 1458636049 +0800	checkout: moving from f2e to newbranch1

此時我們用`git reflog`即可列印出所有「歷史紀錄」的版本變化, 內容一樣, 但順序顛倒:

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog
    65f026a HEAD@{0}: checkout: moving from f2e to newbranch1
    65f026a HEAD@{1}: checkout: moving from newbranch1 to f2e
    65f026a HEAD@{2}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{3}: checkout: moving from newbranch2 to master
    2549228 HEAD@{4}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{5}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{6}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{7}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{8}: checkout: moving from newbranch1 to master
    65f026a HEAD@{9}: commit: Add b.txt in newbranch1
    e93d0dc HEAD@{10}: checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cf
    f to newbranch1
    e93d0dc HEAD@{11}: checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da43
    27fd06e7cff
    39b9da7 HEAD@{12}: checkout: moving from newbranch1 to master
    e93d0dc HEAD@{13}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{14}: commit: Modify b.txt with content 'newbranch2' in the newbran
    ch2 branch
    39b9da7 HEAD@{15}: checkout: moving from master to newbranch2
    39b9da7 HEAD@{16}: commit: Create b.txt with content 'master' in the master bran
    ch
    e93d0dc HEAD@{17}: commit: a.txt: set 1 as content
    d7b3014 HEAD@{18}: commit (initial): Initial commit

文字檔, 第一版在最上面; 而git reflog則先顯示「最新版」最後才是「第一版」。

現在試圖建立一個新版本, 看看記錄檔的變化:

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> echo d > d.txt
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git add .
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git commit -m "Add d.txt"
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog
    195fb18 HEAD@{0}: commit: Add d.txt
    65f026a HEAD@{1}: checkout: moving from f2e to newbranch1
    65f026a HEAD@{2}: checkout: moving from newbranch1 to f2e
    65f026a HEAD@{3}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{4}: checkout: moving from newbranch2 to master
    2549228 HEAD@{5}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{6}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{7}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{8}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{9}: checkout: moving from newbranch1 to master
    65f026a HEAD@{10}: commit: Add b.txt in newbranch1
    e93d0dc HEAD@{11}: checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cf
    f to newbranch1
    e93d0dc HEAD@{12}: checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da43
    27fd06e7cff
    39b9da7 HEAD@{13}: checkout: moving from newbranch1 to master
    e93d0dc HEAD@{14}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{15}: commit: Modify b.txt with content 'newbranch2' in the newbran
    ch2 branch
    39b9da7 HEAD@{16}: checkout: moving from master to newbranch2
    39b9da7 HEAD@{17}: commit: Create b.txt with content 'master' in the master bran
    ch
    e93d0dc HEAD@{18}: commit: a.txt: set 1 as content
    d7b3014 HEAD@{19}: commit (initial): Initial commit

上面可發現到, 有個特殊「參考名稱」為`HEAD@{0}`, 每個版本都會有一個歷史紀錄都會有個編號, 代表這個版本的在記錄檔中的順位, 如果是`HEAD@{0}`, 永遠代表目前分支的「最新版」, 換句話說你在這個「分支」中最近一次對Git操作的紀錄。 你對Git所做的任何版本變更, 全部都會被記錄下來。

## 復原意外地變更 ##

初學Git可能不小心指令執行錯誤, ex: `git merge`合併發生衝突, 或透過`git pull`取得遠端儲存庫最新版發生失誤。在這情況下, 可利用`HEAD@{0}`這個特殊「參考名稱」來對此版本「定位」, 並將目前的Git儲存庫版本回復到前一版或前面好幾版。

ex: 「取消」最近一次的變更紀錄, 可透過 `git reset "HEAD@{1}" --hard` 來復原變更。如此一來, 原本 `HEAD@{0}` 的變更, 就會被刪除。不過, 所有變更都會被記錄, 包含 `git reset "HEAD@{1}" --hard` 這個動作。

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git ll
    * 1060cb0 2016-04-06 | Add d.txt (HEAD -> master) [Eden Liu]
    * 39b9da7 2016-03-15 | Create b.txt with content 'master' in the master branch [
    Eden Liu]
    * e93d0dc 2016-03-15 | a.txt: set 1 as content [Eden Liu]
    * d7b3014 2016-03-15 | Initial commit (refs/InitialCommit) [Eden Liu]
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git reflog
    1060cb0 HEAD@{0}: commit: Add d.txt
    39b9da7 HEAD@{1}: checkout: moving from newbranch1 to master
    195fb18 HEAD@{2}: commit: Add d.txt
    65f026a HEAD@{3}: checkout: moving from f2e to newbranch1
    65f026a HEAD@{4}: checkout: moving from newbranch1 to f2e
    65f026a HEAD@{5}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{6}: checkout: moving from newbranch2 to master
    2549228 HEAD@{7}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{8}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{9}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{10}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{11}: checkout: moving from newbranch1 to master
    65f026a HEAD@{12}: commit: Add b.txt in newbranch1
    e93d0dc HEAD@{13}: checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cf
    f to newbranch1
    e93d0dc HEAD@{14}: checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da43
    27fd06e7cff
    39b9da7 HEAD@{15}: checkout: moving from newbranch1 to master
    e93d0dc HEAD@{16}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{17}: commit: Modify b.txt with content 'newbranch2' in the newbran
    ch2 branch
    39b9da7 HEAD@{18}: checkout: moving from master to newbranch2
    39b9da7 HEAD@{19}: commit: Create b.txt with content 'master' in the master bran
    ch
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git reset "HEAD@{1}
    " --hard
    HEAD is now at 39b9da7 Create b.txt with content 'master' in the master branch
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git ll
    * 39b9da7 2016-03-15 | Create b.txt with content 'master' in the master branch (
    HEAD -> master) [Eden Liu]
    * e93d0dc 2016-03-15 | a.txt: set 1 as content [Eden Liu]
    * d7b3014 2016-03-15 | Initial commit (refs/InitialCommit) [Eden Liu]
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git reflog
    39b9da7 HEAD@{0}: reset: moving to HEAD@{1}
    1060cb0 HEAD@{1}: commit: Add d.txt
    39b9da7 HEAD@{2}: checkout: moving from newbranch1 to master
    195fb18 HEAD@{3}: commit: Add d.txt
    65f026a HEAD@{4}: checkout: moving from f2e to newbranch1
    65f026a HEAD@{5}: checkout: moving from newbranch1 to f2e
    65f026a HEAD@{6}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{7}: checkout: moving from newbranch2 to master
    2549228 HEAD@{8}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{9}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{10}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{11}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{12}: checkout: moving from newbranch1 to master
    65f026a HEAD@{13}: commit: Add b.txt in newbranch1
    e93d0dc HEAD@{14}: checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cf
    f to newbranch1
    e93d0dc HEAD@{15}: checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da43
    27fd06e7cff
    39b9da7 HEAD@{16}: checkout: moving from newbranch1 to master
    e93d0dc HEAD@{17}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{18}: commit: Modify b.txt with content 'newbranch2' in the newbran
    ch2 branch
    39b9da7 HEAD@{19}: checkout: moving from master to newbranch2
    39b9da7 HEAD@{20}: commit: Create b.txt with content 'master' in the master bran

這代表什麼意義? 這代表你在執行Git命令, 再也不用擔心害怕你的任何資料會遺失, 就算怎樣下錯指令都沒關係, 所有已經在版本庫中的檔案, 全都會保存下來, 完全不會有遺失的機會。這時若想復原剛剛執行的  `git reset "HEAD@{1}" --hard` 動作, 只要再執行一次 git reset "HEAD@{1}" --hard即可。看下面例子, 把剛剛 `1060cb0` 這版本給救回了。

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git ll
    * 39b9da7 2016-03-15 | Create b.txt with content 'master' in the master branch (
    HEAD -> master) [Eden Liu]
    * e93d0dc 2016-03-15 | a.txt: set 1 as content [Eden Liu]
    * d7b3014 2016-03-15 | Initial commit (refs/InitialCommit) [Eden Liu]
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git reflog
    39b9da7 HEAD@{0}: reset: moving to HEAD@{1}
    1060cb0 HEAD@{1}: commit: Add d.txt
    39b9da7 HEAD@{2}: checkout: moving from newbranch1 to master
    195fb18 HEAD@{3}: commit: Add d.txt
    65f026a HEAD@{4}: checkout: moving from f2e to newbranch1
    65f026a HEAD@{5}: checkout: moving from newbranch1 to f2e
    65f026a HEAD@{6}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{7}: checkout: moving from newbranch2 to master
    2549228 HEAD@{8}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{9}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{10}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{11}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{12}: checkout: moving from newbranch1 to master
    65f026a HEAD@{13}: commit: Add b.txt in newbranch1
    e93d0dc HEAD@{14}: checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cf
    f to newbranch1
    e93d0dc HEAD@{15}: checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da43
    27fd06e7cff
    39b9da7 HEAD@{16}: checkout: moving from newbranch1 to master
    e93d0dc HEAD@{17}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{18}: commit: Modify b.txt with content 'newbranch2' in the newbran
    ch2 branch
    39b9da7 HEAD@{19}: checkout: moving from master to newbranch2
    39b9da7 HEAD@{20}: commit: Create b.txt with content 'master' in the master bran
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git reset "HEAD@{1}
    " --hard
    HEAD is now at 1060cb0 Add d.txt
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git ll
    * 1060cb0 2016-04-06 | Add d.txt (HEAD -> master) [Eden Liu]
    * 39b9da7 2016-03-15 | Create b.txt with content 'master' in the master branch [
    Eden Liu]
    * e93d0dc 2016-03-15 | a.txt: set 1 as content [Eden Liu]
    * d7b3014 2016-03-15 | Initial commit (refs/InitialCommit) [Eden Liu]

## 記錄版本變更的原則 ##

事實上在使用Git版控的過程中, 有很多機會會產生「版本歷史紀錄」, 我說的並不是單純的 `git log` 顯示版本紀錄, 而是**原始且完整**的變更歷史紀錄。這些紀錄版本變更有個基本原則：【只要透過指令修改任何參照(ref)的內容, 或是變更任何分支的 `HEAD` 參照內容, 就會建立歷史紀錄】。也因為這原則, 所以指令名叫 `reflog`, 因為是改了 `ref` (參照內容)才引發的 `log` (紀錄)。

例如拿 `git checkout` 命令切換不同分支, 這個切換過程由於會修改 `.git\HEAD` 參照內容, 所以也會產生一個歷史紀錄:

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [master]> git checkout newbra
    nch1
    Switched to branch 'newbranch1'
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog
    195fb18 HEAD@{0}: checkout: moving from master to newbranch1
    1060cb0 HEAD@{1}: reset: moving to HEAD@{1}
    39b9da7 HEAD@{2}: reset: moving to HEAD@{1}
    1060cb0 HEAD@{3}: commit: Add d.txt
    39b9da7 HEAD@{4}: checkout: moving from newbranch1 to master
    195fb18 HEAD@{5}: commit: Add d.txt
    65f026a HEAD@{6}: checkout: moving from f2e to newbranch1
    65f026a HEAD@{7}: checkout: moving from newbranch1 to f2e
    65f026a HEAD@{8}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{9}: checkout: moving from newbranch2 to master
    2549228 HEAD@{10}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{11}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{12}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{13}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{14}: checkout: moving from newbranch1 to master
    65f026a HEAD@{15}: commit: Add b.txt in newbranch1
    e93d0dc HEAD@{16}: checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cf
    f to newbranch1
    e93d0dc HEAD@{17}: checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da43
    27fd06e7cff
    39b9da7 HEAD@{18}: checkout: moving from newbranch1 to master
    e93d0dc HEAD@{19}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{20}: commit: Modify b.txt with content 'newbranch2' in the newbran
    ch2 branch

還有哪些動作會導致產生新的 `reflog` 紀錄? 不用死記, 記住原則就好:

- commit
- checkout
- pull
- push
- merge
- reset
- clone
- branch
- rebase
- stash

除此之外, 每一個分支、每一個暫存版本(stash), 都會有自己的reflog歷史紀錄, 這些資料也全都會儲存在 `.git\logs\refs\` 資料夾下

## 只顯示特定分支的reflog紀錄 ##

在查詢歷史紀錄, 也可以針對特定分支(Branch)進行查詢, 僅顯示特定分支的變更歷史紀錄:

C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git branch
  f2e
  master
* newbranch1
  newbranch2
C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog HEAD

    195fb18 HEAD@{0}: checkout: moving from master to newbranch1
    1060cb0 HEAD@{1}: reset: moving to HEAD@{1}
    39b9da7 HEAD@{2}: reset: moving to HEAD@{1}
    1060cb0 HEAD@{3}: commit: Add d.txt
    39b9da7 HEAD@{4}: checkout: moving from newbranch1 to master
    195fb18 HEAD@{5}: commit: Add d.txt
    65f026a HEAD@{6}: checkout: moving from f2e to newbranch1
    65f026a HEAD@{7}: checkout: moving from newbranch1 to f2e
    65f026a HEAD@{8}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{9}: checkout: moving from newbranch2 to master
    2549228 HEAD@{10}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{11}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{12}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{13}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{14}: checkout: moving from newbranch1 to master
    65f026a HEAD@{15}: commit: Add b.txt in newbranch1
    e93d0dc HEAD@{16}: checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cf
    f to newbranch1
    e93d0dc HEAD@{17}: checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da43
    27fd06e7cff
    39b9da7 HEAD@{18}: checkout: moving from newbranch1 to master
    e93d0dc HEAD@{19}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{20}: commit: Modify b.txt with content 'newbranch2' in the newbran
    ch2 branch
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog mast
    er
    1060cb0 master@{0}: reset: moving to HEAD@{1}
    39b9da7 master@{1}: reset: moving to HEAD@{1}
    1060cb0 master@{2}: commit: Add d.txt
    39b9da7 master@{3}: commit: Create b.txt with content 'master' in the master bra
    nch
    e93d0dc master@{4}: commit: a.txt: set 1 as content
    d7b3014 master@{5}: commit (initial): Initial commit
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog newb
    ranch1
    195fb18 newbranch1@{0}: commit: Add d.txt
    65f026a newbranch1@{1}: commit: Add b.txt in newbranch1
    e93d0dc newbranch1@{2}: branch: Created from HEAD

## 顯示 reflog 的詳細版本紀錄 ##

`git reflog` 為取出版本歷史紀錄的摘要資訊。但如果想要顯示每一個reflog版本中, 每一個版本的完整 commit 內容, 可用 `git log -g` 指令顯示出來:

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git log -g
    commit 195fb1883f3f560735db2d0b9190a0dda8792130
    Reflog: HEAD@{0} (Eden Liu <eden90267@gmail.com>)
    Reflog message: checkout: moving from master to newbranch1
    Author: Eden Liu <eden90267@gmail.com>
    Date:   Wed Apr 6 09:38:55 2016 +0800

    Add d.txt

    commit 1060cb08342af71c50520abcc9c38ab88ef6ca2e
    Reflog: HEAD@{1} (Eden Liu <eden90267@gmail.com>)
    Reflog message: reset: moving to HEAD@{1}
    Author: Eden Liu <eden90267@gmail.com>
    Date:   Wed Apr 6 16:53:40 2016 +0800

    Add d.txt

    commit 39b9da78e13bb545893b4b9406df2a6c69bb85d2
    Reflog: HEAD@{2} (Eden Liu <eden90267@gmail.com>)
    Reflog message: reset: moving to HEAD@{1}
    Author: Eden Liu <eden90267@atlassian.com>
    Date:   Tue Mar 15 16:55:41 2016 +0800

    Create b.txt with content 'master' in the master branch

## 刪除特定幾個版本的歷史紀錄 ##

基本上, 版本日誌(reflog)所記錄的只是變更的過程, 而且預設只會儲存在「工作目錄」下的 `.git/` 目錄裡, 這裡所記錄的一樣只是commit物件的指標而已。無論對這些紀錄做任何操作, 不管是竄改、刪除, 都不會影響到目前物件儲存庫的任何內容, 也不會影響版本控管的任何資訊。

如果想刪除之前紀錄的某些紀錄, 可利用 `git reflog delete ref@{specifier}` 來刪除特定歷史紀錄。

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog
    195fb18 HEAD@{0}: checkout: moving from master to newbranch1
    1060cb0 HEAD@{1}: reset: moving to HEAD@{1}
    39b9da7 HEAD@{2}: reset: moving to HEAD@{1}
    1060cb0 HEAD@{3}: commit: Add d.txt
    39b9da7 HEAD@{4}: checkout: moving from newbranch1 to master
    195fb18 HEAD@{5}: commit: Add d.txt
    65f026a HEAD@{6}: checkout: moving from f2e to newbranch1
    65f026a HEAD@{7}: checkout: moving from newbranch1 to f2e
    65f026a HEAD@{8}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{9}: checkout: moving from newbranch2 to master
    2549228 HEAD@{10}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{11}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{12}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{13}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{14}: checkout: moving from newbranch1 to master
    65f026a HEAD@{15}: commit: Add b.txt in newbranch1
    e93d0dc HEAD@{16}: checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cf
    f to newbranch1
    e93d0dc HEAD@{17}: checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da43
    27fd06e7cff
    39b9da7 HEAD@{18}: checkout: moving from newbranch1 to master
    e93d0dc HEAD@{19}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{20}: commit: Modify b.txt with content 'newbranch2' in the newbran
    ch2 branch
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog dele
    te HEAD@{0}
    error: Not a reflog: HEAD@
    error: Not a reflog: -encodedCommand
    error: Not a reflog: MAA=
    error: Not a reflog: -inputFormat
    error: Not a reflog: xml
    error: Not a reflog: -outputFormat
    error: Not a reflog: text

**註**: 這些版本日誌預設並不會被同步到「遠端儲存庫」, 以免多人開發時大家互相影響, 所以版本日誌算是比較個人的東西。

## 設定歷史紀錄的過期時間 ##

預設Git會幫你保存這些歷史紀錄90天, 如果這些紀錄中已有些commit物件不在分支線上, 則預設會保留30天。

ex: 先前建立一個分支, 然後commit了幾個版本, 最後直接刪除掉該分支, 這時這些曾經commit過的版本(即commit物件)還會儲存在物件儲存區(object storage)中, 但已經無法使用 `git log` 取得該版本, 我們稱這些版本為「不再分支線上的版本」。

想修改預設的過期期限, 可透過 `git config gc.reflogExpire` 與 `git config gc.reflogExpireUnreachable` 修正這兩個過期預設值, 如永遠不想刪除紀錄:

    git config --global gc.reflogExpire "never"
    git config --global gc.reflogExpireUnreachable "never"

如果只想保存7天

    git config --global gc.reflogExpire "7 days"
    git config --global gc.reflogExpireUnreachable "7 days"

也可針對特定分支設定

    git config --local gc.master.reflogExpire "14 days"
    git config --local gc.master.reflogExpireUnreachable "14 days"

    git config --local gc.develop.reflogExpire "never"
    git config --local gc.develop.reflogExpireUnreachable "never"

上述指令寫入到 `.git\config` 的內容將是:

    [gc "master"]
    	reflogExpire = 14 days
    	reflogExpireUnreachable = 14 days
    [gc "develop"]
    	reflogExpire = never
    	reflogExpireUnreachable = never

## 清除歷史紀錄 ##

可使用 git reflog expire --expire=now --all 指令完成刪除動作, 最後搭配 git gc 重新整理或清除那些找不到、無法追蹤的版本。

    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog
    195fb18 HEAD@{0}: checkout: moving from master to newbranch1
    1060cb0 HEAD@{1}: reset: moving to HEAD@{1}
    39b9da7 HEAD@{2}: reset: moving to HEAD@{1}
    1060cb0 HEAD@{3}: commit: Add d.txt
    39b9da7 HEAD@{4}: checkout: moving from newbranch1 to master
    195fb18 HEAD@{5}: commit: Add d.txt
    65f026a HEAD@{6}: checkout: moving from f2e to newbranch1
    65f026a HEAD@{7}: checkout: moving from newbranch1 to f2e
    65f026a HEAD@{8}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{9}: checkout: moving from newbranch2 to master
    2549228 HEAD@{10}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{11}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{12}: checkout: moving from newbranch1 to newbranch2
    65f026a HEAD@{13}: checkout: moving from master to newbranch1
    39b9da7 HEAD@{14}: checkout: moving from newbranch1 to master
    65f026a HEAD@{15}: commit: Add b.txt in newbranch1
    e93d0dc HEAD@{16}: checkout: moving from e93d0dc14653f8b72c1a2fbb3da4327fd06e7cf
    f to newbranch1
    e93d0dc HEAD@{17}: checkout: moving from master to e93d0dc14653f8b72c1a2fbb3da43
    27fd06e7cff
    39b9da7 HEAD@{18}: checkout: moving from newbranch1 to master
    e93d0dc HEAD@{19}: checkout: moving from newbranch2 to newbranch1
    2549228 HEAD@{20}: commit: Modify b.txt with content 'newbranch2' in the newbran
    ch2 branch
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog expi
    re --expire=new --all
    fatal: '--expire=new' is not a valid timestamp
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog expi
    re --expire=now --all
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git gc
    Counting objects: 21, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (13/13), done.
    Writing objects: 100% (21/21), done.
    Total 21 (delta 3), reused 0 (delta 0)
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]> git reflog
    C:\Users\eden_liu\Documents\GitHub\git-branch-demo [newbranch1]>

## 今日小結 ##

Git的版本日誌(reflog)幫我們記憶在版控過程中的所有變更, 幫助我們「回憶」到底這段時間到底對Git儲存庫做什麼事。不過這只是個「日誌」, 不會影響Git儲存庫中的任何版本資訊。

重新整理本日學到的Git指令與參數:

- git reflog
- git geflog [ref]
- git log -g
- git reset "HEAD@{1}" --hard
- git reflog delete "ref@{specifier}"
- git reflog delete "HEAD@{0}"
- git reflog expire --expire=now --all
- git gc
- git config --global gc.reflogExpire "never"
- git config --global gc.reflogExpireUnreachable "never"

---

# Day 17: 關於合併的基本觀念與使用方式 #

Git是一種分散式的版本控管系統(DVCS), 過程中會不斷進行分支與合併, 無論是有意的合併(git merge)或無意的合併(git pull), 總之使用Git版控「分支」與「合併」的動作確實經常發生。本篇文章將說明「合併」動作的基本觀念與使用方式。

## 關於「合併」的基本觀念 ##

一般來說, 大家都是以一個主要或預設分支進行開發(`master`), 然後再依據需求建立分支(`bugfix`), 最後則將兩個分支合併成一個。執行「合併」動作, 是將另一個分支合併回目前分支, 然後再手動將另一個分支給移除, 這樣才符合「兩個分支合併成一個」的概念。

實務上, 也經常會將三個、四個或更多的分支(bugfix、feature)合併到其中一個分支。

Git使用合併, 有一個重要觀念【合併的動作必須發生在同一個儲存庫中】。回想一下, 在任何一個Git儲存庫中, 都必須存在一個Initial Commit物件(初始版本), 而所有其他版本都會跟這個版本有關係, 這個關係我們稱為「在分支線上的可追蹤物件」(the tracked object on the branch heads), 所以你不能將一個儲存庫的特定分支合併到另一個毫不相干的儲存庫的某個分支裡。

合併的時候, 只要兩個分支有改到相同檔案, 但行數不一樣, Git就會自動套用/合併這兩個變更。但如果剛好改到「同一個檔案」的「同一行」, 那麼在合併就會發生衝突事件。合併衝突發生時, Git並不會幫你決定任何事, 而是將「解決衝突」的工作交給「你」來負責, 且這些發生衝突的檔案也都會被標示 `unmerged` 狀態, 合併衝突後你可以用 `git status` 指令看到這些狀態。

## 體驗一場成功的合併 ##

	ryuutekiMacBook-Pro:Github eden90267$ mkdir git-merge-demo
	ryuutekiMacBook-Pro:Github eden90267$ cd git-merge-demo/
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git init
	Initialized empty Git repository in /Users/eden90267/Github/git-merge-demo/.git/
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ echo . > a.txt
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master

	Initial commit

	Untracked files:
  	(use "git add <file>..." to include in what will be committed)

		a.txt

	nothing added to commit but untracked files present (use "git add" to track)
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git commit -m "Initial commit (a.txt created)"
	[master (root-commit) bebe5df] Initial commit (a.txt created)
	 1 file changed, 1 insertion(+)
	 create mode 100644 a.txt
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ echo 1 > a.txt
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git commit -m "Update a.txt"
	[master 57de564] Update a.txt
 	1 file changed, 1 insertion(+), 1 deletion(-)
 	
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git log
	commit 57de5647cde2132047745e498757593e970d98c4
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 00:33:34 2016 +0800

	    Update a.txt

	commit bebe5df11393d2ca0e42cdff82d03d737501af80
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 00:32:29 2016 +0800

	    Initial commit (a.txt created)
	    
接著用 `git checkout -b feature` 建立一個 `feature` 分支, 並同時把工作目錄給切換到 `feature` 分支進行開發, 然後建立一個內容為 2 的 b.txt 檔案：

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git checkout -b feature
	Switched to a new branch 'feature'
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git branch
	* feature
	  master
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ echo 2 > b.txt
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ cat b.txt
	2
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git commit -m "Add b.txt"
	[feature 5e46a5e] Add b.txt
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	 
**注意**：切換「分支」之前, 請隨時查看並保持「工作記錄」的狀態是「乾淨」的, 不要有任何檔案異動中的狀態。

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch feature
	nothing to commit, working directory clean
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git checkout master
	Switched to branch 'master'
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ echo NEW LINE >> a.txt
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ cat a.txt
	1
	NEW LINE
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master
	Changes not staged for commit:
	  (use "git add <file>..." to update what will be committed)
	  (use "git checkout -- <file>..." to discard changes in working directory)

		modified:   a.txt

	no changes added to commit (use "git add" and/or "git commit -a")
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git commit -m "Update a.txt: 	append NEW LINE"
	[master 34c6f2e] Update a.txt: append NEW LINE
	 1 file changed, 1 insertion(+)
	  
目前狀態：
	
- `master`:
	- a.txt有兩行, 第一行 `1` , 第二行 `NEW LINE`
	- 沒有feature分支的變更, 沒有b.txt檔案
- `feature`:
   - a.txt: 1(從master分支繼承來)
   - b.txt: 2
	
可用SourceTree查看較為漂亮的commit graph版本圖

以上所做的修改並不會有互相衝突, 所以合併並不會發生任何問題, 接下來就來執行「合併」動作(git merge)。

強調細節：

1. 合併之前, 先看清楚自己在哪個分支
2. 合併之前, 確保工作目錄乾淨
3. 合併時請用 `git merge [另一個分支]` 來將另一個分支的變更合併回來
4. 打錯字, Git會自動幫我執行正確指令
5. 合併成功後, 可利用git log查看版本紀錄, 可發現**「合併」的過程會自動建立一個新版本！**

範例：

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git branch
	  feature
	* master
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master
	nothing to commit, working directory clean
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git mrege feature
	git: 'mrege' is not a git command. See 'git --help'.

	Did you mean this?
		merge
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git merge feature
	Merge made by the 'recursive' strategy.
	 b.txt | 1 +
	 1 file changed, 1 insertion(+)
	 create mode 100644 b.txt
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git log --oneline
	002b70e Merge branch 'feature'
	34c6f2e Update a.txt: append NEW LINE
	5e46a5e Add b.txt
	57de564 Update a.txt
	bebe5df Initial commit (a.txt created)
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ ls
	a.txt b.txt
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ cat a.txt
	1
	NEW LINE
	
合併後狀況可用SourceTree的commit graph看

確實如我們預期把兩個分支中的變更都給合併了, 這就是一場成功的合併！

## 刪除不必要的分支 ##

確定用不到 `git branch -d feature` 刪除分支

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git branch -d feature
	Deleted branch feature (was 5e46a5e).

在Git裡, 只要沒執行過「合併」的分支, 都不能用上述指令進行刪除, 必須改用 `git branch -D feature` 才能刪除該分支。

## 救回誤刪的分支 ##

1. 先利用 `git reflog` 找出該分支最後一個版本的 object id(SHA格式的物件絕對名稱)
2. 執行 `git branch feature <SHA1>`

如下所示, 把 `feature` 分支給救回：

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git reflog
	002b70e HEAD@{0}: merge feature: Merge made by the 'recursive' strategy.
	34c6f2e HEAD@{1}: commit: Update a.txt: append NEW LINE
	57de564 HEAD@{2}: checkout: moving from feature to master
	5e46a5e HEAD@{3}: commit: Add b.txt
	57de564 HEAD@{4}: checkout: moving from master to feature
	57de564 HEAD@{5}: commit: Update a.txt
	bebe5df HEAD@{6}: commit (initial): Initial commit (a.txt created)
	ryuutekiMacBook-Pro:git-merge-demo eden90267$
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git branch feature 34c6f2
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git branch
	  feature
	* master
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git log feature
	commit 34c6f2ef900024b3aca802523603670d39655eb2
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 00:44:28 2016 +0800

	    Update a.txt: append NEW LINE

	commit 57de5647cde2132047745e498757593e970d98c4
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 00:33:34 2016 +0800

	    Update a.txt

	commit bebe5df11393d2ca0e42cdff82d03d737501af80
	Author: eden90267 <eden90267@gmail.com>
	Date:   Thu Apr 14 00:32:29 2016 +0800

	    Initial commit (a.txt created)
	    
## 體驗一場衝突的合併 ##

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git branch
	  feature
	* master
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git checkout -b hotfixes
	Switched to a new branch 'hotfixes'
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git branch
	  feature
	* hotfixes
	  master
	  
修改一行並建立版本, 在切回 `master` 分支：

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ cat a.txt
	1
	NEW LINE
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ vi a.txt
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git commit -m "a.txt bug fixed"
	[hotfixes 51d718c] a.txt bug fixed
	 1 file changed, 1 insertion(+), 1 deletion(-)
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch hotfixes
	nothing to commit, working directory clean
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git checkout master
	Switched to branch 'master'
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git branch
	  feature
	  hotfixes
	* master

在 master 分支修改 a.txt的第一行, 並建立版本

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ vi a.txt
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git commit -m "a.txt bug fixed by Eden"
	[master 19d9255] a.txt bug fixed by Eden
	 1 file changed, 1 insertion(+), 1 deletion(-)
	 
現在狀況勢必發生衝突：

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master
	nothing to commit, working directory clean
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git merge hotfixes
	Auto-merging a.txt
	CONFLICT (content): Merge conflict in a.txt
	Automatic merge failed; fix conflicts and then commit the result.
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master
	You have unmerged paths.
	  (fix conflicts and run "git commit")

	Unmerged paths:
	  (use "git add <file>..." to mark resolution)

		both modified:   a.txt

	no changes added to commit (use "git add" and/or "git commit -a")
	
用 `git status` 可看出目前發生衝突的檔案有哪些, 而且你也可以看到這個檔案位於 `Unmerged paths` 這個區段。

## 查看衝突內容 ##

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git diff
	diff --cc a.txt
	index 64bcf3e,d20ab17..0000000
	--- a/a.txt
	+++ b/a.txt
	@@@ -1,2 -1,2 +1,6 @@@
	++<<<<<<< HEAD
	 +bugfixed by Eden: 1
	++=======
	+ bugfixed: 1
	++>>>>>>> hotfixes
	  NEW LINE
	  
必須注意的是 `diff` 針對衝突內容的表示法, 看得懂, 才容易知道怎改：

- `<<<<<<< HEAD` 到 `=======` 的內容, 代表 `HEAD` 裡的 `a.txt` 內容。註： `HEAD` 代表當前 `master` 分支的最新版。
- 從 `=======` 到 `>>>>>>> hotfixes` 的內容, 代表 `hotfixes` 分支裡 `a.txt` 的內容

## 解決衝突的方法 ##

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master
	You have unmerged paths.
	  (fix conflicts and run "git commit")

	Unmerged paths:
	  (use "git add <file>..." to mark resolution)

		both modified:   a.txt

	no changes added to commit (use "git add" and/or "git commit -a")
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git add .
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master
	All conflicts fixed but you are still merging.
	  (use "git commit" to conclude merge)

	Changes to be committed:

		modified:   a.txt

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git commit
	[master 2d085fa] always commit
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git log --oneline
	2d085fa always commit
	19d9255 a.txt bug fixed by Eden
	51d718c a.txt bug fixed
	002b70e Merge branch 'feature'
	34c6f2e Update a.txt: append NEW LINE
	5e46a5e Add b.txt
	57de564 Update a.txt
	bebe5df Initial commit (a.txt created)
	
做錯了, 執行 git reset --hard ORIG_HEAD 就可回復到上一版, 然後再重新合併一次引發相同的衝突

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git reset --hard ORIG_HEAD
	HEAD is now at 19d9255 a.txt bug fixed by Eden
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git log --oneline
	19d9255 a.txt bug fixed by Eden
	002b70e Merge branch 'feature'
	34c6f2e Update a.txt: append NEW LINE
	5e46a5e Add b.txt
	57de564 Update a.txt
	bebe5df Initial commit (a.txt created)
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master
	nothing to commit, working directory clean
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git branch
	  feature
	  hotfixes
	* master
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git merge hotfixes
	Auto-merging a.txt
	CONFLICT (content): Merge conflict in a.txt
	Automatic merge failed; fix conflicts and then commit the result.
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master
	You have unmerged paths.
	  (fix conflicts and run "git commit")

	Unmerged paths:
	  (use "git add <file>..." to mark resolution)

		both modified:   a.txt

	no changes added to commit (use "git add" and/or "git commit -a")
	
## 找出衝突的檔案 ##
	
- `git status`
- `git ls-files -u`

範例：

	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git status
	On branch master
	You have unmerged paths.
	  (fix conflicts and run "git commit")

	Unmerged paths:
	  (use "git add <file>..." to mark resolution)

		both modified:   a.txt

	no changes added to commit (use "git add" and/or "git commit -a")
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git ls-files -u
	100644 58fc33f91f602c290d1c499ff93faefdcd645390 1		a.txt
	100644 64bcf3e9687e48e52d80a56312d192b9184a8271 2		a.txt
	100644 d20ab17e4f178163526e69b85ca247dd478fb2a4 3		a.txt
	
找到後再用 `git diff [filepath]` 就可以比對其中一個檔案了
	
	ryuutekiMacBook-Pro:git-merge-demo eden90267$ git diff a.txt
	diff --cc a.txt
	index 64bcf3e,d20ab17..0000000
	--- a/a.txt
	+++ b/a.txt
	@@@ -1,2 -1,2 +1,6 @@@
	++<<<<<<< HEAD
	 +bugfixed by Eden: 1
	++=======
	+ bugfixed: 1
	++>>>>>>> hotfixes
	  NEW LINE
	  
## 真正解決衝突 ##

使用SourceTree來修復衝突的檔案, 生命會更美好許多。

-> 用Resolve Using 'Mine'

## 今日小結 ##

- git merge [other_branchname]
- git checkout -b [new_branchname]
- git reflog
- git branch -d [branchname]
- git branch -D [branchname]
- git branch feature
- git reset --hard ORIG_HEAD
- git status
- git ls-files -u
- git diff [filepath]

---

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