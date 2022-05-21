# wavePOC_Linux

### Admin Server 준비사항

먼저 Vault 에 Login 한다.

```console
$ export VAULT_ADDR="http://172.31.37.26:8200"
$ vault login
  Token (will be hidden): hvs.zpu3IwU6OyNBg7iDN8DbWb3K
```

ssh secret engine 을 활성화 하고 otp 용 role 을 하나 발급받는다.

```console
$ vault secrets enable ssh
Success! Enabled the ssh secrets engine at: ssh/

$ vault write ssh/roles/otp_key_role \
     key_type=otp \
     default_user=ubuntu \
     allowed_user=ubuntu \
     key_bits=2048 \
     cidr_list=0.0.0.0/0
Success! Data written to: ssh/roles/otp_key_role
```




### Bastion Server 준비사항

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
$ sshpass -p $SSH_PASS ssh ubuntu@172.31.46.39 "bash -s" -- < ./addUserToRemoteServer.sh -n daeung
```
 
