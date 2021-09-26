---
title: 概述
tags: Linux
lang: zh-tw
---

{%hackmd theme-dark %}

> *抽象思維 => 暫時忽略大部分的細節，並將複雜的部份分解*。
> [color=#7EDBFF]

# Linux作業系統中的抽象級別和層次
- 透過抽象將系統分解為各種元件 (Component)。
- 元件的層次 (Layer) 或級別 (Level) => 它在使用者和硬體系統之間所處的位置。
- 主要分為三層：
    ![](https://i.imgur.com/2lCqzRd.png)
    - 硬體 (Hardware) => 最底層：
        ![](https://i.imgur.com/OGrDolh.png)
    - 核心 (Kernel) => 作業系統的核心，中間層：
        ![](https://i.imgur.com/3o2rkdB.png)
        - 管理硬體系統。
        - 硬體系統和應用程式之間進行通訊的介面。
        - 在Kernel mode中執行：
            - 不受限制存取CPU和記憶體
            - 可輕易使系統崩潰
        - 只有核心可以存取的空間 => [Kernel Space](https://zh.wikipedia.org/wiki/%E4%BD%BF%E7%94%A8%E8%80%85%E7%A9%BA%E9%96%93)。
    - 使用者程序 (User Process) => 最頂層：
        ![](https://i.imgur.com/WVOzGO0.png)
        - 包括電腦中所有執行的程式。
        - 由核心統一管理。
        - 在User mode中執行：
            - 存取CPU和記憶體有一定限制。
            - 使用者程序能存取的記憶體空間 => [User Space](https://zh.wikipedia.org/wiki/%E4%BD%BF%E7%94%A8%E8%80%85%E7%A9%BA%E9%96%93)。

# 硬體系統 : 理解主記憶體 (RAM)
### 主記憶體RAM => main memory
- 儲存0和1這樣的資料 (每組0和1 => 稱為 bit)。
- 核心、程序和外圍設備的輸入輸出都在此執行。

> *通常使用抽象詞彙而非位元值來描述狀態 (state)。
> 映像 (image) => 位元值在記憶體中的特定實體排列。
> CPU => 負責運算*。
> [color=#7EDBFF]

# 核心(Kernel)
### 負責管理
- 程序 => 決定哪個程序可以使用CPU。
- 記憶體 => 管理所有的記憶體，位程序分配記憶體，管理程序間的共享記憶體和空閒記憶體。
- 設備驅動程式 => 負責操控影體設備。
- 系統呼叫和支援 => 程序通常使用系統呼叫和核心進行通訊。
> *核心詳細工作原理參考書：
> [Operating System Concepts, 10th Edition (Wiley，2019)](https://www.tenlong.com.tw/products/9781119454083)
> [Operating System Concepts, 10th (繁中版，東華出版)](https://www.tenlong.com.tw/products/9789865522506?list_name=trs-t)
> [Modern Operating System, 4th Edition ( Pearson FT Press ，2014)](https://www.tenlong.com.tw/products/9781292061429)
> [Modern Operating System, 4th Edition (PDF，2014)](http://index-of.es/Varios-2/Modern%20Operating%20Systems%204th%20Edition.pdf)*
> [color=#7EDBFF]

### 程序管理
- 涉及程序的「啟動」、「暫停」、「恢復」和「終止」。
- 在任何一個特定的時間段內，其實只能有一個程序可以使用CPU，所以每個程序使用一段時間後就會暫停，然後給另一個程序使用，依次輪流。
- [上下文切換 (Context Switch)](https://yang-docs.readthedocs.io/en/latest/linux/contextswitch.html#id4)：
    - 一個程序讓出CPU使用權給另一個程序。
    - 時間單位 => 毫秒 (ms)。
    - 詳細執行流程(黑 => CPU，藍 => 核心)：
        ![](https://i.imgur.com/RgbWrwp.png)
- 程序在其時間段內就能完成其主要的計算工作。由於時間段非常短，短到我們察覺不到，所以我們看起來，系統是同時執行多個程序。
- [多任務執行 (Multitasking)](https://zh.wikipedia.org/wiki/%E5%A4%9A%E4%BB%BB%E5%8A%A1%E5%A4%84%E7%90%86)：
    - 系統同時執行多個程序。
- 核心在「上下文切換時的時間段間隙中」執行。

### 記憶體管理
- 核心在上下文切換中管理記憶體，管理時要保證以下所有條件：
    - 核心要有專屬於自己的記憶體空間，且其他使用者程序無法存取。
    - 每個使用者程序要有專屬於自己的記憶體空間。
    - 一個程序不能存取另一個程序專屬的記憶體空間。
    - 使用者程序之間可以共享記憶體。
    - 使用者程序的某些記憶體空間可以視唯獨的。
    - 透過磁碟交換，'系統可以使用比實際記憶體容量更多的記憶體空間。
- [MMU (Memory Management Unit，記憶體管理單元)](https://zh.wikipedia.org/wiki/%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86%E5%8D%95%E5%85%83)：
    - 使用了[虛擬記憶體 (Virtual Memory)](https://zh.wikipedia.org/wiki/%E8%99%9A%E6%8B%9F%E5%86%85%E5%AD%98) 機制：
        - 不是直接存取記憶體的實體位址。
        - 透過核心，使程序看起來可以使用證個系統的記憶體。
        - 當程序存取記憶體時，MMU截獲存取請求，然後會透過記憶體映射表將要存取的記憶體位址轉換為實際的實體位址。
        - 核心需初始化、維護和更新這個位址映射表。
> *記憶體位置映射透過記憶體頁表面 (Page Table) 來實現。*
> [color=#7EDBFF]

### 設備驅動程式和設備管理
- 設備只能在核心模式中被存取。
- 存取不當會讓系統崩潰。
- 不同設備間，沒有一個統一的程式設計介面 (即使同類設備也是)。
- 盡可能為使用者程序提供統一的介面，以簡化開發人員的工作。

### 系統呼叫和系統支援
- [系統呼叫 (System Call / Syscall)](https://zh.wikipedia.org/zh-tw/%E7%B3%BB%E7%B5%B1%E8%AA%BF%E7%94%A8)
    - 指運行在 user space 的程式向作業系統核心請求需要更高權限運行的服務。
    - 簡單來說，system call 是 process 和 OS 之間的介面。
    - 當使用者程式需要 OS 的服務時，使用者程式便去呼叫 system call。
    - [fork()](https://blog.gtwang.org/programming/c-fork-tutorial-multi-process-programming/)
        - 程序呼叫時，核心建立一個和該程序幾乎一模一樣的副本。
    - [exec()](https://www.cntofu.com/book/46/linux_system/linuxxi_tong_bian_cheng_zhi_jin_cheng_ff08_wu_ff09.md)
        - 程序呼叫 exec(program) 時，核心啟動 program 來替換目前的程序。
    - 除了 init 外，Linux 中的所有使用者程序都是透過 fork() 來執行的。
    - 可以用 exec() 來啟動新的程序。
    - 程序和系統呼叫的流程 (以 ls 為例)：
        ![](https://i.imgur.com/uuFPTCU.png)
    - 虛擬設備 (pseudodevices)：
        - 對於使用者程序而言是實體設備。
        - 其實是透過軟體來實現。
        - 從技術角度來說，它們並不需要存在於核心中。
        - 實際上很多都存在核心中。
    > [系統呼叫詳細介紹](https://hackmd.io/@combo-tw/Linux-%E8%AE%80%E6%9B%B8%E6%9C%83/%2F%40combo-tw%2FBJPoAcqQS)
    > [color=#7EDBFF]