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

	command not found

如果成功找到指令, 該內部指令或應用程式將被系統使用並傳給Linux核心, 根據指令的不同會有不同的輸出結果。

Shell的另一個重要特性是他本身就是一個直譯式型的程式語言, Shell程式設計語言支援絕大多數高階語言的功能, 如函數、變數、陣列與程式控制結構。Shell編程語言簡單易學, 任何指令都能放到一個可執行的Shell程式中。

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

- _(底線) : 上一個指令的最後一個參數
- $ : 目前Shell的程序號碼
- ! : 最後一個背景執行指令的程序號碼
- BASH : BASH的完整路徑名
- BASH_ENV : 和ENV一樣, 但只可在BASH6.0或更高的版本中設定
- BASH_VERSION : BASH的版本號
- CDPATH : cd的搜尋路徑。它是以冒號分隔的目錄列表, Shell透過它來搜尋cd指定的目標目錄
- COLUMNS : 設定視窗的寬度
- EDITOR : 文書編輯器emacs、gmacs或vi的路徑名
- ENV : 每一個新的BASH Shell啟動時執行的環境檔。通常賦予這個變數的檔案名是BASHrc.env的值
- EUID : 列出shell啟動時被初始化的用戶ID
- FORMAT : 用來格式化在指令管道上的time保留字的輸出
- GROUP: 列出當前用戶所屬的群組, 在BASH6.x以前版本中沒有