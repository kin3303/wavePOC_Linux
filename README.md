# wavePOC_Linux


### Admin Server

```console
$ export VAULT_ADDR="http://172.31.37.26:8200"
$ vault login
Token (will be hidden):
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                hvs.zpu3IwU6OyNBg7iDN8DbWb3K
token_accessor       b5I5KQSYJwleaWdELskDitPq
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]

//IP 로 Client IP 를 넣어 OTP 발급 요청하면 SSH_KEY 를 발급 받음
$ SSH_PASS=$(vault write ssh/creds/otp_key_role ip=172.31.46.39 -format=json | jq .data.key |  tr -d '"') 
$ sshpass -p $SSH_PASS ssh ubuntu@172.31.46.39 "bash -s" -- < ./addUserToRemoteServer.sh -n "daeung" -d "/home/daeung" -s "/bin/bash" -g "daeung"

 
