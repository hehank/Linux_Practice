# base64 --help
- Usage: base64 [OPTION (選項)]... [FILE (檔案)]
- Base64 encode or decode FILE, or standard(標準) input, to standard output. (Base64 編碼或解碼檔案，或從標準輸入到標準輸出)
- With no FILE, or when FILE is -, read standard input. (如果沒有指定檔案或檔案名稱是「-」，則讀取標準輸入)
- Mandatory(必要的) arguments to long options are mandatory for short options too. (必要的參數長短皆適用)
    - -d, --decode => decode data (解碼資料)
    - -i, --ignore-garbage => when decoding, ignore non-alphabet characters (當解碼時，忽略不是字母的字元)
    - -w, --wrap=COLS => wrap encoded lines after COLS character (default 76). Use 0 to disable line wrapping(換行) (在指定的COLS字元數後自動換行(預設為76)。COLS=0不會換行)

    - --help => display this help and exit (顯示說明訊息並退出)
    - --version => output version information and exit (輸出版本資訊並退出)

# normal usage
## encode
- base64 [file] => 將一個檔案的內容編碼並輸出在shell
- echo "content" | base64 => 將content用base64編碼並輸出在shell
## decode
- base64 -d(--decode) [file] => 將一個檔案內容解碼並輸出在shell
- echo "content" | base64 -d(--decode) => 將content用base64解碼並輸出在shell