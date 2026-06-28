### 前準備１（必須）
自分のIPとホスト名を追加
```
hostname -i
nano /etc/hosts
```
---
### 前準備２（必須）
IP・NICの設定
[Network　ネットワーク　NIC](:/62b9233c457c49ab87bef3aae376650b)
これを実行しないとLISTENERの設定でコケる
忘れた場合、一度再起動すれば設定しなおせる。
続けてやろうとするとキャッシュが残っている為設定が中途半端になる。

---
### 前準備３（必須）
Desktopインストール
これはxフォrワーディングすれば不要

---
### 前準備４（有ると便利）
リードラインワードラッパー（rlwrap)のインストール

---
# インストール（オフィシャルのマニュアルで）
手順書：[pdf](:/f511e7f56e554676affb6e420b105c3e)

手順全て終わったら、ＯＳ再起動（書いてないけど）

---
## ＤＢ接続テスト
おそらく失敗する
```
$ sqlplus
ユーザー名を入力してください：sys@orcl as sysdba
ORA-12541: TNS: リスナーがありません

原因はＤＢが「startup」していない。
「lsnrctl」でインスタンスの起動を確認しても無駄
```
## ＤＢ起動
[ＤＢ起動](:/65cf787a4ad142e19d69eb6b6890c2e3)


## リスナ確認
[リスナー](:/6183635500714985941158274460a2db)

## リスナ経由でＤＢ起動
[プラガブル操作](:/3ff143ec5d7a4b72a9979ca4c278c67e)

---
### 追加情報
[DBサーバをCentOS化](:/27749e1b8f764840a9eb398c5fc7c264)
に手順のPDFがある
ヒントも少しある

---
## プラガブルを起動と連動させる
[起動と連動してPDB起動](:/b1666cd1a72b4867b53b5fd6065191e9)

---

### DB起動
インストール直後なら起動している
[URL](https://docs.oracle.com/cd/E57425_01/121/UNXAR/strt_stp.htm)
【下にも同じURLある】


### サーバー起動時にOracleを連動して起動する
```
touch /etc/init.d/dbora

こぴぺ
#! /bin/sh 
# description: Oracle auto start-stop script.
#
# Set ORACLE_HOME to be equivalent to the $ORACLE_HOME
# from which you wish to execute dbstart and dbshut;
#
# Set ORA_OWNER to the user id of the owner of the
# Oracle database in ORACLE_HOME.

ORA_HOME=<Type your ORACLE_HOME in full path here>
ORA_OWNER=<Type your Oracle account name here>

case "$1" in
'start')
    # Start the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    # Remove "&" if you don't want startup as a background process.
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
    touch /var/lock/subsys/dbora
    ;;

'stop')
    # Stop the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
    rm -f /var/lock/subsys/dbora
    ;;
esac


コマンド実行
# ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora

```
