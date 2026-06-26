# 新しいディレクトリを作成し、その中で仮想環境を構築する

testというディレクトリを作成する。

```bash
mkdir test
```

`cd`でtestディレクトリへ移動する。

```bash
cd test
```

`vagrant init`でVagrantfileを作成する。

```bash
vagrant init bento/ubuntu-24.04
```

Vagrantを起動する。

```bash
vagrant up
```

VagrantにSSH接続する。

```bash
vagrant ssh
```

# Webサーバーを立ち上げる

パッケージをインストールする前にupdateする。

```bash
sudo apt update
```

apache2をインストールする。

```bash
sudo apt install apache2 -y
```

`systemctl start`でapache2を起動する。

```bash
sudo systemctl start apache2
```

`systemctl status`で**active (running)**になっていることを確認する。

```bash
sudo systemctl status apache2
```

# /var/www/htmlにBasic認証をかける

パスワードファイルを作成し、ユーザーを登録する。今回はユーザー名を`vagrant`とする。

```bash
sudo htpasswd -c /etc/apache2/.htpasswd vagrant
```

実行後、パスワードを2回入力して登録する。

公開ディレクトリへ移動する。

```bash
cd /var/www/html/
```

`.htaccess`ファイルを作成し、以下の内容を入力する。

```bash
sudo vi .htaccess
```

```text
AuthType Basic
AuthName "Please type your ID and PW(Basic):"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user
```

Apacheのメイン設定ファイルを開く。

```bash
sudo vi /etc/apache2/apache2.conf
```

以下の内容を追記する。

```text
<Directory "/var/www/html">
    AllowOverride AuthConfig
</Directory>
```

Apacheを再起動する。

```bash
sudo systemctl restart apache2
```

Apacheが正常に起動していることを確認する。

```bash
sudo systemctl status apache2
```

# vsftpdサーバーを立ち上げる

最初にクライアント用の仮想マシンを立ち上げておく。

`vagrant init`でVagrantfileを作成する。

```bash
vagrant init bento/ubuntu-24.04
```

Vagrantを起動する。

```bash
vagrant up
```

VagrantにSSH接続する。

```bash
vagrant ssh
```

`ip a`でサーバー側のVagrantのIPアドレスと、クライアント側のVagrantのIPアドレスが同じネットワークに属しているか確認する。今回は同じなので変更なし。

## サーバー側

パッケージをインストールする前にupdateする。

```bash
sudo apt update
```

vsftpdをインストールする。

```bash
sudo apt install vsftpd -y
```

設定ファイルを開き、`listen=NO`になっていることを確認する。

```bash
sudo vi /etc/vsftpd.conf
```

`systemctl status`でvsftpdが起動していることを確認する。起動していなければ`restart`または`start`する。

```bash
sudo systemctl status vsftpd
```

## クライアント側

パッケージをインストールする前にupdateする。

```bash
sudo apt update
```

ftpをインストールする。

```bash
sudo apt install ftp -y
```

サーバーへ接続する。

```bash
ftp [サーバー側のIPアドレス]
```

ユーザー名とパスワードを入力する。（今回はどちらも`vagrant`）

# Telnetサーバーを立ち上げる

## サーバー側

パッケージをインストールする前にupdateする。

```bash
sudo apt update
```

xinetdとtelnetdをインストールする。

```bash
sudo apt install xinetd telnetd -y
```

設定ファイルを開く。

```bash
sudo vi /etc/xinetd.d/telnet
```

以下の内容を入力する。

```text
service telnet
{
    disable     = no
    flags       = REUSE
    socket_type = stream
    wait        = no
    user        = root
    server      = /usr/sbin/telnetd
    log_on_failure += USERID
}
```

xinetdを再起動して設定を反映させる。

```bash
sudo systemctl restart xinetd
```

`systemctl status`で起動していることを確認する。

```bash
sudo systemctl status xinetd
```

## クライアント側

パッケージをインストールする前にupdateする。

```bash
sudo apt update
```

Telnetをインストールする。

```bash
sudo apt install telnet -y
```

サーバーへ接続する。

```bash
telnet [サーバー側のIPアドレス]
```

ユーザー名とパスワードを入力する。（今回はどちらも`vagrant`）
