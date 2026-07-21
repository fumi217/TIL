# WSL(Ubuntu)にMySQLをインストールした際の手順と詰まったこと
環境：Windows + WSL2(Ubuntu)

## 1. インストール
決まった場所にインストールされるため、`pwd`等でカレントディレクトリを気にする必要はない。

```bash
sudo apt update
sudo apt install mysql-server
Continue? [Y/n] => Y           //`Y`を入力してEnter
```

### ⚠️間違えた箇所
`Continue? [Y/n]`に対して`-y`と入力して`Abort`(中止)扱いになり、インストールされていなかった。  
`-y`はコマンド実行時に付けるオプション（`sudo apt install -y mysql-server`）であって、対話プロンプトへの回答ではない。
```bash
【Abortに気づかず、打ったコマンドとエラー】
sudo service mysql status
grep: /etc/init.d/mysql: No such file or directory
```
### 🟢解消した手順
もう一度`sudo apt install mysql-server`を打ち、`Continue? [Y/n]`に`Y`と答えた。

## 2. 起動確認
`service mysql status`を実行すると、(1)と似ているが少し異なるエラーが返ってきた。

```bash
sudo service mysql status
grep: /etc/init.d/mysql: No such file or directory
mysql: unrecognized service
```

### ⚠️エラーの原因
WSLでは`systemd`が**無効**になっているため、`service`コマンドが動かなかった。

```ログの内容
invoke-rc.d: unknown initscript, /etc/init.d/mysql not found.
Created symlink '/etc/systemd/system/multi-user.target.wants/mysql.service' → '/usr/lib/systemd/system/mysql.service'.
```

### 🟢解消した手順
**WSLでsystemdを有効化**した。

1. `cat /proc/1/comm`で状態を確認。  
   - `systemd`=> 有効
   - `init`   => 無効（こちらだった）
2. `sudo nano /etc/wsl.conf`を実行。  
   パスワードを入力し、ファイルを開く。
3. ファイルに以下の文言を追記する。
   ```ini
   [boot]
   systemd=true
   ```
4. 以下の手順でファイルを保存する。
   1. `Ctrl+O`(Write Out)で保存
   2. `Write to File: /etc/wsl.conf`と表示されるので、`Enter`
   3. `Ctrl+X`(Exit)で閉じる（ターミナル画面に戻る）
5. [任意] `cat /etc/wsl.conf`を実行し、追記した内容が反映されているか確認。
6. PowerShellかコマンドプロンプトを開き、`wsl --shutdown`を実行（Windows側でWSLを再起動する）。
7. Ubuntuを開き直して`cat /proc/1/comm`(1と同じコマンド)を実行。  
   `systemd`と表示されればOK！

## 3. 続・起動確認
MySQLサービスの状態を確認する。以下を実行し、パスワードを入力。
```bash
sudo systemctl status mysql
```
レスポンスの中に`Active: active (running)` `Status: "Server is operational"`と表示されていること（起動できたこと）を確認。

以下が表示されたままの場合は、`q`を押して元のプロンプトに戻る。
```bash
vice - MySQL Community Server...
ice - MySQL Community Server.
```

## 4. ログイン確認
以下を実行し、ログイン。
```bash
sudo mysql -u root
```

プロンプトが、ディレクトリから`mysql>`に変化したらログインが成功している証。  
`exit` (`mysql> exit`)と打つと、もとのプロンプトに戻る。

## 5. パスワードの設定
VSCodeからパスワードで接続したい場合は、下記のパスワード設定が必要。

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY '任意のパスワード';
```

- Ubuntu(WSL)のsudoパスワードとMySQLのパスワードは別物なので、同じにする必要はない。
- プロジェクトが異なれば、入力するパスワードも異なる（プロジェクトごとに別のユーザーを作り、そのユーザーに、そのプロジェクトのDBへのアクセス権を与えるため）。

## 次回以降、起動する時のコマンド
以下を実行し、(5)で決めたパスワードを入力する。  
※パスワード入力時、画面上には文字入力が反映されないが、しっかり打てている。心の目で打ち、Enterを押す👀

```bash
mysql -u root -p
```

## 参考：
  - [MySQL 8.4 Reference Manual: Native Pluggable Authentication](https://dev.mysql.com/doc/refman/8.4/en/native-pluggable-authentication.html)
  - [Ubuntu Server documentation: Install and configure a MySQL server](https://ubuntu.com/server/docs/how-to/databases/install-mysql/)

