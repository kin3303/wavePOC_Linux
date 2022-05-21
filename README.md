# wavePOC_Linux


### Step 1 > Admin Server 에서 BationHost 에 접근 후 신규 유저 생성

```console
$ chmod +x ./addUserToRemoteServer.sh
$ export VAULT_ADDR="http://172.31.37.26:8200"
$ vault login
  Token (will be hidden): hvs.zpu3IwU6OyNBg7iDN8DbWb3K

$ SERVER_IP="172.31.43.91"
$  vault write mysql/roles/linux-acc \
    db_name=mysql-database \
    creation_statements="CREATE USER '{{name}}'@'%' IDENTIFIED BY '{{password}}';GRANT SELECT ON *.* TO '{{name}}'@'%';" \
    default_ttl="5m" \
    max_ttl="5m"
$ NEW_USER=$(vault read mysql/creds/linux-acc -format=json | jq .data.username |  tr -d '"')
$ vault write ssh/roles/otp_temp_user_role \
     key_type=otp \
     default_user=$NEW_USER \
     allowed_user=$NEW_USER \
     key_bits=2048 \
     cidr_list=0.0.0.0/0

# 서버에 신규 사용자 추가
$ vault write ssh/roles/otp_add_user_role \
     key_type=otp \
     default_user=ubuntu \
     allowed_user=ubuntu \
     key_bits=2048 \
     cidr_list=0.0.0.0/0 
$ SSH_PASS=$(vault write ssh/creds/otp_add_user_role ip=$SERVER_IP -format=json | jq .data.key |  tr -d '"') 
$ sshpass -p $SSH_PASS ssh ubuntu@$SERVER_IP "bash -s" -- < ./addUserToRemoteServer.sh -n $NEW_USER

# SSH 접속
$ SSH_TEMP_USER_PASS=$(vault write ssh/creds/otp_temp_user_role ip=$SERVER_IP -format=json | jq .data.key |  tr -d '"') 
$ sshpass -p $SSH_TEMP_USER_PASS ssh $NEW_USER@$SERVER_IP

# SSH 접속 5분후 안되는것 
$ SSH_TEMP_USER_PASS=$(vault write ssh/creds/otp_temp_user_role ip=$SERVER_IP -format=json | jq .data.key |  tr -d '"') 
$ sshpass -p $SSH_TEMP_USER_PASS ssh $NEW_USER@$SERVER_IP
```
 
