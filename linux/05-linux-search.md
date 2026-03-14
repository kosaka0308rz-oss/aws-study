# Day5 Linux 検索コマンド

## 環境

OS : Ubuntu
環境 : VirtualBox

## 実行コマンド

```bash
echo "apple" > fruits.txt
echo "banana" >> fruits.txt
echo "apple juice" >> fruits.txt

cat fruits.txt
grep apple fruits.txt

touch sample1.txt
touch sample2.txt
find . -name "sample1.txt"
find . -name "*.txt"

history
```

## 学び

grepでファイル内の文字列検索ができる。
findでファイル検索ができる。
historyで過去に実行したコマンドを確認できる。
