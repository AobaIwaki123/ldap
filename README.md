# LDAPサーバー

## 準備

```sh
$ mkdir ldap_data
$ chown $USER:docker ldap_data
$ mkdir ldap_config
$ chown $USER:docker ldap_config
$ ls -la | grep ldap
drwxrwxr-x 2 $USER docker 4096 Oct  5 07:31 ldap_config
drwxrwxr-x 2 $USER docker 4096 Oct  5 07:31 ldap_data
$ cp ldap_admin.env.example ldap_admin.env # 環境変数は適宜変更
$ cp ldap.env.example ldap.env # 環境変数は適宜変更
```

## Quik Start

```sh
$ docker compose up
```

- ldap://localhostでldapにアクセスできる
- https://localhost:6443でPHP LDAP Adminにアクセスできる
  - `http`ではないことに注意
  - ログイン情報
    - DN: cn=admin,dc=example,dc=org # ドメインに合わせて適宜変更
    - Password: AhFKQeynF6Hr # 当然本番運用では変更すること

## Check Connection

**以下の3つのコマンドで想定される出力は全て同じ(実行される場所のみ異なる)**

- Docker Container内に入って動作確認

```sh
$ docker exec ldap     ldapsearch -x -H ldap://localhost     -b dc=inu,dc=tail869d5,dc=ts,dc=net     -D "cn=admin,dc=inu,dc=tail869d5,dc=ts,dc=net"     -w WcEshXgcdpUY
```

- localhostから疎通確認

```sh
$ ldapsearch -x -H ldap://localhost \
    -b dc=example,dc=org \
    -D "cn=admin,dc=example,dc=org" \
    -w AhFKQeynF6Hr
```

- Remote Serverから疎通確認

```sh
$ ldapsearch -x -H ldap://example.org \
    -b dc=example,dc=org \
    -D "cn=admin,dc=example,dc=org" \
    -w AhFKQeynF6Hr
```

### 想定される出力

```sh
# extended LDIF
#
# LDAPv3
# base <dc=example,dc=org> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

[...]

# numResponses: 3
# numEntries: 2
```
