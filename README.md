<div align="center"><h1>Actions Connect Open VPN</h1></div>

This action is a connect ovpn script

## Example file `.ovpn` to connect vpn

[Test.ovpn](./test.ovpn) and [setup file](#Example-prepare-file-.ovpn)

## Configuration with With

The following settings must be passed as environment variables as shown in the
example.

| Key         | Value                                                                                                                           | Suggested Type | Required | Default         |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------- | -------------- | -------- | --------------- |
| `FILE_OVPN` | Location file open vpn and .                                                                                                    | `env`          | **Yes**  | `./config.ovpn` |
| `PING_URL`  | URL for check status vpn connect pass or fail                                                                                   | `env`          | **Yes**  | `127.0.0.1`     |
| `SECRET`    | Username password for access vpn`(Encode base 64 before set secret.)`[How to encode base 64 ?](https://www.base64encode.org/).  | `secret env`   | No       | `''`            |
| `TLS_KEY`   | Tls-crypt for access vpn `(Encode base 64 before set secret.)`[How to encode base 64 ?](https://www.base64encode.org/).         | `secret env`   | No       | `''`            |

## Configuration with Env

The following settings must be passed as environment variables as shown in the
example.

| Key         | Value                                                                                                                           | Suggested Type | Required | Default |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------- | -------------- | -------- | ------- |
| `CA_CRT`    | Certificate for access vpn `(Encode base 64 before set secret.)`[How to encode base 64 ?](https://www.base64encode.org/).       | `secret env`   | **Yes**  | N/A     |
| `USER_CRT`  | User certificate for access vpn. `(Encode base 64 before set secret.)`[How to encode base 64 ?](https://www.base64encode.org/). | `secret env`   | **Yes**  | N/A     |
| `USER_KEY`  | User key for access vpn. `(Encode base 64 before set secret.)`[How to encode base 64 ?](https://www.base64encode.org/).         | `secret env`   | **Yes**  | N/A     |

## Outputs

### `STATUS`

**Boolean** Can get status after connect `true` or `false`.

## Example usage

```yml
  connect-open-vpn:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - name: Install Open VPN
        run: sudo apt-get install openvpn
      - name: Connect VPN
        uses: golfzaptw/action-connect-ovpn@master
        id: connect_vpn
        with:
          PING_URL: '127.0.0.1'
          FILE_OVPN: '.github/vpn/config.ovpn'
          SECRET: ${{ secrets.SECRET_USERNAME_PASSWORD }}
          TLS_KEY: ${{ secrets.TLS_KEY }}
        env:
          CA_CRT: ${{ secrets.CA_CRT}}
          USER_CRT: ${{ secrets.USER_CRT }}
          USER_KEY: ${{ secrets.USER_KEY }}
      - name: Check Connect VPN
        run: echo ${{ steps.connect_vpn.outputs.STATUS }}
      - name: kill vpn
        if: always()
        run: sudo killall openvpn
```

## How to prepare .ovpn file

### Steps

1. Copy data inside tags:
- `<ca></ca>`
- `<cert></cert>`
- `<key></key>`
- `<tls></tls>` (if applicable)
- `<tls-auth></tls-auth>` (if applicable)

and base64 encode them each individually, saving the values of each to github actions secrets.

2. Remove the (now empty) tags
- `<ca></ca>`
- `<cert></cert>`
- `<key></key>`
- `<tls></tls>` (if applicable)
- `<tls-auth></tls-auth>` (if applicable)

and replace them with
```
ca ca.crt
cert user.crt
key user.key
auth-user-pass secret.txt
tls-auth tls.key # IF APPLICABLE
askpass key.pass # IF APPLICABLE
```

3. If openvpn has username and password, please encode base64 and then save github actions secrets, the following value:
```
MyUsernameHere
MyPasswordHere
```
