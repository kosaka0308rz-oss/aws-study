# Day7 Linux ログ調査

## 環境

OS : Ubuntu  
環境 : VirtualBox  

## 実行コマンド

```bash
echo "INFO Server start" > app.log
echo "INFO User login" >> app.log
echo "ERROR Database connection failed" >> app.log
echo "INFO Request completed" >> app.log

cat app.log
tail app.log
tail -f app.log
grep ERROR app.log
```

## 学び

tailでログファイルの末尾を確認できる。  
tail -fでログをリアルタイム監視できる。  
grepと組み合わせることでエラーログを検索できる。
