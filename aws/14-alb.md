# Day28 ALB（Application Load Balancer）

## 環境
・AWS  
・VPC（10.0.0.0/16）  
・Publicサブネット ×2（異なるAZ）  
・Privateサブネット ×2（異なるAZ）  
・Internet Gateway  
・NAT Gateway  
・EC2（Amazon Linux 2 ×2）  
・ALB  

---

## 概要
Application Load Balancer（ALB）を構築し、  
複数のEC2インスタンスへトラフィックを分散する構成を作成する。

Privateサブネットに配置したEC2へ、  
ALBを経由して外部からアクセス可能であることを確認する。

---

## 作業手順

### 1. EC2（Private）を2台作成
・異なるAZに配置  
・パブリックIPなし  

---

### 2. Apacheインストール（両EC2）

sudo yum install httpd -y  
sudo systemctl start httpd  
sudo systemctl enable httpd  

---

### 3. 動作確認用ページ作成

EC2①  
echo "EC2-1" | sudo tee /var/www/html/index.html  

EC2②  
echo "EC2-2" | sudo tee /var/www/html/index.html  

---

### 4. ターゲットグループ作成
・タイプ：インスタンス  
・プロトコル：HTTP  
・ポート：80  
・EC2を2台登録  

---

### 5. ALB作成
・スキーム：インターネット向け  
・VPC：作成済みVPC  
・サブネット：Publicサブネットを2つ選択（異なるAZ）  
・セキュリティグループ：HTTP（80）許可  

---

### 6. リスナー設定
・HTTP:80  
・ターゲットグループを紐付け  

---

### 7. 動作確認
ALBのDNS名にアクセス  

ブラウザ更新で以下を確認  
・EC2-1  
・EC2-2  

交互に表示されることを確認  

---

## 確認

・ALB経由でEC2にアクセスできること  
・リロードで接続先が切り替わること  
・ターゲットグループのステータスがhealthyであること  

---

## 補足

・ALBはPublicサブネットに配置し、インターネットからの入口となる  
・EC2はPrivateサブネットに配置し、直接アクセスを防ぐ構成とした  
・ALBを経由することで、Private環境のEC2へ安全にアクセス可能  

---

## 学び

・ALBの役割（トラフィック分散）を理解  
・PrivateサブネットのEC2でもALB経由で公開可能であることを理解  
・単一構成ではなく冗長構成の重要性を理解  
・実務に近いWebアーキテクチャを構築できた  
