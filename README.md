# wavePOC_Linux


### Step 1 > Admin Server 에서 BationHost 에 접근 후 신규 유저 생성 및 SSH 설정

```console
$ chmod +x ./addUserToRemoteServer.sh
$ chmod +x ./userManagementToServer.sh
$ ./userManagementToServer.sh -s 172.31.43.91 -n <USER_NAME>
```
