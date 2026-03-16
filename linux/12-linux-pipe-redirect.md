# Day12 Linux パイプとリダイレクト

## 環境

OS : Ubuntu  
環境 : VirtualBox  

## 実行コマンド

```bash
ls | less
ps aux | grep bash
ls > list.txt
cat list.txt
echo hello > test.txt
echo world >> test.txt
cat test.txt
```

## 学び

| を使うとコマンド結果を次のコマンドへ渡せる。  
> を使うとコマンド結果をファイルに保存できる。  
>> を使うとファイルに追記できる。
