# Linux Commands
学習中に使用したLinuxコマンドのまとめ。

# ディレクトリ操作
pwd : 現在ディレクトリ確認
ls : ファイル一覧表示
cd : ディレクトリ移動

# ファイル操作
touch : ファイル作成
cp : コピー
mv : 移動 / 名前変更
rm : 削除

# ファイル閲覧
cat : 内容表示
less : ファイルをスクロール表示
head : 先頭行を表示
tail : 末尾行を表示
tail -f : リアルタイムログ表示

# 権限管理
chmod : 権限変更
chown : 所有者変更
sudo : 管理者権限で実行

# 検索
grep : 文字列検索
find : ファイル検索

# 履歴
history : コマンド履歴

# プロセス管理
ps : 実行中プロセス表示
ps aux : 実行プロセス表示（全ユーザー）
top : CPU / メモリ使用状況表示
kill : プロセス終了

# ネットワーク
ip : IPアドレス確認
ping : 通信確認
curl : HTTPリクエスト送信

# 環境変数
echo : 文字列 / 変数表示
env : 環境変数一覧表示
export : 環境変数設定

# パッケージ管理
apt update : パッケージ情報更新
apt upgrade : ソフト更新
apt install : ソフトインストール
apt remove : ソフト削除

# ログ確認
dmesg : カーネルログ表示
journalctl : systemdログ表示
journalctl -n : 最新ログ表示
journalctl -f : リアルタイムログ

# パイプ / リダイレクト
# パイプ / リダイレクト
`|` : コマンド結果を次のコマンドへ渡す  
`>` : ファイル上書き保存  
`>>` : ファイル追記  

# システム確認
top : CPU / プロセス確認
df -h : ディスク使用量
free -h : メモリ使用量
uptime : 稼働時間

# ユーザー / コマンド確認
whoami : 現在ユーザー確認
which : コマンドの場所確認
