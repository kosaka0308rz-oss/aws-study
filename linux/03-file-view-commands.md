# Day3 Linux ファイル閲覧コマンド

## 環境

OS : Ubuntu  
環境 : VirtualBox

## 実行コマンド

```bash
echo hello >> log.txt
echo aws >> log.txt
echo linux >> log.txt
cat log.txt
less log.txt
head log.txt
tail log.txt
tail -f log.txt
```

## 学び

catはファイルの内容をすべて表示するコマンド。  
lessはファイル内容をスクロールしながら確認できる。ログ確認でよく使われる。  
headはファイルの先頭部分を表示する。  
tailはファイルの末尾部分を表示する。  
tail -fはログファイルをリアルタイムで確認する際に使用される。
