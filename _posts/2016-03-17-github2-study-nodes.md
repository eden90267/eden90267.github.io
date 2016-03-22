---
layout: post
title:  "GitHub Study Notes(Day 11)"
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