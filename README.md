# wavePOC_Linux


### Step 1 > Admin Server 에서 BationHost 에 접근 후 신규 유저 생성 및 SSH 설정

```console
$ git clone https://github.com/kin3303/wavePOC_Linux.git
$ cd wavePOC_Linux
$ chmod +x ./addUserToRemoteServer.sh
$ chmod +x ./userManagementToServer.sh
$ export VAULT_ADDR="http://172.31.37.26:8200"
$ export VAULT_TOKEN="hvs.zpu3IwU6OyNBg7iDN8DbWb3K"
$ ./userManagementToServer.sh -s 172.31.43.91 -n <USER_NAME>
```
