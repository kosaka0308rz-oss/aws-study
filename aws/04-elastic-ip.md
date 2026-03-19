# Day18 Elastic IP

## 環境

Local : Windows PowerShell  
SSH Client : OpenSSH  
Cloud : AWS  
Service : EC2 / Elastic IP  
Instance Name : aws-study  
OS : Ubuntu Server 22.04 LTS  

---

## 概要

EC2インスタンスにElastic IPを割り当て、パブリックIPを固定化した。  

通常、EC2は停止→起動するとパブリックIPが変更されるが、Elastic IPを使用することで同じIPアドレスを維持できる。  

これにより、Webサーバーなどの公開サービスで安定したアクセスが可能になる。

---

## Elastic IPとは

Elastic IPはAWSが提供する固定グローバルIPアドレス。  

EC2に関連付けることで、インスタンスを再起動してもIPが変わらなくなる。

---

## 作業手順

### Elastic IPの割り当て

AWSコンソール  

EC2  
→ Elastic IP  
→ 「Elastic IPアドレスを割り当て」  

---

### EC2へ関連付け

Elastic IP一覧  
→ アクション  
→ 「Elastic IPアドレスの関連付け」  

対象インスタンス  
aws-study
を選択

---

## 確認

ブラウザからElastic IPへアクセス
http://<Elastic-IP>

Apache2 Ubuntu Default Pageが表示された


---

## 動作確認（IP固定の確認）

1 EC2インスタンスを停止  

2 再度起動  

3 パブリックIP確認  

結果  
Elastic IPは変わらない


---

## 補足

通常のパブリックIPは動的に割り当てられるため、再起動で変更される。  

Elastic IPを使用することで、DNS設定や外部接続先を変更する必要がなくなる。

---

## 学び

EC2のパブリックIPはデフォルトでは固定ではない。  

Elastic IPを使用することで、インスタンスの再起動後も同じIPを維持できる。  

WebサービスではIPアドレスが変わるとアクセスできなくなる可能性があるため、固定IPの利用が重要である。
