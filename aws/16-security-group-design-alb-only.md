# Day30 セキュリティグループ設計（ALB経由のみ許可）

## 環境

Local : Windows PowerShell  
SSH Client : OpenSSH  
Cloud : AWS  
Service : EC2 / ALB / VPC  
Instance Name : aws-study  
OS : Ubuntu Server 22.04 LTS  

---

## 概要

EC2インスタンスをインターネットから直接アクセスできない構成に変更し、  
ALB（Application Load Balancer）経由のみ通信を許可するセキュリティ設計を実施した。

セキュリティグループを用いて通信元を制限することで、  
実務で一般的な「入口を限定した安全な構成」を構築する。

---

## 作業手順

### 1. ALB用セキュリティグループ作成

EC2 → セキュリティグループ → 作成

名前：alb-sg

インバウンドルール：

タイプ：HTTP  
ポート：80  
ソース：0.0.0.0/0  

タイプ：HTTPS  
ポート：443  
ソース：0.0.0.0/0  

---

### 2. EC2用セキュリティグループ修正

EC2に設定されているセキュリティグループを編集

修正前：

タイプ：HTTP  
ポート：80  
ソース：0.0.0.0/0  

修正後：

タイプ：HTTP  
ポート：80  
ソース：alb-sg（ALBのセキュリティグループID）  

---

### 3. SSHアクセス制御

タイプ：SSH  
ポート：22  
ソース：自分のグローバルIP  

---

### 4. ALBのセキュリティグループ確認

ALBに「alb-sg」が適用されていることを確認

---

## 確認

### 1. ALB経由アクセス

http://<ALBのDNS名>

→ Webページが表示されることを確認

---

### 2. EC2直接アクセス

http://<EC2のパブリックIP>

→ 接続できないことを確認

---

## 補足

・セキュリティグループはステートフル  
　→ 返信トラフィックは自動許可される  

・ALBを経由することで以下が可能  
　・負荷分散  
　・WAF連携  
　・HTTPS終端  

・実務ではEC2はPrivateサブネットに配置し、  
　外部公開はALBのみとする構成が基本  

---

## 学び

・通信元を制御することでセキュリティを強化できる  
・EC2を直接公開しない設計が重要  
・ALBを入口にすることで拡張性と安全性が向上する  
・実務では「許可する通信だけ通す」設計が基本である

## 構成図

```mermaid
flowchart TB
    User[User / Browser] -->|HTTP/HTTPS| ALB[ALB]

    subgraph AWS Cloud
        subgraph VPC[10.0.0.0/16]
            subgraph Public Subnet
                ALB
                ALB_SG[Security Group<br/>alb-sg<br/>80,443 from 0.0.0.0/0]
            end

            subgraph Private Subnet
                EC2[EC2 Apache]
                EC2_SG[Security Group<br/>ec2-sg<br/>80 from alb-sg<br/>22 from My IP]
            end
        end
    end

    ALB -->|Forward 80| EC2
    ALB_SG -. attached .- ALB
    EC2_SG -. attached .- EC2

    User -. direct access NG .-> EC2
