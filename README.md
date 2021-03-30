# mvp sample login
인증서비스입니다.   

아래와 같이 cluster에 배포하십시오.   

## local에 fetch   
k8s cluster에 연결된 PC나 VM에 접근하십시오. 
작업디렉토리를 만들고 git clone합니다.   
$ mkdir -p ~/work   
$ cd ~/work   
$ git clone https://github.com/happykube/mvp-sample-login.git
$ cd mvp-sample-login

## namespace 생성 & 현재 namespace 변경      
$ kubectl create namespace mvp-sample   
$ kubectl config set-context $(kubectl config current-context) --namespace mvp-sample

### mongo db POD 배포   
- deploy/db-mongodb/config.yaml수정 
  - storageClassName지정: kubectl get sc로 StorageClass를 찾아 적절한 SC를 지정
  - NFS dynamic provisioning사용 여부 지정: 사용 시 volume.dynamic에 true로 지정
  - dyamic provisioning 미사용시 NFS server, path 지정: volume.nfs.server, volume.nfs.path지정   
  - dyamic provisioning 미사용시 volume directory 작성   
  ```
  NFS서버로 접속하여, 아래 예제처럼 위에서 지정한 path를 생성   
  $ mkdir -p /data/nfs/mongodb   
  $ chmod 777 /data/nfs/mongodb  

  ```
- helm으로 mongodb 배포
```
$ cd ~/work/mvp-sample-login/deploy/db-mongodb   
$ helm install mongodb-login . -n mvp-sample   
$ kubectl get pod   
```
 
* 만약 다시 설치하려면 아래와 같이 지우고 재시도   
```
$ kubectl delete job mongodb-login-create-user   
$ helm delete mongodb-login   
```

### login microservice 배포
$ cd ~/work/mvp-sample-login/deploy   
$ vi ingress.yaml   
- host정보를 수정합니다.     
ICP에서는 kubectl get nodes -o wide로 proxy node의 ip를 확인한후, 그 ip로 지정 
- apiVersion을 수정: ICP는 extensions/v1beta1, vanilla k8s는 networking.k8s.io/v1beta1   

$ kubectl apply -f . 







