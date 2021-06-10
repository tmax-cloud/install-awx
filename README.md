# Ansible AWX 설치 가이드

## 구성 요소 및 버전
awx-operator ([quay.io/awx-operator:0.8.0]())

awx ([quay.io/ansible/awx:18.0.0]())

awx-ee ([quay.io/ansible/awx-ee:0.1.1]())

redis ([redis:latest](https://hub.docker.com/layers/redis/library/redis/latest/images/sha256-b4b16c2978639e1423f3618732a75bb53967c6e3bf3722a3f8c31f9691743eea?context=explore))

postgres ([postgres:12](https://hub.docker.com/layers/postgres/library/postgres/12/images/sha256-328b452c593fa415c523ac54ec06a1170c4fccf170f41aa967ab159765c44f89?context=explore))


## 설치 가이드
### 1. awx operator deploy
[awx-operator.yaml](yamls/awx-operator.yaml)을 적용
```bash
kubectl apply -f awx-operator.yaml
```


### 2. awx deploy
[awx.yaml](yamls/awx.yaml)을 적용
```bash
kubectl apply -f awx.yaml
```
`tower_hostname`: 

`tower_image`: 

`tower_ee_images`:

### 3. login
id: admin
password: 아래 명령어를 쳐서 나오는 값을 패스워드로 사용
```
kubectl get secret awx-admin-password -o jsonpath='{.data.password}' | base64 --decode
```


#### Keycloak 연동
[keycloak 연동 가이드](keycloak.md) 참고