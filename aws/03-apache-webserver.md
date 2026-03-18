# Day17 Apache Web Server

## 環境

Local : Windows PowerShell  
SSH Client : OpenSSH  
Cloud : AWS  
Service : EC2  
Instance Name : aws-study  
OS : Ubuntu Server 22.04 LTS  

---

## 概要

EC2インスタンスにApacheをインストールし、Webサーバーとして公開した。  
ブラウザからアクセスし、Apacheのデフォルトページが表示されることを確認した。  
また、アクセスログを確認してHTTP通信の流れを理解した。

---

## SSH接続

```bash
ssh -i aws-ec2-key.pem ubuntu@<EC2-Public-IP>
```

---

## パッケージ更新

```bash
sudo apt update
```

---

## Apacheインストール

```bash
sudo apt install apache2 -y
```

※ `-y` はインストール確認の `yes` を自動選択するオプション

---

## Apache状態確認

```bash
sudo systemctl status apache2
```

確認ポイント

```
Active: active (running)
```

Apacheサービスが正常に起動していることを確認。

---

## Apache自動起動設定

```bash
sudo systemctl enable apache2
```

設定確認

```bash
systemctl is-enabled apache2
```

結果

```
enabled
```

---

## プロセス確認

```bash
ps aux | grep apache2
```

確認ポイント

- `apache2` プロセスが起動していること
- `root` と `www-data` のプロセスが存在すること

Apacheは親プロセスと子プロセスで動作する。

---

## Webアクセス確認

ブラウザからEC2のパブリックIPへアクセス

```
http://<EC2-Public-IP>
```

以下のページが表示されることを確認

```
Apache2 Ubuntu Default Page
```

※セキュリティグループでHTTP（ポート80）の許可が必要

---

## Apacheログ確認

ログディレクトリへ移動

```bash
cd /var/log/apache2
```

ログ一覧確認

```bash
ls
```

主なログファイル

```
access.log
error.log
```

---

## リアルタイムログ監視

```bash
tail -f access.log
```

ブラウザからアクセスするとログが出力される。

例

```
xxx.xxx.xxx.xxx - - [18/Mar/2026:13:05:48 +0000] "GET / HTTP/1.1" 200 3460 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36"
```

ログ内容

| 項目 | 内容 |
|-----|-----|
| IPアドレス | アクセス元 |
| GET | HTTPメソッド |
| / | アクセスURL |
| HTTP/1.1 | プロトコル |
| 200 | 正常レスポンス |

---

## 学び

EC2はLinuxサーバーであり、ソフトウェアをインストールすることで様々な用途のサーバーとして利用できる。

ApacheをインストールすることでWebサーバーとして動作する。

ブラウザからのHTTPアクセスはApacheに届き、アクセスログとして記録される。
