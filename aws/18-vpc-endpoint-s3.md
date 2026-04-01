# Day32 VPCエンドポイント（NATなし構成）

## 環境

Local : Windows PowerShell  
SSH Client : OpenSSH  
Cloud : AWS  
Service : VPC / EC2 / S3 / IAM  
Instance Name : private-ec2-a  
OS : Amazon Linux 2023  

---

## 概要

プライベートサブネットに配置されたEC2インスタンスから、インターネットを経由せずにS3へアクセスするため、VPCエンドポイント（Gateway Endpoint）を構築した。

NAT Gatewayを使用しない構成で、AWS内部ネットワークのみで通信を完結させる設計を理解することを目的とした。

---

## 作業手順

### 1. 前提確認（NATなし状態）

Private Subnetのルートテーブルにおいて、以下が存在しないことを確認した。

0.0.0.0/0 → NAT Gateway

この状態ではインターネットへは通信できない。

---

### 2. VPCエンドポイント作成

VPC → エンドポイント → 作成

以下の設定で作成した。

サービス名：
com.amazonaws.ap-northeast-1.s3

タイプ：
Gateway

VPC：
対象のVPCを選択

ルートテーブル：
Privateサブネットに関連付けられているルートテーブルを選択

ポリシー：
フルアクセス（デフォルト）

---

### 3. ルートテーブルの確認

エンドポイント作成後、ルートテーブルに以下が追加されていることを確認した。

宛先：
com.amazonaws.ap-northeast-1.s3

ターゲット：
vpce-xxxx

これにより、S3への通信はVPCエンドポイント経由となる。

---

### 4. IAMロールの前提

EC2には以下の最小権限ポリシーを付与済み。

・s3:ListBucket（特定バケット）  
・s3:GetObject（特定バケット内オブジェクト）  

※ s3:PutObject は未許可

---

### 5. 動作確認

#### バケット一覧（失敗）

aws s3 ls

結果：

AccessDenied（s3:ListAllMyBuckets の権限不足）

---

#### 特定バケットの一覧取得（成功）

aws s3 ls s3://"バケット名"

成功

---

#### オブジェクト取得（成功）

aws s3 cp s3://"バケット名"/test.txt .

結果：

download: s3://"バケット名"/test.txt to ./test.txt

---

## 確認

・Private SubnetからでもS3へアクセス可能であることを確認  
・インターネット（NAT Gateway）を経由していない構成であることを確認  
・IAMポリシーにより操作制限が正しく機能していることを確認  

---

## 補足

・S3のVPCエンドポイントはGatewayタイプであり、ルートテーブルに設定される  
・通信はAWS内部ネットワークで完結するため、インターネットは不要  
・NAT Gatewayは不要になるため、コスト削減につながる  

また、今回の検証で以下の違いを理解した。

aws s3 ls  
→ s3:ListAllMyBuckets が必要（全バケット対象）

aws s3 ls s3://バケット名  
→ s3:ListBucket が必要（特定バケット対象）

IAMの権限設計とCLIコマンドの動作が密接に関係していることを確認した。

---

## 学び

・VPCエンドポイントを利用することで、Private Subnetからでも安全にS3へアクセスできる  
・IAM（認証・認可）と通信経路（ネットワーク設計）は別の要素として考える必要がある  
・NAT Gatewayに依存しない構成はコスト・セキュリティ両面でメリットがある  
・最小権限設計では、操作単位（List / Get / Put）ごとに権限を明示的に付与する必要がある  
・「アクセスできない原因」が IAM かネットワークかを切り分ける力が重要であると理解した
