# mvp sample login
인증서비스입니다.   


아래와 같이 cluster에 배포하십시오.   

# 사전준비
- k8s cluster에 연결된 PC나 VM에 접근하십시오. 
- NFS Dynamic provisiong을 사용하려면, [NFS설치와 Dynamic provisiong설정](https://happycloud-lee.tistory.com/178?category=832243)을 참조하십시오. 
- [run-cicd 파이프라인](https://happycloud-lee.tistory.com/195?category=832250) 다운로드 
```
$ cd ~
$ git clone https://github.com/happyspringcloud/run-cicd.git
```
- namespace변수를 만듭니다. 아래 예 참조하여 적절히 변경하세요. 
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
kubens가 설치되어 있으면 ..
$ kubens ${NS}
```

# mongo db POD 배포   
- deploy/db-mongodb/config.yaml수정 
  - storageClassName지정: kubectl get sc로 StorageClass를 찾아 적절한 SC를 지정
  - NFS dynamic provisioning사용 여부 지정: 사용 시 volume.dynamic에 true로 지정
  - dyamic provisioning 미사용시 NFS server, path 지정: volume.nfs.server, volume.nfs.path지정   
  - [예제]
  <img src="./img/2021-03-30-12-25-33.png" width=60% height=60%/>

- dyamic provisioning 미사용시 volume directory 작성   
  ```
  NFS서버로 접속하여, 아래 예제처럼 위에서 지정한 path를 생성   
  $ mkdir -p /data/nfs/mongodb   
  $ chmod 777 /data/nfs/mongodb  

  ```

- helm으로 mongodb 배포
```
$ cd ~/work/mvp-sample-login/deploy/db-mongodb   
$ helm install login -f config.yaml .   
$ kubectl get pod   
```

* 만약 다시 설치하려면 아래와 같이 지우고 재시도   
```
$ kubectl delete job mongodb-login-create-user   
$ helm delete mongodb-login   
```

# mvp-sample-login microservice 배포

- cicd디렉토리 하위의 cicd-common.properties 파일 수정: image_project, image_repository, image_tag, namespace, service_host의 IP   

- run-cicd 실행하고, 값을 적절히 입력 
```
$ cd ~/work/mvp-sample-login
$ run-cicd {image registry login id} {image registry passw0rd} . dev . nodejs
ex) run-cicd happycloudpak passw0rd . dev . nodejs 
```

- PVC바인딩이 되어 있는지 확인
```
$ kubectl get pvc
```

- Pod실행여부 확인
```
$ kubectl get po
```

# Next Action
- [mvp-sample-front](https://github.com/happykube/mvp-sample-front.git)를 배포하십시오. 
- [mvp-sample-bizlogic](https://github.com/happykube/mvp-sample-bizlogic.git)을 배포하십시오.



