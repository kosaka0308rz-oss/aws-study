# Day16 EC2 SSH接続

## 環境

Local : Windows PowerShell  
SSH Client : OpenSSH  
Cloud : AWS  
Service : EC2  
Instance Name : aws-study  
OS : Ubuntu Server 22.04 LTS 

## 実行コマンド

ssh -i aws-study-key.pem ubuntu@PublicIP

pwd
ls
uname -a

## トラブル対応

Issue  
Permission denied (publickey)

Cause  
SSH接続時、秘密鍵(.pem)のアクセス権限が広すぎると接続が拒否される。  
Windowsではダウンロードしたpemファイルに複数ユーザーのアクセス権が付与されることがある。

Fix  
icaclsコマンドで現在ユーザーの読み取り権限のみに変更。

```
$path = ".\aws-study-key.pem"

icacls $path /inheritance:r
icacls $path /grant:r "$($env:USERNAME):(R)"
```

## 学び

EC2インスタンスにはSSHで接続できる。  
SSHでは秘密鍵の権限管理が重要であり、権限が広すぎる場合は接続が拒否される。  
Windowsではicaclsを使用してアクセス権限を調整できる。
