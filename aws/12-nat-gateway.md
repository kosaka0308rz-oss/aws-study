# Day26 NAT Gateway

## 環境
・AWS  
・VPC（10.0.0.0/16）  
・Publicサブネット（10.0.1.0/24）  
・Privateサブネット（10.0.2.0/24）  

## 概要
NAT Gatewayを構築し、
Privateサブネットからインターネットへのアウトバウンド通信を可能にする。

外部からのアクセスは遮断しつつ、外部への通信のみ許可する構成を理解する。

---

## 作業手順

### 1. Elastic IP作成
1. EC2 → Elastic IP → 割り当て

---

### 2. NAT Gateway作成
1. VPC → NAT Gateway → 作成
2. 名前：my-nat
3. サブネット：public-subnet
4. Elastic IP：作成したものを選択

---

### 3. 作成確認
・ステータス：Available

---

### 4. ルートテーブル作成（Private用）
1. VPC → ルートテーブル → 作成
2. 名前：private-rt
3. VPC：my-vpc

---

### 5. ルート追加
ルートテーブルに以下を追加

    送信先：0.0.0.0/0
    ターゲット：NAT Gateway

---

### 6. サブネットに関連付け
1. ルートテーブル → サブネットの関連付け
2. private-subnet を選択

---

## 確認
・NAT Gatewayが作成されていること  
・Privateサブネットにルートテーブルが関連付けされていること  
・0.0.0.0/0がNAT Gatewayに向いていること  

---

## 補足
・NAT GatewayはPrivateサブネットからインターネットへの通信を可能にする  
・外部からPrivateサブネットへの直接アクセスはできない  
・NAT GatewayはPublicサブネットに配置する必要がある  
・Elastic IPは固定IPとしてNATに割り当てる  

通信の流れ：

Private EC2  
↓  
Route Table（0.0.0.0/0）  
↓  
NAT Gateway  
↓  
Internet Gateway  
↓  
Internet  

---

## 学び
・Privateサブネットでもインターネット通信が可能になる仕組みを理解した  
・セキュリティを維持しながら外部通信を行う設計が重要である  
・NAT Gatewayはアウトバウンド専用であることを理解した  
・ネットワーク設計において通信経路を意識する重要性を理解した  
