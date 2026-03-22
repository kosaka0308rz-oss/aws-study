# Day25 Internet Gateway / Route Table

## 環境
・AWS  
・VPC（10.0.0.0/16）  
・Publicサブネット（10.0.1.0/24）  

## 概要
Internet GatewayとRoute Tableを設定し、
Publicサブネットをインターネットへ接続する。

ルーティングによってネットワークの性質（Public / Private）が決まることを理解する。

---

## 作業手順

### 1. Internet Gateway作成
1. VPC → Internet Gateway → 作成
2. 名前：my-igw

---

### 2. VPCへアタッチ
1. 作成したIGWを選択
2. アクション → VPCにアタッチ
3. my-vpc を選択

---

### 3. ルートテーブル作成
1. VPC → ルートテーブル → 作成
2. 名前：public-rt
3. VPC：my-vpc

---

### 4. ルート追加
ルートテーブルに以下を追加

    送信先：0.0.0.0/0
    ターゲット：Internet Gateway（my-igw）

---

### 5. サブネットに関連付け
1. ルートテーブル → サブネットの関連付け
2. public-subnet を選択

---

## 確認
・Internet GatewayがVPCにアタッチされていること  
・ルートテーブルに0.0.0.0/0が設定されていること  
・public-subnetにルートテーブルが関連付けされていること  

---

## 補足
・Internet Gatewayはインターネットとの接続口  
・0.0.0.0/0はすべての通信を意味する  
・ルートテーブルは通信の行き先を決定する  

・Publicサブネットとは  
「Internet Gatewayへのルートを持つサブネット」である  

・サブネット単体ではPublic / Privateは決まらない  

---

## 学び
・ネットワークの性質はルーティングによって決まる  
・Internet GatewayとRoute Tableの組み合わせが重要である  
・Publicサブネットの定義を理解した  
・通信経路を意識することがインフラ設計において重要であると理解した  
