# wavePOC_Linux


### Step 1 > Admin Server 에서 BationHost 에 접근 후 신규 유저 생성 및 SSH 설정

```console
$ git clone https://github.com/kin3303/wavePOC_Linux.git
$ cd wavePOC_Linux
$ chmod +x ./addUserToRemoteServer.sh
$ chmod +x ./userManagementToServer.sh
$ ./userManagementToServer.sh -s 172.31.43.91 -n <USER_NAME>
```

설치 부분 자동화..

- https://www.hashicorp.com/blog/codifying-vault-policies-and-configuration
- https://code.stanford.edu/et-public/cloud-scripts/-/tree/master/
