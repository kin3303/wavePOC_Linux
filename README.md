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

//IP 로 Client IP 를 넣어 OTP 발급 요청하면 Key 를 발급 받음
$ vault write ssh/creds/otp_key_role ip=172.31.46.39
Key                Value
---                -----
lease_id           ssh/creds/otp_key_role/RsJfwxCwMLwwKNxdOcgaQFvU
lease_duration     768h
lease_renewable    false
ip                 172.31.46.39
key                e5821cd0-43df-1be6-948c-2ab39d312f35
key_type           otp
port               22
username           ubuntu
 
$ ssh ubuntu@172.31.46.39
The authenticity of host '172.31.46.39 (172.31.46.39)' can't be established.
ECDSA key fingerprint is SHA256:F+qWH8F0lHeQXf4ReRQDSgreUHr4fq404bmWfVr+jFg.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.31.46.39' (ECDSA) to the list of known hosts.
Password: e5821cd0-43df-1be6-948c-2ab39d312f35
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.13.0-1022-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon May 16 09:23:54 UTC 2022

  System load:  0.0               Processes:             113
  Usage of /:   19.0% of 7.69GB   Users logged in:       1
  Memory usage: 22%               IPv4 address for eth0: 172.31.46.39
  Swap usage:   0%

 * Ubuntu Pro delivers the most comprehensive open source security and
   compliance features.

   https://ubuntu.com/aws/pro

0 updates can be applied immediately.


Last login: Mon May 16 09:00:20 2022 from 219.240.45.245

// SSH 접속 확인 (2차) - 접속 안됨 확인
$ ssh ubuntu@172.31.46.39
ubuntu@13.125.74.127: Permission denied (publickey,keyboard-interactive).
```
