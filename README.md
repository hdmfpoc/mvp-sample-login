# mvp sample login
인증서비스입니다.   

아래와 같이 cluster에 배포하십시오.   

# 사전준비
- k8s cluster에 연결된 PC나 VM에 접근하십시오. 
- namespace변수를 만듭니다. 
```
$ export NS=mvp-sample
```

# git clone   
작업디렉토리를 만들고 git clone합니다.  
```
$ mkdir -p ~/work   
$ cd ~/work   
$ git clone https://github.com/happykube/mvp-sample-login.git
$ cd mvp-sample-login
```

# namespace 생성 & 현재 namespace 변경      
```
$ kubectl create ns ${NS}   
$ kubectl config set-context $(kubectl config current-context) --namespace ${NS}
```

# mongo db POD 배포   
- deploy/db-mongodb/config.yaml수정 
  - storageClassName지정: kubectl get sc로 StorageClass를 찾아 적절한 SC를 지정
  - NFS dynamic provisioning사용 여부 지정: 사용 시 volume.dynamic에 true로 지정
  - dyamic provisioning 미사용시 NFS server, path 지정: volume.nfs.server, volume.nfs.path지정   
[예제]
![](./img/2021-03-30-12-25-33.png =60%*60%)

- dyamic provisioning 미사용시 volume directory 작성   
  ```
  NFS서버로 접속하여, 아래 예제처럼 위에서 지정한 path를 생성   
  $ mkdir -p /data/nfs/mongodb   
  $ chmod 777 /data/nfs/mongodb  

  ```

- helm으로 mongodb 배포
```
$ cd ~/work/mvp-sample-login/deploy/db-mongodb   
$ helm install mongodb-login .   
$ kubectl get pod   
```

* 만약 다시 설치하려면 아래와 같이 지우고 재시도   
```
$ kubectl delete job mongodb-login-create-user   
$ helm delete mongodb-login   
```

# mvp-sample-login microservice 배포
- deploy/ingress.yaml의 spec.rules.host수정
```
$ cd ~/work/mvp-sample-login/deploy   
$ vi ingress.yaml  

$ kubectl apply -f . 
```






