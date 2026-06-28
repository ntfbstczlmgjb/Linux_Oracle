# Linux_Oracle
Oracle Database installation guide on CentOS Linux
# インストール
オフィシャルから「5.7.15」をダウンロード「Red Hat Enterprise Linux / Oracle Linux」
・common
・libs
・client
・server
の4つあればインストールできる。

状況にもよるけど、conflict(競合)が発生したら「--replacefiles」で置き換えする
なぜ発生するかは不明。インストールされていないはずなのに・・・

```
# rpm -ivh --replacefiles パッケージ
```

# 設定
```
# mysql_secure_installation
```
rootのパスワードはログを確認
```
# nano /var/log/mysqld.log
```
キーワード「temporary password」

ログイン後、rootのパスワード変更
条件、８文字以上、大文字、小文字、数字、記号

あとは質問に「Yes/No」で完了

## ログイン
```
# mysql -u root -p
```
ポスグレみたいにsuしなくていいの？
なんで？

## ユーザー作成
本番はデータ移行するので必要ないかも、でもDB初期化でrootのリモートログイン否にしたのでテスト接続用にユーザーを作る

```
create user ユーザー名 identified by 'パスワード'
```

```
grant all on *.* to ユーザー 
```

## パスワード制限の変更
```
mysql> show variables like 'validate_password%'
```
で確認
```
パスワードのレベル変更
mysql> set global validate_password_policy=low;
```
で変更


## iptable追加・ファイヤーウォール追加
```
# nano /etc/sysconfig/iptables
```
ポート：3306

再起動
```
# /etc/rc.d/init.d/iptables restart
```

# データのダンプ・エクスポート
```
mysqldump -u root -p -h localhost -A > d:\mysql.dmp
```

# データのロード
```
mysql -u root -p DB名 < ダンプファイル
```
ロード完了後、DB再起動

ダンプとロードでコマンドが違う
