# mvp sample login
인증서비스입니다.   

아래와 같이 cluster에 배포하십시오.   

### local에 fetch   
kubectl명령으로 배포할 수 있는 terminal에서 git clone합니다.   
$ mkdir ~/work   
$ cd ~/work   
$ git clone https://gitlab.com/jenkins28/mvp-sample-login.git   
$ cd mvp-sample-login

### namespace 생성 & 현재 namespace 변경      

$ kubectl create namespace mvp-sample   
$ kubectl config set-context $(kubectl config current-context) --namespace mvp-sample

### mongo db POD 배포   
- volume server, path 지정   
$ cd deploy/db-mongodb      
$ vi values.yaml   
267라인쯤에 있는 아래 nfs volume정보를 수정   
  nfs:   
    server: 169.56.88.117   
    path: /data/nfs/mongodb   

- volume directory 작성   
NFS서버로 접속하여, 위에서 지정한 path를 생성   
예)
$ mkdir -p /data/nfs/mongodb   
$ chmod 777 /data/nfs/mongodb  

- helm으로 mongodb 배포
$ cd ~/work/mvp-sample-login/deploy/db-mongodb   
$ helm install mongodb-login . -n mvp-sample   
ICP에서는 helm install --name mongodb-login . -n mvp-sample --tls   
$ kubectl get pod   

* 만약 다시 설치하려면 아래와 같이 지우고 재시도   
$ kubectl delete job mongodb-login-create-user   
$ helm delete mongodb-login   
  ICP는 helm delete mongodb-login --purge --tls   

### login microservice 배포
$ cd ~/work/mvp-sample-login/deploy   
$ vi ingress.yaml   
- host정보를 수정합니다.     
ICP에서는 kubectl get nodes -o wide로 proxy node의 ip를 확인한후, 그 ip로 지정 
- apiVersion을 수정: ICP는 extensions/v1beta1, vanilla k8s는 networking.k8s.io/v1beta1   

$ kubectl apply -f . 







