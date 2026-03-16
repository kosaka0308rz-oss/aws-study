# Day11 Linux ログ確認

## 環境

OS : Ubuntu  
環境 : VirtualBox  

## 実行コマンド

```bash
dmesg
dmesg | grep error
journalctl
journalctl -n 20
journalctl -f
```

## 学び

dmesgでカーネルログを確認できる。  
journalctlでsystemdのログを確認できる。  
journalctl -nで最新ログを確認できる。  
journalctl -fでリアルタイムログ確認ができる。
