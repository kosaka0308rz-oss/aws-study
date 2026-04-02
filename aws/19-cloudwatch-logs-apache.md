# Day33 CloudWatch Logs（ログ収集）

## 環境

Local : Windows PowerShell  
SSH Client : OpenSSH  
Cloud : AWS  
Service : EC2 / IAM / CloudWatch Logs  
Instance Name : private-ec2-a  
OS : Amazon Linux 2023  

---

## 概要

EC2上のApacheアクセスログをCloudWatch Logsへ送信し、サーバー内だけでなくAWS上でログを一元管理できる構成を確認した。

今回はCloudWatch Agentを利用して、ApacheのアクセスログをCloudWatch Logsへ転送する構成を作成した。  
あわせて、Agent導入時の設定ファイル不備や、Private Subnet環境での通信経路の考え方も確認した。

---

## 作業手順

### 1. IAMロールにCloudWatch権限を追加

EC2にアタッチしているIAMロールへ、CloudWatch Logs送信用の権限を追加した。

追加ポリシー：

CloudWatchAgentServerPolicy

---

### 2. CloudWatch Agentをインストール

EC2へCloudWatch Agentをインストールした。

sudo yum install amazon-cloudwatch-agent -y

---

### 3. 監視対象ログを確認

最初はUbuntu系のログパスを想定していたが、今回のOSは Amazon Linux 2023 だったため、Apacheログのパスは以下だった。

/var/log/httpd/access_log

この時点で、OSごとにログ保存先が異なることを確認した。

---

### 4. CloudWatch Agent設定

当初は config wizard を使って設定を作成したが、不要な collectd / socket_listener 関連設定が含まれ、Agent起動に失敗した。

エラー内容：

error parsing socket_listener, open /usr/share/collectd/types.db: no such file or directory

そのため、今回はログ収集だけに絞った最小構成の設定ファイルを手動で作成した。

設定ファイル作成：

sudo tee /opt/aws/amazon-cloudwatch-agent/bin/config.json > /dev/null <<'EOF'
{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/httpd/access_log",
            "log_group_name": "apache-access-log",
            "log_stream_name": "{instance_id}",
            "retention_in_days": 7
          }
        ]
      }
    }
  }
}
EOF

---

### 5. CloudWatch Agentを起動

作成した設定ファイルを読み込ませてAgentを起動した。

sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
-s

---

### 6. Agent状態確認

以下のコマンドでAgentの起動状態を確認した。

sudo systemctl status amazon-cloudwatch-agent

結果：

active (running)

これにより、CloudWatch Agent が正常起動していることを確認できた。

---

## 確認

・CloudWatch Agent が active (running) で起動していることを確認  
・Apacheログの監視対象が `/var/log/httpd/access_log` であることを確認  
・設定ファイルを最小構成にすることで、不要設定による起動失敗を解消できた  

必要に応じて、ブラウザからALB経由でApacheへアクセスし、CloudWatch Logs のロググループ `apache-access-log` にログが反映されることを確認する。

---

## 補足

### 1. ログパスはOSによって異なる

Ubuntu系では以下が一般的：

/var/log/apache2/access.log

Amazon Linux系では以下が一般的：

/var/log/httpd/access_log

同じApacheでもOSによってパスが異なるため、実機で確認することが重要。

---

### 2. config wizard は便利だが不要設定が混ざることがある

今回は wizard 実行後、以下のエラーでAgentが起動しなかった。

error parsing socket_listener, open /usr/share/collectd/types.db: no such file or directory

これは、ログ収集だけで十分な場面にもかかわらず、collectd関連設定が含まれていたことが原因だった。  
そのため、必要最低限のJSON設定を手動で作る方が確実だった。

---

### 3. CloudWatch Logs はS3 Endpointでは到達できない

前日までにS3 Gateway Endpointを作成していたが、CloudWatch Logs は別サービスのため、それだけでは通信できない。  
CloudWatch Logs へ送るには、以下のいずれかが必要となる。

・NAT Gateway  
・CloudWatch Logs 用 Interface Endpoint  

S3 と CloudWatch では必要な通信経路が異なることを理解した。

---

## 学び

・EC2のログはローカル保存だけでなく、CloudWatch Logsへ集約することで運用性が向上する  
・OSごとにログパスは異なるため、思い込みではなく実機確認が重要  
・CloudWatch Agentはwizard任せではなく、必要な設定だけに絞ると安定しやすい  
・IAM権限だけでなく、CloudWatch Logsまでの通信経路も必要  
・S3 Gateway EndpointとCloudWatch Logsの通信要件は異なり、サービスごとに設計を分けて考える必要がある  
・「動かない理由」を設定ミス、権限不足、通信経路不足に切り分けることが運用では重要だと理解した
