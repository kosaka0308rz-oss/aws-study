# Day21: EBS（Elastic Block Store）

## ■目的
EC2に追加ディスク（EBS）をアタッチし、マウントして利用できるようにする。

---

## ■実施内容

### ① EBSボリューム作成
- タイプ：gp2
- サイズ：8GB
- 暗号化：有効（aws/ebs）
- AZ：EC2と同じ（ap-northeast-1a）

---

### ② EC2へアタッチ
- デバイス名：/dev/xvdf

※Ubuntu環境ではNVMeデバイスとして認識されるため、実際のデバイス名は異なる

---

### ③ デバイス確認

    lsblk

出力例：

    NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    nvme0n1      259:0    0    8G  0 disk
    ├─nvme0n1p1  259:1    0  7.9G  0 part /
    ├─nvme0n1p14 259:2    0    4M  0 part
    └─nvme0n1p15 259:3    0  106M  0 part /boot/efi
    nvme1n1      259:4    0    8G  0 disk

- nvme0n1：ルートディスク
- nvme1n1：追加したEBS

---

### ④ フォーマット

    sudo mkfs -t ext4 /dev/nvme1n1

---

### ⑤ マウント

    sudo mkdir /data
    sudo mount /dev/nvme1n1 /data

---

### ⑥ マウント確認

    df -h

---

### ⑦ 書き込みテスト（エラー発生）

    cd /data
    echo "EBS test" > test.txt

エラー：
    Permission denied

---

### ⑧ 原因
マウント直後のディレクトリはroot所有のため、ubuntuユーザでは書き込み不可

---

### ⑨ 対応

    sudo chown ubuntu:ubuntu /data

---

### ⑩ 再テスト

    echo "EBS test" > test.txt
    cat test.txt

成功

---

## ■学んだこと

- EBSはEC2にアタッチ可能なブロックストレージ
- EC2とは独立して存在する（データ永続化）
- 同一AZでないとアタッチできない
- デバイス名は環境により変わる（NVMe）
- マウント後は権限設定が必要

---

## ■補足（面接対策）

- EBSはAZ単位のサービス
- EC2のルートディスクとは別に追加可能
- マウント後に適切な権限設定を行う必要がある
