---
title: xxd
tags: Linux
lang: zh-tw
---

{%hackmd theme-dark %}

[![hackmd-github-sync-badge](https://hackmd.io/EkxCjUItRHGP_jT1H7dbOA/badge)](https://hackmd.io/EkxCjUItRHGP_jT1H7dbOA)

# xxd --help
```shell=
Usage:
       xxd [options] [infile [outfile]]
    or
       xxd -r [-s [-]offset(抵銷)] [-c cols(列)] [-ps] [infile [outfile]]
Options:
    -a          toggle(切換) autoskip(自動跳過): A single '*' replaces nul-lines(單個 '*' 替換空行). Default off.
    -b          binary digit dump(轉儲) (incompatible(不相容) with -ps,-i,-r). Default hex.
    -C          capitalize variable names in C include file style (-i).
    -c cols     format <cols> octets per line.(每行顯示 <cols> 個字符) Default 16 (-i: 12, -ps: 30).
    -E          show characters in EBCDIC.(將右邊的 ASCii 轉成 EBCDIC 來顯示) Default ASCII.
    -e          little-endian dump (incompatible with -ps,-i,-r).(使用 little-endian 模式轉儲)
    -g          number of octets(字節) per group(每組) in normal output.(在正常輸出中，每<byte>個字符用空格隔開) Default 2 (-e: 4).
    -h          print this summary(簡介).
    -i          output in C include file style.(使用 C 語言文件樣式輸出)
    -l len      stop after <len> octets(字符).(只顯示 <len> 個字符)
    -o off      add <off> to the displayed file position.
    -ps         output in postscript plain hexdump style.(以 postscript 的連續十六進制轉儲輸出)
    -r          reverse operation: convert (or patch) hexdump into binary.
    -r -s off   revert(恢復) with <off> added to file positions found in hexdump.
    -s [+][-]seek  start at <seek> bytes abs.(從 <seek>字節的絕對偏移量開始) (or +: rel.) infile offset.
    -u          use upper case hex letters.(用大寫字母，預設是小寫字母)
    -v          show version: "xxd V1.10 27oct98 by Juergen Weigert".
```

# Normal Usage
- string to hex
    ```shell=
    # 直接轉換 file 裡的內容
    xxd file
    
    # 轉換 echo 出來的字串
    echo string | xxd
    
    # 直接轉換 file 裡的內容輸出到out_file
    xxd file out_file
    ```
- hex to string
    ```shell=
    # 直接轉換 file 裡的內容
    xxd -r file
    
    # 轉換 echo 出來的字串
    echo string | xxd -r
    
    # 直接轉換 file 裡的內容輸出到out_file
    xxd -r file out_file
    ```
    
# Return Values
- 0 => 一切正常
- -1 => 不支持此操作
- 1 => 解析選項錯誤
- 2 => 輸入文件出錯
- 3 => 輸出文件出錯
- 4, 5 => 指定的偏移量地址不可到達