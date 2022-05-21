# wavePOC_Linux

### Target Server 준비사항

jq 를 미리 설치해 놓아야 한다.

```console
$ sudo apt install jq -y
```

### Admin Server

```console
$ chmod +x ./addUserToRemoteServer.sh
$ export VAULT_ADDR="http://172.31.37.26:8200"
$ vault login
  Token (will be hidden): hvs.zpu3IwU6OyNBg7iDN8DbWb3K
$ SSH_PASS=$(vault write ssh/creds/otp_key_role ip=172.31.46.39 -format=json | jq .data.key |  tr -d '"') 
$ sshpass -p $SSH_PASS ssh ubuntu@172.31.46.39 "bash -s" -- < ./addUserToRemoteServer.sh -n "daeung" -d "/home/daeung" -s "/bin/bash" -g "daeung"
```
 
