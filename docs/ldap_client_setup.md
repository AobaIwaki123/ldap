# LDAPクライアントの設定

# 事前準備

- ldapクライアントのインストール

```bash
$ sudo apt update
$ sudo apt instal dialog -y
$ sudo apt install libnss-ldap libpam-ldap nscd -y
```

- インタラクティブに設定を入力していく
    - LDAP server Uniform Resource Identifier: `ldap://comnets.xxx.ts.net`
        - LDAPサーバーのドメイン名またはIP。DNSエラーでLDAPが動かなくなると良くないので、IPが推奨
    - Distinguished name of the search base:  `ou=users,dc=comnets,dc=xxx,dc=ts,dc=net`
        - LDAPサーバーに保存されているユーザー情報のうち、 `ou=users` に含まれるものを検索する
        - `ou` は任意なので、 `comnets` とかでもいい
    - LDAP version to use: `3`
        - LDAPのバージョンはその時の最新版を使う
    - Make local root Database admin: `Yes`
        - これを設定しないと、 `passwd` コマンドでLDAPのパスワードを変更できない
    - Does the LDAP database require login?: `No`
        - LDAPのデータを読むのにログインを要求したら不便なので、通常はNo
    - LDAP account for root: `cn=admin,dc=comnets,dc=xxx,dc=ts,dc=net`
        - LDAPサーバーのルートアカウントのユーザ名
    - LDAP root account password: `AhFKQeynF6Hr`
        - ルートアカウントのパスワード

# 設定の編集

## ユーザー、グループ、パスワードの参照先にldapを追加

- `nsswitch.conf` は、LinuxやUnix系システムにおける名前解決および情報検索の順序を定義する設定ファイル

```bash
$ sudo vim /etc/nsswitch.conf
```

```diff
- passwd:         files systemd
- group:          files systemd
- shadow:         files systemd
+ passwd:         compat ldap
+ group:          compat ldap
+ shadow:         compat ldap
```

## ログイン時にホームディレクトリを自動で生成する

- `/etc/skel` がデフォルトのディレクトリとして生成される
- PAM（**Pluggable Authentication Modules**）とは、LinuxやUnix系システムにおいて、ユーザー認証を柔軟かつ統一的に管理するためのモジュール化されたシステム

```diff
$ sudo vim /etc/pam.d/common-session
+ session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```

## ユーザーが `passwd` コマンドでパスワードを変更できるように修正

- `use_authtok` はパスワードを使いまわすためのオプションなので、これが有効だとパスワードを変更できません

```bash
$ sudo vim /etc/pam.d/common-password
```

```diff
- password	[success=1 user_unknown=ignore default=die]	pam_ldap.so use_authtok try_first_pass
+ password	[success=1 user_unknown=ignore default=die]	pam_ldap.so try_first_pass
```

## SSHでPAMを有効にする

### 以下の設定はセキュリティインシデントに直結するので慎重にお願いします

- sshの設定を修正

```diff
$ sudo vim /etc/ssh/sshd_config
```

以下の設定を確認してもしも異なれば修正する

```bash
# パスワード認証を可能にするときは、外部ネットワークに接続しないか
# match文などで内部ネットワークからのアクセスのみに制限していることを確認すること！！！
# 詳しくは渡部先生に聞いてください！！！
PasswordAuthentication yes 
UsePAM yes # これはあまり気にせずyesにして大丈夫です
```

sshの設定を変更した場合は、再起動する

```bash
sudo systemctl restart ssh
```

## nscd (Name Service Cache Daemon) の再起動

LDAPによる名前解決を行うためにnscdの再起動

> nscd（Name Service Cache Daemon）は、UNIXおよびLinuxベースのシステムで名前解決のためのキャッシュを提供するデーモンです。具体的には、ホスト名、ユーザー、グループ、パスワードなどの名前解決に関する情報をキャッシュし、システムのパフォーマンスを向上させる役割を果たします。 - ChatGPT
> 

```bash
sudo systemctl restart nscd
```

## 動作確認

SSHできるか試してみる

```bash
$ ssh {USER}@{LDAP_CLIANT_IP}
# LDAPクライアント上なら以下でもOK
$ ssh {USER}@localhost
```
