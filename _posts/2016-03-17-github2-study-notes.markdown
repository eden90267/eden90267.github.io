---
layout: post
title:  "GitHub Study Notes(Day 15)"
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