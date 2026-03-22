# Day27 EC2 Public / Private配置

## 環境
・AWS  
・VPC（10.0.0.0/16）  
・Publicサブネット（10.0.1.0/24）  
・Privateサブネット（10.0.2.0/24）  
・Internet Gateway  
・NAT Gateway  
・EC2（Amazon Linux 2）

---

## 概要
PublicサブネットとPrivateサブネットにそれぞれEC2を配置し、  
インターネット接続およびアクセス可否の違いを確認する。

---

## 作業手順

### 1. Public EC2作成
1. EC2 → インスタンス作成  
2. サブネット：Publicサブネット  
3. 自動パブリックIP：有効  
4. セキュリティグループ：SSH（22）許可  

---

### 2. Public EC2へ接続
ssh -i key.pem ec2-user@<Public IP>

---

### 3. Private EC2作成
1. EC2 → インスタンス作成  
2. サブネット：Privateサブネット  
3. パブリックIP：無効  
4. セキュリティグループ：SSH許可  

---

### 4. Private EC2へ直接接続確認
ssh -i key.pem ec2-user@<Private IP>

---

### 5. 踏み台サーバー経由で接続

#### 鍵ファイルをPublic EC2へ転送
scp -i key.pem key.pem ec2-user@<Public IP>:/home/ec2-user/

#### Public EC2へ接続
ssh -i key.pem ec2-user@<Public IP>  
chmod 400 key.pem

#### Private EC2へ接続
ssh -i key.pem ec2-user@<Private IP>

---

### 6. Private EC2からインターネット接続確認
curl https://google.com

---

## 確認

・Public EC2へSSH接続できること  
・Private EC2へ直接SSH接続できないこと  
・踏み台経由でPrivate EC2へ接続できること  
・Private EC2からインターネットへ接続できること  

---

## 補足

・Publicサブネットはインターネットから直接アクセス可能  
・Privateサブネットは外部から直接アクセス不可  
・NAT GatewayによりPrivateサブネットから外部通信が可能になる  
・踏み台サーバーを経由することでPrivateリソースへ安全にアクセス可能  

---

## 学び

・Public / Privateサブネットの役割の違いを理解  
・踏み台サーバー構成の基本を理解  
・NAT Gatewayのアウトバウンド通信の仕組みを理解  
・実務に近いネットワーク構成を再現できた  
