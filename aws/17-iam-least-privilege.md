# Day31 IAM深掘り（最小権限）

## 環境

Local : Windows PowerShell  
SSH Client : OpenSSH  
Cloud : AWS  
Service : IAM / EC2 / S3  
Instance Name : private-ec2-a  
OS : Amazon Linux 2023  

---

## 概要

IAMの最小権限の原則を理解するため、特定のS3バケットに対して読み取りのみを許可するカスタムIAMポリシーを作成し、EC2にアタッチしたIAMロールへ適用した。

今回は以下を確認した。

・特定バケットのみを対象にできること  
・GetObject と ListBucket を分けて設定する必要があること  
・PutObject を許可しなければアップロードできないこと  
・IAMロールの利用自体はインターネット不要だが、AWS APIへ到達する通信経路は必要であること  

---

## 作業手順

### 1. 最小権限ポリシーを作成

IAM → ポリシー → 作成 → JSON を選択し、特定のS3バケットだけを読み取り可能にするポリシーを作成した。

ポリシー内容：

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListSpecificBucket",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::"バケット名""
    },
    {
      "Sid": "GetObjectsFromSpecificBucket",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:"バケット名"/*"
    }
  ]
}

作成したポリシー例：

s3-readonly-specific-bucket

---

### 2. IAMロールへアタッチ

EC2に関連付けている IAM ロール `ec2-s3-role` に、上記カスタムポリシーをアタッチした。

---

### 3. IAMロール認識の確認

EC2インスタンスにIAMロールは付いていたが、最初は認証確認で詰まったため、インスタンスメタデータサービス（IMDS）を確認した。

IMDSv2でトークンを取得：

TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

その後、IMDSv2トークンを使ってロール情報を取得する流れを確認した。

---

### 4. 通信経路の確認

作業はPrivate Subnet上のEC2で実施していたため、最初は `aws sts get-caller-identity` などのAWS CLIコマンドが正常に返らなかった。

原因は、IAMロールそのものではなく、Private SubnetからAWSサービスへ到達するための通信経路が不足していたことだった。

今回理解したポイント：

・IAMロールのアタッチ自体にインターネットは不要  
・EC2はIMDSから一時認証情報を取得できる  
・ただし STS や S3 などのAWS APIへアクセスするには通信経路が必要  
・Private Subnetでは NAT Gateway または VPC Endpoint が必要になる  

今回は NAT Gateway を前提とした構成で確認を進めた。

---

### 5. AWS CLIで動作確認

まず、対象バケットのファイルをダウンロードした。

aws s3 cp s3://"バケット名"/test.txt .

結果：

download: s3://"バケット名"/test.txt to ./test.txt

ローカル確認：

ls

結果：

test.txt

---

## 確認

### 1. 読み取り操作は成功

以下の操作は成功した。

aws s3 cp s3://"バケット名"/test.txt .

確認できたこと：

・対象バケット内のオブジェクト取得が可能  
・作成したポリシーの `s3:GetObject` が有効に機能している  

---

### 2. 書き込み操作は拒否

以下の操作を実行した。

aws s3 cp ./test.txt s3://"バケット名"/

結果：

upload failed: ～～

確認できたこと：

・`s3:PutObject` を許可していないためアップロードは拒否された  
・最小権限ポリシーが意図通り動作していることを確認できた  

---

## 補足

・`s3:ListBucket` はバケット自体に対する権限  
・`s3:GetObject` はバケット内オブジェクトに対する権限  
・そのため Resource のARNは以下のように分ける必要がある  

バケット：
arn:aws:s3:::"バケット名"

オブジェクト：
arn:aws:s3:::"バケット名"/*

また、今回の確認を通して以下も理解できた。

・IAMロールの取得はEC2のメタデータサービス経由で行われる  
・この処理はローカル通信のためインターネット不要  
・一方で STS や S3 へのAPI呼び出しはAWS側サービスへの通信になるため、Private Subnetでは NAT Gateway や VPC Endpoint が必要になる  

---

## 学び

・IAMは「使えるようにする」だけでなく「必要最小限だけ許可する」設計が重要  
・AWS管理ポリシーよりも、対象サービス・対象操作・対象リソースを絞ったカスタムポリシーの方が実務的  
・`GetObject` と `PutObject` は別権限であり、明示的に許可しない限り書き込みはできない  
・Private Subnetでは、IAMロールがあってもAWS APIへ到達する通信経路がなければCLI操作は成立しない  
・「IAMロール」と「AWSサービスへの通信経路」は別の論点として考える必要があると理解した
