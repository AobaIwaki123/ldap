# LDAPサーバー

## Quik Start

```sh
$ docker compose up
```

## Check Connection

- Docker Container内に入って動作確認

```sh
$ docker exec my-openldap-container \
    ldapsearch -x -H ldap://localhost \
    -b dc=example,dc=org \
    -D "cn=admin,dc=example,dc=org" \
    -w AhFKQeynF6Hr
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
