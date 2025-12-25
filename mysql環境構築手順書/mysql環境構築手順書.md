# MySQL環境構築手順

<!-- omit in toc -->
## はじめに
本書ではmysqlのインストール及び環境構築手順について記す。  
mysqlはオープンソースのRDBMSソフトウェアである。  

<!-- omit in toc -->
## 目次
- [実行環境](#実行環境)
- [MySQLの設定](#mysqlの設定)
  - [インストール](#インストール)
  - [環境変数の設定](#環境変数の設定)
  - [初期設定](#初期設定)
- [SQLクライアントツール（A5M2）の設定](#sqlクライアントツールa5m2の設定)


## 実行環境
- Windows11（64ビット）  
- mysql v8.0.21  
- A5M2(A5:SQL Mk-2) v2.20.4  

## MySQLの設定
### インストール
1. 以下のURLからArchiveを選択し、v8.0.21のインストーラーをダウンロードする。(mysql-installer-community-8.0.21.0.msi)  

https://dev.mysql.com/downloads/installer/  

2. ダウンロードしたmsiをダブルクリックし実行する。  
3. Choosing a Setup Type の画面で「Developer Default」を選択する。  
4. 基本は初期設定のままでよいので、画面の指示に従ってExecute, Next, Finishをクリックしていく。  
5. Connect To Server の画面で管理者名、管理者パスワードを設定できるので、以下のように設定する。  
- User name: root  
- Password:  root  
6. 画面の指示に従ってNext, Finishを押す。「Start MySQL Workbensh after setup」「Stat MySQL Shell after setup」のチェックは外しておく。  

### 環境変数の設定
1/ システムの環境変数「Path」に先ほどインストールした以下を追加する。  
- C:\Program Files\MySQL\MySQL Server 8.0\bin

### 初期設定
ここでは、コマンドプロンプトを利用してMySQLの初期設定を行う。  
なお、コマンド中の以下の値を変更することで他の設定にも応用可能である。
- root　    ：管理者ユーザー名
- root　    ：管理者ユーザーのパスワード
- test_user ：テスト用ユーザー
- test_pass ：テスト用ユーザーのパスワード
- test_db   ：テスト用データベースの名前

1. MySQLのバージョン確認  
コマンドプロンプトを立ち上げ、以下を実行する。
~~~
mysql --version
~~~

2. MySQLへの管理者ユーザーでのログイン  
以下を実行すると、パスワードの入力を求められるので以下のパスワードを入力する。  
~~~
ログイン実行: "mysql -u root -p"  
パスワード: "root"  
~~~

3. ユーザーの作成  
管理者ユーザーでMySQLにログインした状態で以下を実行する。  
~~~
create user test_user@localhost identified by 'test_pass';
~~~  

4. ユーザーの確認  
管理者ユーザーでMySQLにログインした状態で以下を実行する。  
~~~
select Host,User from mysql.user;
~~~
5. データベース（スキーマ）の作成  
管理者ユーザーでMySQLにログインした状態で以下を実行する。  
~~~
create database test_db default character set utf8;
~~~

6. データベース（スキーマ）の確認  
管理者ユーザーでMySQLにログインした状態で以下を実行する。  
~~~
show databeses;
~~~

7. ユーザーへのアクセス権限の付与  
管理者ユーザーでMySQLにログインした状態で以下を実行する。  
~~~
grant all privileges on test_db.* to test_user@localhost;
~~~

8. ユーザーへのアクセス権限の確認  
管理者ユーザーでMySQLにログインした状態で以下を実行する。  
~~~
show grants for test_user@localhost;
~~~  

9. 認証プラグインの変更  
管理者ユーザーでMySQLにログインした状態で以下を実行する。  
~~~
alter user test_user@loaclhost identified with mysql_native_password by 'test_pass';
~~~  
※バージョン8.0からデフォルトの認証プラグインが「caching_sha2_password」に変更になっているが、MySQLに接続するツールの多くがこれをサポートしていないため、以前までデフォルトだった「mysql_native_password」に設定を戻す。  

10. 認証プラグインの確認  
管理者ユーザーでMySQLにログインした状態で以下を実行する。  
~~~
select User,Plugin from mysql.user;
~~~  

11. MySQLからのログアウト  
MySQLにログインした状態で以下を実行する。  
~~~
quit
~~~  

## SQLクライアントツール（A5M2）の設定
1. 以下のURLから「Vectorからダウンロード」をクリックする。  

https://a5m2.mmatsubara.com/

2. A5:SQL Mk-2(x64 edition)2.20.4の「ダウンロードページへ」をクリックする。

3. 「このソフトを今すぐダウンロード」をクリックする。

4. ダウンロードされたzipファイルを解凍する。

5. 解凍したフォルダ(a5m2_2.20.4_x64)を任意の場所に移動する。  
例) C:\ForDevelop\a5m2_2.20.4_x64  

6. 解凍したフォルダの中にあるA5M2.exeのショートカットを作成する。（任意）

7. A5M2.exeを実行する

8. 起動すると「データベースの追加と削除」ウィンドウが立ち上がるので「追加」をクリックする。

9. 以下の情報を入力する。  
- ホスト名：localhost  
- ユーザーID：test_user  
- パスワード：test_pass  
- データベース：test_db  
- キャラクタセット：utf8mb4  

10. 「テスト接続」をクリックしOKであれば、「OK」をクリックする。

11. データベース別名を求められるので「sample_test_db」などとする。

12. 画面左側に先ほど設定したデータベース（sample_test_db）が表示されるので、それをクリックし開く。  

13. パスワードを求められるので「test_pass」と入力し、接続する。  
