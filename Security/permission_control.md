---
title: 許可權控制
tags: Linux
lang: zh-tw
---

[![hackmd-github-sync-badge](https://hackmd.io/t4lX0A9oQReqhwHdkty7uQ/badge)](https://hackmd.io/t4lX0A9oQReqhwHdkty7uQ)

{%hackmd theme-dark %}

# 簡介
- Linux 下常見的許可權控制機制
    - DAC
    - MAC
- 其實本質上的模型都是差不多的，參與的物件有3種
    - 主體(subject)
    - 客體(object)
    - 規則(policy)
- 

# DAC
![](https://i.imgur.com/d2jYAZV.png)

- 英文全名：Discretionary Access Control
- 中文全名：自主存取控制
- Linux 上最初的安全模型
- 

# MAC
- 英文全名：Mandatory Access Control
- 中文全名：強制存取控制
- SELinux

# UID
### 簡介
- 英文全名：user identifier
- 中文全名：使用者識別碼
- 在`類 UNIX 系統`中是核心用來`辨識使用者`的一個`無符號整型數值`
- 是 UNIX 檔案系統與行程的必要組成部分之一

### 數值範圍
- 不同的系統中，UID的值的範圍也有所不同。
- 一般來說 UID 都是由一個`15位的整數`表示，其範圍在0～32767之內。
- 有些特殊的系統也支援 16 位元的 UID，因而 UID 的數目可以擴充到 65536 個。
- 現代系統支援 32 位元的 UID，這也使 UID 數目可能可以擴充到 4,294,967,296 個。

#### 限制
- root 的 UID 總為0
- 按`傳統`的做法，`nobody`（類UNIX系統的一種特殊帳戶，與 root 相反），占有`數值最大的PID`，即32767。
- `現今`的系統為 `nobody` 分配的UID則在`系統保留範圍`（1～100）或是 `65530-65535` 的範圍內。
- 數值於 `1～100` 內的 UID 約定預留給系統使用。
- 有些手冊則推薦在此基礎上再預留 `101～499`（RHEL） 甚至是 `101～999`（Debian）的 UID 以作備用。
- 在 Linux 中用 useradd 命令建立`第一個使用者`時，`預設`為之分配的 UID 則為 `1000`。

### 分類
#### EUID
- 英文全名：Effective UID。
- 中文全名：有效使用者 ID。
- 與 EGID 在建立與存取檔案的時候發揮作用。
- 建立檔案時，kernel 將根據`建立檔案`的 `process` 的 `EUID 與 EGID` 設定檔案的`所有者 / 組屬性`
- 存取檔案時，kernel 亦根據`存取 process` 的 EUID 與 EGID 決定其`能否`存取檔案。

#### RUID
- 英文全名：Real UID
- 中文全名：真實使用者 ID
- 與 RGID 用於辨識行程的真正所有者，且會影響到行程傳送訊號的權限。
- 沒有 `root` 權限的 process 僅在`其 RUID` 與`目標行程的 RUID` `相匹配時`才能向目標 process 傳送訊號。
- Ex：在`父子 process` 間，子 process 從父 process 處`繼承`了`認證資訊`，使得父子 process 間可以互相傳送訊號。

#### SUID
- 英文全名：Saved UID
- 中文全名：暫存使用者 ID
- 於以`提升權限執行的 process` 暫時需要做一些`不需特權`的操作時使用。
- 這種情況下 process 會`暫時`將自己的 EUID 從特權使用者（常為 root）對應的 UID 變為某個`非特權使用者`對應的 UID，而後將`原有`的特權使用者 UID `複製`為 SUID `暫存`。
- 之後當 process 完成`不需特權`的操作後，process 使用 SUID 的值`重設` EUID 以重新獲得特權。

#### FSUID
- 英文全名：File System UID
- 中文全名：檔案系統使用者 ID
- 只用於對`檔案系統`的`存取權限`控制，在沒有明確設定的情況下與 EUID 相同，且 EUID 改變也會影響到 FSUID。
  > [color=#E6BB45]*PS. 若 FSUID 為 root 的 UID，則 SUID、RUID 與 EUID 必需至少有一個為 root 的 UID。*
- FSUID 的目的是允許 process（例如，NFS 服務器）將自己限制為某些指定 uid 的文件系統權限，而不授予該 uid 向它們發送信號的權限。
- 在 2.0 版本之後的 kernel ，不再需要 FSUID，因為支援 SUSv3 規則。

# GID

# 檔案的特殊屬性