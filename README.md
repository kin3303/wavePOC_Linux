# wavePOC_Linux

### DB 서버



### Step 1 > Admin Server 준비사항

DB 로 mysql 을 설치한다.

```console
$ sudo  apt-get update
$ sudo  apt-get install mysql-server﻿
$ sudo  ufw allow mysql
$ sudo  systemctl start mysql
$ sudo systemctl enable mysql
$ sudo /usr/bin/mysql -u root -p
비번 ubuntu
mysql>  show variables like "%version%";
mysql> CREATE DATABASE master;
mysql> SHOW DATABASES;
mysql> CREATE USER 'linux'@'%' IDENTIFIED BY 'PASSWORD';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'linux'@'%' WITH GRANT OPTION;
mysql> GRANT PROXY ON ''@'' TO 'linux'@'%' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;
mysql> SHOW GRANTS FOR 'linux'@'%';

// 외부 접속 허용
$ sudo  vi /etc/mysql/my.cnf
..
[mysqld]
bind-address            = 0.0.0.0
$ sudo systemctl restart mysql
$ netstat -ntlp | grep mysql
tcp    0    0 0.0.0.0:3306     0.0.0.0:*     LISTEN      7206/mysqld
```

Vault 에 Login 한다.

```console
$ export VAULT_ADDR="http://172.31.37.26:8200"
$ vault login
  Token (will be hidden): hvs.zpu3IwU6OyNBg7iDN8DbWb3K
```

DB 시크릿 엔진을 활성화하고 Role 을 발급받는다.

```console
// DB 시크릿 엔진 활성화
$  vault secrets enable -path mysql database

// DB 시크릿 엔진 설정
$ vault write mysql/config/mysql-database \
     plugin_name=mysql-database-plugin \
     connection_url="{{username}}:{{password}}@tcp(<DB_CLIENT_IP>:3306)/" \
     allowed_roles="*" \
     username="linux" \
     password="PASSWORD"

// Linux Account 생성용 Role 생성
$  vault write mysql/roles/linux-acc \
    db_name=mysql-database \
    creation_statements="CREATE USER '{{name}}'@'%' IDENTIFIED BY '{{password}}';GRANT SELECT ON *.* TO '{{name}}'@'%';" \
    default_ttl="30m" \
    max_ttl="30m"

// DB 계정 발급
$ vault read mysql/creds/linux-acc
Key                Value
---                -----
lease_id           mysql/creds/linux-acc/hMKOt6ZqdbFMcJ9DelJajLZ1
lease_duration     1m
lease_renewable    true
password           iWJm-DZ99ARtTLh9bKWc
username           v-root-linux-acc-lRTgtUHpY5uCMwb

// DB 계정 삭제 확인
$ sudo /usr/bin/mysql -u root -p
비번 ubuntu
mysql> Select user from mysql.user;  

```

SSH 시크릿 엔진을 활성화 하고 role 을  발급받는다.
(해당 role 은 bastion 호스트에 유저를 생성하기 위한 role 이다.)

```console
// SSH 시크릿 엔지 활성화
$ vault secrets enable ssh
Success! Enabled the ssh secrets engine at: ssh/

// 
$ vault write ssh/roles/otp_add_user_role \
     key_type=otp \
     default_user=ubuntu \
     allowed_user=ubuntu \
     key_bits=2048 \
     cidr_list=0.0.0.0/0
Success! Data written to: ssh/roles/otp_key_role
```


### Step 2 > Bastion Server 준비사항

vault-ssh-helper 설치 및 구성을 진행한다.

```console
$ sudo su

// vault-ssh-helper 다운로드 및 설치
$ wget https://releases.hashicorp.com/vault-ssh-helper/0.2.1/vault-ssh-helper_0.2.1_linux_amd64.zip
$ unzip vault-ssh-helper_0.2.1_linux_amd64.zip
$ mv vault-ssh-helper /usr/bin
$ chmod +x /usr/bin/vault-ssh-helper

// vault-ssh-helper 구성, tls 가 없으면 dev 로만 동작하
$ mkdir /root/vault
$ tee /root/vault/config.hcl <<EOF
vault_addr = "http://172.31.37.26:8200"
ssh_mount_point = "ssh" 
tls_skip_verify = true
allowed_cidr_list="0.0.0.0/0"
allowed_roles = "*"
EOF

// vault-ssh-helper 구성 테스트
$ vault-ssh-helper -verify-only -config=/root/vault/config.hcl -dev
2021/10/18 06:25:40 ==> WARNING: Dev mode is enabled!
2021/10/18 06:25:40 [INFO] using SSH mount point: ssh
2021/10/18 06:25:40 [INFO] using namespace:
2021/10/18 06:25:40 [INFO] vault-ssh-helper verification successful!

// 리눅스 표준 SSH 모듈인 common-auth 를 주석 처리
// 인증시 vault-ssh-helper 를 사용하도록 설정
$ vi /etc/pam.d/sshd  
# Standard Un*x authentication.
#@include common-auth
auth requisite pam_exec.so quiet expose_authtok log=/tmp/vaultssh.log /usr/bin/vault-ssh-helper -config=/root/vault/config.hcl -dev
auth optional pam_unix.so not_set_pass use_first_pass nodelay
...

$ vi /etc/ssh/sshd_config
ChallengeResponseAuthentication yes
UsePAM yes
PasswordAuthentication no

$ sudo systemctl restart sshd
```

jq 를 설치한다.

```console
$ sudo apt install jq -y
```

### Step 3 > Admin Server 에서 SSH Onetime pass 를 이용해서 BationHost 에 접근 후 신규 유저 생성

```console
$ chmod +x ./addUserToRemoteServer.sh
$ export VAULT_ADDR="http://172.31.37.26:8200"
$ vault login
  Token (will be hidden): hvs.zpu3IwU6OyNBg7iDN8DbWb3K
$ SSH_PASS=$(vault write ssh/creds/otp_add_user_role ip=172.31.46.39 -format=json | jq .data.key |  tr -d '"') 
$ sshpass -p $SSH_PASS ssh ubuntu@172.31.46.39 "bash -s" -- < ./addUserToRemoteServer.sh -n daeung
```
 
