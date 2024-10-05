# CLIからLDAP Userを作成する

## 事前準備

```sh
$ sudo apt update && sudo apt install -y ldap-utils
```

以下のコマンドを使用する

- `ldapadd`: LDAPにエントリを追加する
- `ldapsearch`: LDAPからエントリを検索する(確認用)

## LDAP OUを作成する

存在しないOUを指定して、Userを作成するとエラーになるので、OUを作成する

- LDAPサーバー上で作業する場合、`-H ldap://localhost`を指定する。

```sh
$ ldapadd -x -H ldap://localhost \ 
    -D "cn=admin,dc=example,dc=org" \
    -f add_ou.ldif \
    -w AhFKQeynF6Hr
```

- 作成したOUを確認する

```sh
$ ldapsearch -x -H ldap://localhost -b "dc=example,dc=org" "(ou=users)"
# extended LDIF
#
# LDAPv3
# base <dc=example,dc=org> with scope subtree
# filter: (ou=users)
# requesting: ALL
#

# search result
search: 2
result: 32 No such object
```

# LDAP Userを作成する

- LDAPサーバー上で作業する場合、`-H ldap://localhost`を指定する。

```sh
$ ldapadd -x -H ldap://localhost \
    -D "cn=admin,dc=example,dc=org" \
    -f add_user.ldif \
    -w AhFKQeynF6Hr
```

- 作成したUserを確認する

```sh
$ ldapsearch -x -H ldap://localhost -b "dc=example,dc=org" "(uid=john.doe)"
# extended LDIF
#
# LDAPv3
# base <dc=inu,dc=tail869d5,dc=ts,dc=net> with scope subtree
# filter: (uid=john.doe)
# requesting: ALL
#

# search result
search: 2
result: 32 No such object
```
