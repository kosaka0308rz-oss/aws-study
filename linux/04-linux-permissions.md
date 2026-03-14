# Day4 Linux 権限

## 環境

OS : Ubuntu  
環境 : VirtualBox  

## 実行コマンド

```bash
touch permission.txt
ls -l
chmod +x permission.txt
chmod 755 script.sh
sudo chown ubuntu permission.txt
sudo apt update
```

## 学び

ls -lでファイルの権限を確認できる。  
chmodでファイルの権限を変更できる。  
chownでファイルの所有者を変更できる。  
sudoは管理者権限でコマンドを実行する際に使用する。
