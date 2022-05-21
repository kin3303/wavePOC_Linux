# wavePOC_Linux


### Step 1 > Admin Server 에서 SSH Onetime pass 를 이용해서 BationHost 에 접근 후 신규 유저 생성

```console
$ chmod +x ./addUserToRemoteServer.sh
$ export VAULT_ADDR="http://172.31.37.26:8200"
$ vault login
  Token (will be hidden): hvs.zpu3IwU6OyNBg7iDN8DbWb3K
$ SSH_PASS=$(vault write ssh/creds/otp_add_user_role ip=172.31.46.39 -format=json | jq .data.key |  tr -d '"') 
$ sshpass -p $SSH_PASS ssh ubuntu@172.31.46.39 "bash -s" -- < ./addUserToRemoteServer.sh -n daeung
```
 
