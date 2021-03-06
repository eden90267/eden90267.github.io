---
layout: post
title:  "GitHub Study Notes(Day 10)"
date:   2016-03-01 10:00:00 +0800
categories: [git, github]
---

公司採用Mercurial SCM分散式版本管理系統, 因GUI工具簡單易用造成基本扎根的部分有些模糊, 所以藉由學習Git來增長對版本控管系統的知識, 學習來源為: [Learn-Git-in-30-days](https://github.com/doggy8088/Learn-Git-in-30-days)。

# Day 1 #

## 前言 ##

Git是一套分散式控管版本系統(DVCS;Distributed Version Control System)。帶來許多版本控管上的各種優勢與解決傳統集中式版本控管上的缺失, 例如: 支援本地操作、備份容易、功能強大且彈性的分支與合併等等。

Github平台整合性高, 有完整的**Git版控支援**、**議題追蹤與管理**、**線上Wiki文件管理**、**友善的Code Review介面**。


## 認識Git版本控管 ##

Git出現來自Linux之父"Linus Torvalds"開發Linux kernel時候, 進行集中式版本控管出現許多問題, 所以基於BitKeeper與Monotone的使用經驗, 設計出更棒的Git版控系統。

Git有幾個重要設計:

- 強力支援非線性開發模式(分散式開發模式)
  - Git擁有快速分支與合併機制, 還包括圖形化的工具顯示版本變更的歷史路徑。
  - Git非常強調分支與合併, 所以版控過程中會不斷執行分支與合併動作。
  - Git分支機制非常輕量無負擔, 每一次分支合併只是某個commit參考指標而已。
- 分散式開發模型
  - 參與Git開發每個人都擁有完整的開發歷史紀錄。
  - 當開發人員第一次將Git版本庫clone下來, 相當這份Git版本庫的「完整備份」。
  - 整個版本庫中的所有變更過的檔案與歷史紀錄, 通通都會儲存local repository。
- 相容現有作業系統
  - Git版本庫其實就一個資料夾而已, 資料夾中有許多相關的設定檔與各種blob物件檔案而已。
  - Git版本庫可用任何方式發布, HTTP, FTP, rsync, SSH甚至用Git protocol都可當成存取Git版本庫的媒介, 相容性高。
- 有效率處理大型專案
  - 由於完整版本庫會clone一份在本機, 該版本庫包含完整的檔案與版本變更紀錄, 所以針對版本控管的各種檔案操作速度, 比存取遠端快上百倍之多。
  - 這也代表著Git版本庫不會因為專案越大檔案越多, 導致速度變慢。
- 歷史紀錄保護
  - Git版控的過程, 每次commit都會產生一組hash id編號, 而且每個版本在變化過程都會參考到這個hash id, 只要hash id無法比對上, Git就會無法運作, 所以當專案越大, 版本庫clone越多, 幾乎無法竄改檔案的內容或版本紀錄。
  - **請記得每個人都有一份完整的版本庫, 你改了原始的那份, 所有人的版本庫就無法再合併回原本的版本庫了, 所以你幾乎不可能任意竄改版本紀錄。**
- 以工具集為主的設計(Toolkit-based design)
  - Git被設計成一個一個工具軟體(指令列工具), 可輕易組合不同工具使用, 非常彈性。
- 彈性的合併策略(Pluggable merge strategies)
  - Git有一個良好設計的「不完整合併(incomplete merge)」機制, 以及多種可以完成合併的演算法, 並在最後告知使用者為何無法自動完成合併, 或通知你需手動進行合併動作。
- 被動的垃圾回收機制
  - 在使用Git時候, 想中斷目前操作或回覆上一操作都是可以的, 可完全不必擔心可能有其中一個指令下錯, 或指令執行到一半當機等問題。
  - Git的垃圾回收機制, 其實就是那些殘留在檔案系統中的無用檔案, 這個垃圾回收機制只會在這些無用物件累積一段時間後自動執行, 也可下達指令。ex: git gc -prune
- 定期封裝物件
  - Git物件就是代表版本庫中的一個檔案。 在版本異動過程中, 專案中的程式碼或其他檔案會被更新, 每次更新時只要檔案內容不同, 就會建立一個新的"物件", 這些不同內容的檔案全部都會被保留下來。
  - 當一個專案越大、版本越多時, 這個物件會越來越多, 雖每個檔案都可以各自壓縮讓檔案變小, 不過過多的檔案會讓存取越來越沒效率。因此Git設計有個機制可將一群老舊的"物件"自動封裝進一個封裝檔(packfile)中, 以改善檔案存取效率。
  - 那些新增檔案還是會以單一檔案的方式存在著, 也代表一個Git版本庫中的"檔案"就是一個Git"物件", 但每隔一段時間就會須重新封裝(repacking)。
  - Git照理說會自動執行重新封裝等動作, 但依然可下達指令執行。ex: git gc
  - 如果要檢查Git維護的檔案系統是否完整, 可執行指令: git fsck。

---

- Git完全不需要Server site支援就可運作版本控制, 因每個仁都有一份完整的儲存庫副本。
- 因每個人都有一份完整的儲存庫版本, 所以每次提交版本變更, 都僅提交到本地的儲存庫而已, 因此提交速度非常快, 也不須網路連線, 節省開發時間
- 由於每個人都有一份完整的儲存庫副本, 代表Git做版本控管, 沒有所謂「權限控管」這件事, 每個成員都能把儲存庫clone回來, 也都可在本地提交變更, 沒任何權限可以限制。使用Git只能設定有沒有權力存取上層儲存庫(upstream repository)或遠端儲存庫(remote repository)的權限。
- 如果需要跟別人交換變更後版本, 隨時可透過「合併」方式進行, Git有非常強悍的merge tracing能力。
- 要合併多人的版本, 只要有存取共用版本庫(shared repository)的權限或管道即可。ex: 在同台Server可透過資料夾權限進行共用, 或透過SSH遠端存取另一台伺服器的Git儲存庫, 也可透過Web Server等方式來共用Git儲存庫。

---

# Day 2 #

## Git工具 ##

- Git for Windows

- GitHub for Windows
  - Git Shell
  - GitHub(GUI)
  
- SourceTree

- TortoiseGit


# Day 3 #

建立儲存庫:
- 本機建立本地儲存庫(local repository)
- 本機建立共用儲存庫(shared repository)
- GitHub或其他Git平台建立遠端的儲存庫(remote repository)

## local repository ##

建立儲存庫: git init指令把儲存庫給建立起來

- path: %USERPROFILE%\Documents\GitHub
- mkdir git-demo
- cd git-demo
- git init

[master]: 為工作目錄的狀態, 可少打git status指令查詢。

## shared repository ##

指建立一個Git儲存庫但不包含工作目錄, 這種狀況較常發生在Linux作業系統下, 因Linux作業系統通常都是多人使用同一台Linux主機。

git init --bare指令建立共用儲存庫, 會在當前目錄建立所有Git儲存庫的相關檔案與資料夾, 此資料夾不能作為開發用途, 只能用來儲存Git相關資訊, 大多數情況下不應該手動編輯此資料夾的任何檔案, 最好透過git指令進行操作。

由於這是一個沒有工作目錄的純儲存庫, 所以共用儲存庫有個別名是「裸儲存庫」。

git clone [REPO_URI]指令: clone一份, 不但自動建立工作目錄, 還會直接把「裸儲存庫」完整的複製回來。似完整備份, 將所有版本紀錄、所有版本檔案、...等等, 所有資料全部複製回來。

實務上會使用「共用儲存庫」或「裸儲存庫」的方式可能有幾種:

- 在一台多人使用的機器上進行協同開發, 可開放大部分人對「裸儲存庫的資料夾」僅唯獨權限, 少數幾人才有寫入權限。
- 把裸儲存庫放到Dropbox跟自己的多台電腦同步這個裸儲存庫

P.S. 工作目錄下的.git資料夾也是一個「儲存庫」, 不過工作目錄下的儲存庫還包含一些工作目錄下的索引資訊, 記錄著工作目錄下的狀態資訊, 這些狀態資訊不會出現在「共用儲存庫」裡面, 這裡只有版本資訊而已(Git物件資訊)。

## remote repository ##

「遠端儲存庫」與「共用儲存庫」差別僅在於「共用儲存庫」大多使用直接的檔案存取, 而「遠端儲存庫」通常使用SSH, Git protocol, HTTP等協定可「遠端」存取Git儲存庫, 其他的使用方式基本上一樣。

git clone https://github.com/eden90267/sanbox.git


# Day 4: 常用的Git版本控管指令 #

主要是基本的版控工作, 其中也包含基本的檔案操作如新增、刪除、重命名檔案, 提交變更(建立新版本)、查詢歷史紀錄等工作。

## 準備目錄工作 ##

建立本地儲存庫

~~~ java

mkdir git-demo
cd git-demo
git init

~~~

## 新增檔案 ##

在工作目錄隨便放一些檔案, 就會立刻在Git Shell介面看到路徑後面提示:

~~~ java

[master +10 ~0 -0 !]

~~~

- master代表目前工作目錄是master分支, 也是Git的預設分支名稱。
- 「紅色」的數字都代表Untracked(未跟蹤)的檔案, 也就是這些變更都不會進入版本控管。
- +10代表有10個「新增」的檔案
- ~0代表有0個「修改」的檔案
- -0代表有0個「刪除」的檔案

如果要將這些新增的檔案加入到Git版本控管, 必須下達以下指令:

~~~ java

git add .

~~~

如此一來這個工作目錄下所有檔案、目錄與子目錄下的所有檔案, 全部會被加入到這個Git工作目錄的【索引】或【快取】之中。P.S. 此時沒有建立任何版本, 只是告知Git這些檔案「即將」被加入到Git版本庫而已。

`git add .`指令執行後, 原本「紅色的數字」變成「綠色的數字」:

- 「綠色」的數字都代表Staged(準備好)的檔案, 也就是這些變更才會進入版本控管
- +23代表有23個「新增」的檔案將被建立一個版本
- ~0代表有0個「修改」的檔案將被建立一個版本
- -0代表有0個「刪除」的檔案將被建立一個版本

原本+10(紅)執行完變+23(綠)?

執行`git reset`重設一下工作目錄的索引狀態, 然後再執行一次`git status`查詢當前工作目錄的詳細狀態。

可以發現列出的只有「第一層目錄下的檔案與目錄」而已, 因為git不會這個時候去查到底目錄下有多少檔案沒有被追蹤。

當執行`git add .`之後, 再執行一次`git status`查詢狀態, 可發現連子目錄下的檔案也都全部被加入了, 數字才會變多。

## 新增部分檔案 ##

`git add .`指令會自動將所有檔案(含子層目錄的檔案)加入到工作目錄索引中, 有時候只想讓特定目錄或特定檔案加入版本, 這時也可以指定特定目錄或利用萬用字原來加入檔案。

再執行一次`git reset`, 然後用`git add app`加入app這個資料夾與旗下的檔案, 還有用`git add .*`新增「點」開頭的檔案。

就會發現到有「綠色的數字」與「紅色的數字」。

## 提交變更 / 建立版本 ##

先執行`git add .`。

建立版本的指令如下:

~~~ java

git commit
git commit -m "版本紀錄的說明文字"

~~~

在Git版本控管中, 所有的版本都必須擁有「版本紀錄的說明文字」(簡稱log), 不像Subversion預設可以簽入「沒有版本紀錄說明」的版本。所以當輸入`git commit`預設會開啟Notepad讓你輸入這個版本訊息。開啟後的檔案會有很多#符號開頭的文字, 這些都是註解, 不會成為Log的一部分。

最上行輸入完成後`Ctrl+s`, 還要關閉Notepad才會正式建立版本。

建立版本後, Git Shell的提示就只剩下`[master]`字樣了, 代表目前已經沒有任何要被建立版本的索引或快取。

## 查詢歷史紀錄 ##

可輸入以下指令:

~~~ java

git log

~~~

如果紀錄越來越多, 可透過以下指令限制輸出的版本數量

~~~ java

git log -10

~~~

## 刪除檔案 ##

~~~ java

git rm 'Gruntfile.js'

~~~

這個git rm指令執行後, 會同時做兩件事:

1. 刪除工作目錄快取的'Gruntfile.js'這個檔案(用來標示這個刪除檔案的動作要列入版本控管)
2. 刪除工作目錄下的'Gruntfile.js'這個實體檔案(代表真的把這個實體檔案給刪除)

## 檔案更名 ##

在Git指令列工具中有個`mv`指令, 可用來變更檔案或目錄的名稱

~~~java

git mv test unit-test

~~~

## 顯示工作目錄的索引狀態 ##

除使用`git status`來顯示工作目錄的狀態, 也可使用git status -s來顯示較為精簡的版本

## 重置目前的工作目錄 ##

請注意`git reset`只會重置「索引狀態」, 用`git rm`刪除的目錄或檔案, 還是用`git mv`更名的目錄或檔案, 透過`git reset`都無法把「實體檔案」給救回來。

如果想把工作目錄也給還原到目前的最新版, 則必須輸入以下指令:

~~~ java

git reset --hard

~~~

## 還原其中一個被改壞的檔案 ##

~~~ java

git checkout master Gruntfile.js

~~~

這個指令意思是把`master`分支中最新版的Gruntfile.js給還原, 可避免用`git reset --hard`把所有檔案一次都給還原了。

請注意, 還原的過程也會一併復原工作目錄的索引狀態!

## 今日小結 ##

常用指令:

- git init
- git add .
- git add app/*
- git add *.txt
- git status
- git status -s
- git commit
- git commit -m "版本紀錄的說明文字"
- git log
- git log -10
- git rm '*.txt'
- git rm 'app/*.html'
- git mv 'oldname' 'newname'
- git reset
- git reset --hard
- git checkout master 'filename'


# Day 5: 了解儲存庫、工作目錄、物件與索引之間的關係 #

## 了解儲存庫 ##

使用Git進行版本控管,很自然的, 需要一個「版本庫」來儲存這些版本資訊, Repository就是這個意思, 代表用來儲存所有版本的一個空間或一個資料夾與一堆檔案。

建立儲存庫有多種方法, 如果要在任意一個資料夾建立一個Git儲存庫, 只要輸入以下指令就可建立完成:

~~~ java

git init

~~~

當在G:\git-demo下執行`git init`之後, Git會自動幫我們建立一個所謂Git repository在該目錄的.git目錄下, 這個.git資料夾就是一個完整的Git儲存庫, 未來所有版本的變更, 都會自動儲存在這個資料夾裡面。

## 了解工作目錄 ##

上述例子, 目錄G:\git-demo此時會自動成為我們的「工作目錄」(working directory)。所謂「工作目錄」, 就是我們正準備開發的專案檔案, 未來都會在這個目錄下進行編輯, 無論是新增檔案、修改檔案、刪除檔案、檔案更名、...以及所有其他的Git相關的操作, 都在這個目錄下完成, 所以才稱為「工作目錄」。

我們在操作Git相關指令參數時, 也通常都在「工作目錄」下執行的。

由於在使用Git版本控管時, 會遭遇很多分支的狀況, 所以工作目錄很有可能會在不同的分支之間進行切換, 有些git指令在執行時, 會一併更新工作目錄下的檔案。例如當使用git checkout切換到不同分支, 由於目前分支與想要切換過去的分支的目錄結構不太一樣, 所以很有可能會將您目前工作目錄下的檔案進行更新, 好讓目前的工作目錄下的這些目錄與檔案, 都與另一個要切換過去的分支下目錄與檔案一樣。

所以, 適時保持工作目錄的乾淨, 是版本控管過程中一個基本原則, 更尤其是日後要進行合併時, 這點尤其重要。

## 了解Git的資料結構 ##

在Git裡有兩個重要的資料結構,分別是「**物件**」與「**索引**」。

「物件」用來**保存版本庫中所有檔案與版本紀錄**，「索引」則是用來**保存當下要進版本庫之前的目錄狀態**。

## 關於物件 ##

所謂的「物件」是一個「特別的檔案」, 該檔案的產生過程很有趣, 是將一個檔案的內容中取出, 透過內容產生一組SHA1雜湊值, 然後依照這個SHA1雜湊值命名的一個檔案。

在使用Git進行版本控管的過程中, 所有要進行控管的目錄與檔案, 都會先區分「**目錄資訊**」與「
**檔案內容**」, 我們稱為**tree物件**與**blob物件**。

其中**blob物件**就是把原本的**「檔案內容」當成blob檔案的內容**(注意: blob物件其實就是一個實體檔案), 然後再將內容進行SHA1雜湊計算後產生的一個hash id, 再把這個hash id當成blob檔案的檔名。由此可知, blob物件只是一個「只有內容」的檔案, 其檔名又是由內容產生的, 所以, 任何一的單獨存在的blob檔案通常對版本控管沒任何幫助。

另一個**tree物件**, 則是用來**儲存特定資料夾下包含哪些檔案, 以及該檔案對應的blob物件的檔名為何**。在tree物件中, 除了可以包含blob物件的檔名與相關資訊, 還可包含其他的tree物件。所以tree物件其實就是「資料夾」的代名詞。

無論blob物件與tree物件, 這些都算是物件。這些物件都會儲存在一個所謂的「物件儲存區」(object storage)之中, 而這「物件儲存區」預設在「儲存區」的objects目錄下。

## 關於索引 ##

所謂「索引」是一個經常異動的暫存檔, 這個檔案通常位於.git目錄下的一位名為index的檔案。簡單來說, 「索引」的目的主要用來記錄「**有哪些檔案即將要被提交到下一個commit版本中**」。換句話說, 如果想提交一個版本到Git儲存庫, 那一定要先更新索引狀態, 變更才會被提交出去。

這個索引檔, 通常保存著Git儲存庫中特定版本的狀態, 這個狀態可以由任意一個commit物件, 以及tree物件所表示。

通常不會直接編輯.get\index這個二進位檔, 而是透過標準的git指令操作這個索引檔, 對於索引檔操作指令大概有以下幾個:

- `git add`
- `git mv`
- `git rm`
- `git status`
- `git commit`
- `git ls-files`

Git的「索引」是一個介於「物件儲存區」(object storage)與「工作目錄」(working directory)之間的媒介。

本章觀念之間的關係, 可用以下5個步驟解釋:

- 要使用Git版本控管, 必須先建立「工作目錄」與「版本庫」。(`mkdir`, `git init`)
- 要先在「工作目錄」進行開發, 你可能會建立目錄、建立檔案、修改檔案、刪除檔案、...等操作。
- 然後當想要提交一個新版本到Git的「儲存庫」裡, 一定要先更新「索引」狀態。(`git add`, `git mv`, ...)
- 然後Git會依據「索引」當下的狀態, 決定要把那些檔案提交到Git的「儲存庫」裡。(`git status`)
- 最後提交變更時(`git commit`),才會把版本資訊寫入到「物件儲存區」當中(此時將會寫入commit物件)。

## 今日小結 ##

沒有「索引」資訊, Git就無法建立版本。

「物件」是一種「不可變」(immuable)的檔案類型,任何寫入到「物件儲存區」的物件, 原則就不會再發生變動, 因所有物件都從原本檔案內容產生的, 可說這是一個物件資料庫(object database), 且這個資料庫通常只會增加內容, 比較不會有「刪除內容」或「異動內容」的情況, 只有在執行`git gc`清除垃圾資料時才會刪除資料。

「索引」則是一種「可變的」(mutable)索引檔, 用來記錄目前工作目錄準備要commit的內容。

# Day 6: 解析Git資料結構 - 物件結構 #

在Git資料結構中, 「物件」是一種「不可變」(immutable)檔案類型, 所有儲存在「物件儲存區」的檔案通常只進不出, 也不會修改內容。 原因在於, 如果竄改檔案內容, 新的內容所運算出來的SHA1雜湊值將會與原有物件的檔名不一樣, 將導致Git無法繼續執行, 相對的也對Git儲存庫產生一定程度的保護作用。

## 關於物件資料庫 ##

無論blob物件與tree物件, 這些都是物件, 這些物件會儲存在「物件儲存區」(object storage)之中, 預設在Git「儲存庫」的objects目錄下。

然而Git儲存庫中每一個「物件」, 都是以「檔案內容」進行SHA1雜湊運算出一個hash值, 並用這個hash值當作物件的名稱(檔名)。以8a6b275638f3cf164395e65066a1132bb36b7896為例, Git會先拿前兩個字元(8a)當作目錄,然後把剩下的hash值當成檔名(6b275638f3cf164395e65066a1132bb36b7896), 這些物件的實體目錄與檔案也都會存在.get\object目錄下。

## 物件的類型 ##

在這些「物件資料庫」裡面, 又包含了4種物件類型, 分別是:

1. **blob物件**: 就是工作目錄中某檔案的"內容", 且只有內容而已, 當執行git add指令同時, 這些新增檔案的內容就會**立刻**被寫入成為blob物件, 檔名則是物件內容的雜湊運算結果, 沒任何其他資訊, 像是檔案時間、原本檔名或檔案的其他資訊, 都會儲存在其他類型的物件裡(也就是tree物件)。
2. **tree物件**: 這類物件會儲存特定目錄下的所有資訊，包含該目錄下的檔名、對應的blob物件名稱、檔案連結(symbolic link)或其他tree物件等等。由於tree物件可以包含其他tree物件, 所以瀏覽tree物件的方式其實就跟檔案系統中的「資料夾」沒兩樣。簡單來說, tree物件這就是在特定版本下某個資料夾的快照(Snapshot)。
3. **commit物件**: 用來記錄有哪些tree物件包含在版本中, 一個commit物件代表著Git的一次提交,　紀錄特定提交版本有哪些tree物件、以及版本提交的時間、紀錄訊息等等, 通常還會紀錄上一層的commit物件名稱(只有第一次的版本沒有上層commit物件名稱)。
4. **tag物件**:是一個容器, 通常用來關聯特定一個commit物件(也可關連到特定的blob、tree物件), 並額外儲存一些額外的參考資訊(metadata), ex: tag名稱。使用tag物件最常見的情況是替特定一個版本的commit物件標示一個易懂的名稱, 可能是代表某個特定發行的版本, 或是擁有某個特殊意義的版本。

Git會將每一個版本中的檔案建立一個對應的blob物件, 一樣的, 該blob物件的檔名就是用上述的方式計算出來, 從這些blob檔案, 看不出跟版本有任何關係, 必須透過tree物件(資料夾snapshot)與commit物件(每一版本的snapshot)才能關聯出這些blob與版本的關係。

**所有物件都會以zlib演算法進行壓縮**, 不但有效提升檔案存取效率, 在日後進行封裝(pack)的時候也可以利用差異壓縮(delta compression)演算法來節省空間。他會自動找出相似的blobs, 並自動計算出blob之間的變化差異, 再將這些差異儲存在一個名為packfile的檔案, 這樣就可以大幅節省磁碟空間的耗用。通常pack file會置於.git\object\pack目錄下。

算出該file的物件hash id(blob物件): `git hash-object a.txt`
看最新的tree物件的hash id: `git cat-file -p master`
查tree物件包含哪些tree或blob物件: `git cat-file -p <treeHashId>`

## 物件結構的優點 ##

重新列出與「物件」特性有關的設計來看看:

- 有效率的處理大型專案
  - 不僅是完整的版本庫會clone一份在本機, 由於所有的blob物件都是透過內容作定址(content addressable), 因此, 若在不同版本之間找尋相同的檔案, 效率是非常高的。
- 歷史紀錄保護
  - Git版控過程, 每次提交變更都會產生一個commit物件, 而這個commit物件名稱又是透過commit物件的內容產生。再者, commit物件會關聯到tree物件, tree物件的名稱又是透過tree物件的內容產生。tree物件又會關聯到blob與tree物件, 這些物件的名稱也是透過內容產生。就這樣一層一層關聯下去, 如果今天要竄改某版本的歷史紀錄, 困難度也挺高的!
  - 由於Git儲存庫經常會被clone或fork, 只要被clone過的儲存庫, 來源的儲存庫只要任何一個物件被修改, 這些clone出去的儲存庫就很難再合併回來, 所以幾乎不可能竄改版本紀錄。
- 定期的封裝物件
  - 在Git提及的「物件」其實就是代表版本庫中的一個檔案。在版本異動過程中, 專案中的程式碼或其他檔案會被更新, 每次更新時, 只要檔案內容不一樣, 就會建立一個新的「物件」, 這些不同內容的檔案全部都會保留下來。
  - 可以想像當一個專案越來越大、版本越來越多, 這物件就會越來越多, 雖然每個檔案都可以各自壓縮讓檔案變小, 不過過多的檔案還是會檔案存取變的越來越沒效率。因此Git設計有個機制可將一群老舊的「物件」自動封裝進一個封裝檔(packfile)中, 以改善存取效率。
  - 那些新增的檔案還是會以單檔方式存在著, 也代表一個Git版本庫中的「檔案」就是一個Git「物件」, 但每隔一段時間就會需要重新封裝(repacking)。
  - 照理Git會自動執行重新封裝等動作, 但依然可自行下達指令。ex: `git gc`
  - 要檢查Git維護的檔案系統是否完整, 可執行: `git fsck`

# Day 7: 解析Git資訊結構 - 索引結構 #

Git有兩個重要資料結構, 分別是「物件」與「索引」。這篇主要解說「索引」的細節。

Linus Torvalds在郵件清單(Mailing List)中提到:「在使用者了解索引的意義前, 是無法完整了解Git的能力的」, 因此了解「索引」的用途十分重要。

## 關於索引 ##

「索引」的目的主要用來記錄「有哪些檔案即將要被提交到下一個commit版本中」。

換句話說, 「如果想要提交一個版本到Git儲存庫, 那麼得先更新索引狀態, 變更才會被提交出去。」

相關單字:

- Index
- Cache
- Directory cache
- Current directory cache
- Staging area(等待被commit的地方)
- Staged files(等待被commit的檔案)

ex: `git diff --cached`與`git diff --staged`是相同的

## 操作索引的指令 ##

由於「索引」對Git十分重要, 在大多數的指令中都會有跟Git索引相關的參數可用。

透過指令改變狀態的生命週期, 改變的過程, 都是在更新「**索引檔**」的過程。

untracked -> unmodified -> modified -> staged

1. untracked -> unmodified: add the file
2. unmodified -> modified: edit the file
3. modified -> staged: stage the file
4. staged -> modified: commit
5. modified -> unmodified: commit
6. unmodified -> untracked: remove the file

四種狀態:

- untracked(未追蹤, 代表尚未被加入Git儲存庫的檔案狀態)
- unmodified(未修改, 代表檔案第一次被加入, 或是檔案內容與HEAD內容一致的狀態)
- modified(已修改的, 代表檔案已經被編輯過, 或是檔案內容與HEAD內容不一致的狀態)
- staged(等待被commit的, 代表下次執行`git commit`會將這些檔案全部送入版本庫)

## git status ##

取得 **工作目錄**(working tree) 下的狀態

一句話形容儲存庫、工作目錄、物件與索引關係:

~~~ java

Git 儲存庫的運作, 是將工作目錄裡的變化, 透過更新索引的方式, 將資料寫入成Git物件。

~~~

git status指令, 目的是顯示出 **目前最新版** 與 **索引檔** 之間的差異，這當中的差異包含了一些微妙的關係, 以下是執行`git status`結果:

~~~ java

G:\git-demo>git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       new file:   c.txt
#
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   a.txt
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#       b.txt

~~~

有三種不同分組, 分別是:

- Changes to be committed(準備提交的變更)
  - 這區有個`new file: c.txt`檔案, 代表c.txt是一個新檔案, 而且已經被標示可提交。
  - 這代表幾件事:
     1. **目前最新版** 並沒有c.txt這檔案
     2. **索引檔** 已經加入了這個
     3. 該檔案會在執行`git commit`之後被存入下一個版本
- Changes not staged for commit(尚未準備提交的變更)
  - 這區有個`modified: a.txt`檔案, 代表a.txt已經被變更, 但尚未標示可提交。(not staged)
  - 這代表幾件事:
     1. **目前最新版** 也有a.txt這個檔案 
     2. **索引檔** 尚未加入a.txt.這個檔案
     3. 該檔案就算執行了`git commit`也不會在下一版中出現
- Untracked files(未追蹤的變更)
  - 這區有個`b.txt`檔案, 代表b.txt尚未被追蹤。(untracked)
  - 這代表幾件事
     1. **目前最新版** 沒有b.txt這個檔案
     2. **索引檔** 也沒有b.txt這個檔案
     3. 所以該檔案就算執行了git commit也不會在下一版中出現

執行git status就是為了查出 **目前最新版** 與 **索引檔** 之間的差異, 最終只有 **目前最新版** 與 **索引檔** 之間有差異的變更, 才會真正儲存到下一個commit物件裡。

## git add ##

`git add`指令, 是為了將目前「工作目錄」的變更寫入到「索引檔」裡。

使用`git add -u`則可以僅將「更新」或「刪除」的檔案變更寫入到「索引檔」中。

## git rm ##

直接在檔案系統中刪除一個檔案, 這只是從「工作目錄」中刪除而已, 並沒有更新到索引檔, 可利用`git status`看到這層改變,不過若真正把「刪除」狀態寫進索引檔, 要靠`git rm filename`更新索引檔。

執行`git rm filename`, 除更新索引檔外, 連工作目錄下的檔案也會一併刪除。

若只想刪除索引檔中的該檔, 又要保留工作目錄下的實體檔案, 可在指令列加上`--cached`參數, 就能做到:

~~~ java

git rm --cached a.txt

~~~

## git mv ##

使用`git mv oldname newname`可以將檔案更名, 執行此命令會同時更新索引與變更工作目錄下的實體檔案。

## git commit ##

把「索引檔」與「目前最新版」中的資料比對出差異, 然後把差異部分提交變成一個commit物件。

## git ls-files ##

在索引檔中, 預設就包含了 **目前最新版** 的所有檔案, 外加在工作目錄中新增檔案且透過`git add`更新索引檔後的那些檔案。透過`git ls-files`命令, 可以列出所有目前已經儲存在「索引檔」中的那些檔案路徑。


# Day 8: 關於分支的基本觀念與使用方式 #

在Git裡面 `分支`(Branch)是個非常重要的機制, 專案不會無限制「分支」下去, 最終還是要「合併」的, 使用上須特別小心。

## 關於分支的基本概念 ##

版本控管中使用「分支」機制, 主要目的就是用來解決開發過程中版本衝突的問題。

由於Git屬於「分散式版本控管機制」, 在分散式版本管理的使用情境, 最不想做的就是「管理」, 所以Git很少有所謂的管理機制或權限控管機制, 他唯一想做的就是讓大家可以順利的「分支」與「合併」而已。

remote repository, 從我們使用`git clone`指令開始, 其實就是「分支」的開始, 你從remote repository clone一份完整的repository下來, 然後開始在自己的本地端建立版本, 等軟體修訂到一定程度後再「合併」回去,只是這時合併指令叫做`git push`。

這種分支與合併的情形, 在Git版本控管的過程無所不在, 遠端儲存庫可以有分支, 本地儲存庫可以有分支, 遠端儲存庫任何一分支合併(pull)到本地分支, 也可將本地分支推向(push)遠端分支, 當然也可從本地任何一分支合併(merge)到本地的另一分支。所以如果「分支」沒有一套良好的控管邏輯, 可以組出各種極其複雜的版本控管使用情境, 非大家所樂見。

`git-flow`是一套廣受歡迎的分支管理模式, 這不是一套工具, 而是一種管理分支的邏輯, 之後會再說明。

Git分散式版控機制, 每個人都有完整的版本, 版本散出去後, 大家必須控管好自己的版本, 然後遵照團隊的要求合併回來。然而, 在合併回來之前, 這套機制確保每個人都能夠順利地開發, 不受任何其他開發人員的版本影響, Git作到這點, 同時降低版本控管的複雜度。

如果參與軟體開發的團隊只有兩三人, 且都還聚在一起, 那確實不一定要使用Git版控, 使用Subversion也是很好選擇, 簡單又直覺, 開發過程遇到問題, 前後左右協調一下就能解決, 遠比讓整個團隊來了解Git來的方便很多。

`git branch`指令得知我們已經擁有一個名為`master`分支, 這是在Git儲存庫中的預設分支。若嘗試透過`git branch -d master`刪除, 會得到`error: Cannot delete the branch'master' which you are currently on.`的錯誤訊息, 代表「目前工作目錄分支設定在master, 不能刪除目前這個分支」, 必須先切換到「其他分支」才能刪除這個分支 。

## 建立分支 ##

常見有兩種方法, 分別是:

1. 建立分支, 但目前工作目錄維持在自己的分支: `git branch [BranchName]`
2. 建立分支, 並將目前工作目錄切換到新的分支: `git checkout -b [BranchName]`

詳細指令與參數說明, 可以輸入`git help branch`查詢完整的文件。

## 切換分支 ##

~~~ java

git checkout [branch_name]

~~~

## 刪除分支 ##

~~~ java

git branch -d [branch_name]

~~~

刪除前必須先切換到其他分支。

## 查看工作目錄在哪個分支 ##

可透過`git status`命令, 查看目前所在分支。

## 查看Git儲存庫的完整分支圖 ##

使用SourceTree工具顯示目前Git儲存庫的分支圖。

~~~ java

git log

# git checkout [commit_id]
git checkout b917758c0f2f347a895ee5bbb5e5c8228f66335a

~~~

若執行`git checkout b917758c0f2f347a895ee5bbb5e5c8228f66335a`時會出現一對訊息, 這些訊息很重要:

~~~ java

'detached HEAD'

git checkout -b new_branch_name

~~~

首先, 由於你將工作目錄的版本切換到「舊的」版本, 所以你會被提示這個工作目錄已經進入所謂的**detached HEAD**狀態, 這是一種「目前工作目錄不在最新版」的提示, 可隨時切換到Git儲存庫的任一版本, 但由於這個版本已有「下一版」, 所以如果你在目前「舊版」執行`git commit`的話, 就會導致這個新版本無法被追蹤變更, 所以建議不要這麼做。

若要在**detached HEAD**狀態建立一個可被追蹤的版本, 正確方法是透過「**建立分支**」的方式來追蹤:

~~~ java

git checkout -b newbranch1

~~~

## 今日小結 ##

分支可把它想像成一種「快照」功能, 把某個commit版本與歷史版本建立出一個快照, 然後複製一份出來, 並給予一個分支名稱, 你可以在這些分支上建立版本, 等待日後進行合併。

本日學到的Git指令與參數:

- git branch
- git branch [branch_name]
- git checkout -b [branch_name]
- git checkout [branch_name]
- git branch -d [branch_name]
- git log


# Day 9: 比對檔案與版本差異 #

使用任何版本控管軟體的過程中, 經常會需要查看歷史紀錄與比對版本之間的差異。Git使用如何進行比對, 將是本文重點。

## 關於git diff的基本概念 ##

在Git中比對兩個版本之間的差異, 通常會用`git diff`命令:

1. 先執行`git log`取得版本資訊, 並取得最近兩個commit物件的id
2. 再執行`git diff commit1 commit2`指令, 比對兩個版本間的差異, 其中commit1請用較舊的版本, commit2則用較新的版本

~~~ java

git diff 53c1f 8adec

~~~

result:

~~~ java

diff --git a/a.txt b/a.txt
index ca90535..00b30ed 100644
Binary files a/a.txt and b/a.txt differ
diff --git a/b.txt b/b.txt
index c7250cb..b1b10c2 100644
Binary files a/b.txt and b/b.txt differ

~~~

因Binary問題比對所以可以採用下列兩種方式:

~~~ java

git difftool <commit1> <commit2>

~~~

~~~ java

git diff --text <commit1> <commit2>

~~~

Reference: [Why does Git treat this text file as a binary file?](http://stackoverflow.com/questions/6855712/why-does-git-treat-this-text-file-as-a-binary-file)

`git diff --text <commit1> <commit2>`的result:

~~~ java

diff --git a/a.txt b/a.txt
index ca90535..00b30ed 100644
--- a/a.txt
+++ b/a.txt
@@ -1,2 +1,2 @@
-<FF><FE>1^@^M^@
+<FF><FE>3^@^M^@
 ^@
\ No newline at end of file
diff --git a/b.txt b/b.txt
index c7250cb..b1b10c2 100644
--- a/b.txt
+++ b/b.txt
@@ -1,2 +1,2 @@
-<FF><FE>2^@^M^@
+<FF><FE>4^@^M^@
 ^@
\ No newline at end of file

~~~

可看到每一段都是以`diff --git`開頭, 代表git對哪兩個檔案進行比對。

第二行的`index ca90535..00b30ed 100644`則是代表git在作這次比對時的「標頭資訊」(Header Line), 這裡可能會有好幾行, 資訊不一定只有這些。這裡會標示許多關於此次差異比對的額外資訊。例如index這行, 後面兩個hash id(`ca90535..00b30ed`)就代表在Git物件儲存庫(object storage)中的兩個blob物件id, 用來比較這兩個blob物件。後面的`100644`則是git屬性, 有點類似Linux環境下的檔案屬性, 例如宣告這是個檔案、目錄、可讀、可寫、可執行之類的。以下是幾個常見的git屬性範例:

~~~ java

0100000000000000 (040000): Directory
1000000110100100 (100644): Regular non-executable file
1000000110110100 (100664): Regular non-executable group-writeable file
1000000111101101 (100755): Regular executable file
1010000000000000 (120000): Symbolic link
1110000000000000 (160000): Gitlink

~~~

第三行的 `--- a/a.txt` 則代表兩個比對的版本中「比較舊的」那個版本。

第四行的 `+++ b/a.txt` 則代表兩個比對的版本中「比較新的」那個版本。

第五行的 `@@ -1 +1 @@` 則代表這個檔案在舊版的總行數與新版的總行數, -1代表舊版只有1行, +1代表新版也只有1行。

最後則是列出所有變更的內容, 有三個可能的表示法:

- 以減號- 號開頭, 代表從舊版到新版的過程中, 此行被刪除了。
- 以加號+ 號開頭, 代表從舊版到新版的過程中, 此行是被新增上去的。
- 以空白字元開頭, 則代表這一行在兩個版本中都有出現, 沒有任何變更。

如此一來就完成第一個blob物件的差異比對, 第二個blob以此類推。

在Git中使用`git diff`的時候, 事實上是以tree物件為比較的單位, 在【Day 6: 解析Git資料結構 - 物件結構】有學到, 其實每一個commit物件都會包括一個根目錄的tree物件。所以剛利用`git diff`比對兩個commit物件, 其實比對是commit物件下的那個tree物件, 而比對的過程又會遞迴的一直比下去。可感受到Git的強大, 可以很快速的比對出任意兩個版本之間的異動比較。

使用`git diff`命令時, 主要有三種tree物件的來源, 分別是:

- 在所有commit graph中存在的tree object, 也就是任意版本中任意一個tree物件的意思。
- 索引(index), 代表你已經將檔案狀態送進「索引資料庫」中哪些資訊, 此時透過`git add`命令時, 其實tree物件已經被建立。
- 你目前的工作目錄(working directory), 雖然工作目錄的改變還沒變成tree物件, 但透過`git diff`是可以這樣用的。

## 四種基本的比較方式 ##

要透過`git diff`命令比對任意兩個版本, 通常會有以下四種指令的用法:

1.  git diff
    
    在什麼參數都不加的使用情況, 比對的是「工作目錄」與「索引」之間的差異。這是很常用指令, 當執行`git add .`指令之前, 先透過`git diff`查看自己到底改了哪些東西。

	註: 事實上, 使用Git版本控管的過程中, 在執行`git commit`之前, 的確有可能會執行`git add`指令好幾次, 用以確認到底哪些檔案要加入到索引之中, 最後才會commit進版本。

2.  git diff commit

	如果只在`git diff`之後加上一個commit id, 比對的是「工作目錄」與「指定commit物件裡的那個tree物件」。

	最常用的指令是`git diff HEAD`, 因為這代表你要拿「工作目錄」與「當前分支的最新版」進行比對。這種比對方法, 不會去比對「索引」的狀態, 所以需區分清楚, 到底比對的是什麼tree物件的來源。

3.  git diff --cached commit

	在執行`git commit`之前, 索引狀態應該已經都準備好了。所以要比對「當前的索引狀態」與「指定commit物件裡的那個tree物件」, 就可用此指令完成比對任務。

	最常用指令一樣是`git diff --cached HEAD`, 這語法代表的是「當前的索引狀態」與「當前分支最新版」進行比對。這種比對方法, 不會去比對「工作目錄」的檔案內容, 而是直接去比對「索引」與「目前最新版」之間的差異, 這有助於你在執行`git commit`之前找出那些變更的內容, 也就是將會有那些變更被建立版本的意思。

	**註1**: `git diff --cached`與`git diff --staged`是完全一樣的結果。

	**註2**: `git diff --cached`與`git diff --cached HEAD`執行也是完全一樣, HEAD可省略。

4.  git diff commit1 commit2

	最後一種則是透過兩個不同的版本(commit id)來比對其差異, 這命令可以跳過「索引」與「工作目錄」的任何變更, 而是直接比對特定的兩個版本。事實上Git是比對特定兩個版本commit物件內的那個tree物件。

	最常用指令則是`git diff HEAD^ HEAD`命令, 這代表要比較【最新版的前一版】與【最新版】之間的差異。

## 今日小結 ##

~~~ java

git diff				=> 工作目錄 vs 索引
git diff HEAD			=> 工作目錄 vs HEAD(當前分支最新版)
git diff --cached HEAD	=> 索引 vs HEAD(當前分支最新版)
git diff --cache			=> 索引 vs HEAD(當前分支最新版)
git diff HEAD^ HEAD		=> HEAD^(前分支一版) vs HEAD(當前分支最新版)

~~~

重新整理本日學到的Git指令與參數:

- git log
- git diff
- git diff HEAD
- git diff --cached HEAD
- git diff --cached
- git diff --staged
- git diff HEAD^ HEAD


# Day 10: 認識Git物件的絕對名稱 #

在Git版本控管過程, 每一個版本代表一個commit物件。因版控過程常會建立分支, 最終的commit graph可能會蠻複雜的, 所以如何識別不同版本或快速定位到特定版本已取得資訊, 就變得很重要。所以必須了解其Git裡常用物件名稱概念與使用方法。

## 物件絕對名稱 ##

在Git中, 每個物件都會有一個以SHA雜湊計算過的id, 而這id就是所謂的「絕對名稱」。如果該物件是commit物件, 那這就是commit物件的絕對名稱, 就可隨時取得該版本。

如果想看commit物件的內容, 可利用`git cat-file -p commitId`來取得

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-demo [master]> git cat-file -p 53373b7f55
82a63abf94da23dc6a34311461ff91
tree f506a7d239e17ad391e82bdce27afd0793250e49
parent da449d835641e72438caf73e219a80dcad8f92a0
author Eden Liu <eden90267@atlassian.com> 1458098483 +0800
committer Eden Liu <eden90267@atlassian.com> 1458098483 +0800

Update a.txt and b.txt to 3 and 4

~~~

## 物件絕對名稱的簡短語法 ##

Git物件id因是透過SHA1雜湊後的結果, 所以很長, 在Git標示「絕對名稱」時, 可以用前面幾碼代替, 最少不可低於4個字元。也就是說4~40個字元長度的「絕對名稱」都可以用。

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-demo [master]> git cat-file -p f506

~~~

取得版本紀錄會使用`git log`命令, 同時也會輸出每個檔案的變更比較結果, 結果會十分冗長, 可用`git log --pretty=oneline`指令取得較為精簡的歷史紀錄, 同時你也可以取得commit物件完整的「絕對名稱」。

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-demo [master]> git log --pretty=oneline
53373b7f5582a63abf94da23dc6a34311461ff91 Update a.txt and b.txt to 3 and 4
da449d835641e72438caf73e219a80dcad8f92a0 Initial commit

~~~

另外一個常用技巧則是僅輸出部分的「絕對名稱」, 透過`git log --pretty=oneline --abbrev-commit`指令執行, 如下:

~~~ java

C:\Users\eden_liu\Documents\GitHub\git-demo [master]> git log --pretty=oneline -
-abbrev-commit
53373b7 Update a.txt and b.txt to 3 and 4
da449d8 Initial commit

~~~

## 今日小結 ##

以上就是絕對名稱用法, 由於Git物件特性, 可透過絕對名稱存取到Git儲存庫中任意物件, 還有很多git指令都會用到絕對名稱, 熟悉了這些概念與表示法, 將更能掌握Git物件

整理一下本日學到的Git指令與參數

- git log
- git cat-file -p [object_id]
- git log --pretty=oneline
- git log --pretty=oneline --abbrev-commit