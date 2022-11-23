# Ansible AWX 설치 가이드

## 구성 요소 및 버전
awx-operator ([quay.io/awx-operator:0.13.0]())

awx ([quay.io/ansible/awx:19.3.0]())

awx-ee ([quay.io/ansible/awx-ee:0.5.0]())

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

```
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx
spec:
  ingress_type: Ingress
  ingress_annotations: |
     kubernetes.io/ingress.class: "<ingress_class_name>"
     traefik.ingress.kubernetes.io/router.entrypoints: "websecure"
  hostname: awx.example.com
  image: quay.io/ansible/awx
  image_version: 19.3.0
  redis_image: docker.io/redis
  redis_image_version: latest
  postgres_image: postgres
  postgres_image_version: "12"
  init_container_image: quay.io/centos/centos
  init_container_image_version: "8"
  ee_images:
    - name: awx-ee
      image: quay.io/ansible/awx-ee:0.5.0
  control_plane_ee_image: quay.io/ansible/awx-ee:0.5.0
```
`metadata.name` :awx deployment에 사용할 이름

`spec.ingress_annotations.kubernetes.io/ingress.class` : annotation으로 ingress class 지정 또는 아래 2-1과 같이 ingress 수정하여 지정

`spec.hostname`: awx 주소로 사용할 값

`spec.image`: awx 도커 이미지 (default=19.3.0)

`spec.ee_images`: awx_ee(execution environment) 도커 이미지 (default=latest)

`spec.control_plane_ee_image`: control plane으로 사용할 awx-ee 이미지 (default=latest)

이미지의 버전이나 태그를 지정하지 않을 시에는 default로 지정된 이미지가 사용되며 default 값은 awx-operator/roles/installer/defaults/main.yaml에서 확인 가능 (ex. [https://github.com/ansible/awx-operator/blob/0.13.0/roles/installer/defaults/main.yml](https://github.com/ansible/awx-operator/blob/0.13.0/roles/installer/defaults/main.yml))

테스트 환경에서는 ingress_annotations 옵션 주석 처리 및 필요에따라 활성화

### 2-1. Using https with ingress
위와 같이 ingress를 사용하여 deploy 시, 기본적으로 http 사용하며 
https 지원을 위해서는 deploy 이후에 아래와 같이 ingress 수정 필요

```bash
$ kubectl edit ingress awx-ingress
```
```
spec:
  ingressClassName: <ingress_class_name>
  .
  .
  .
  tls:
  - hosts:
    - awx.example.com
```

### 3. login
위에서 사용한 hostname으로 접속     
![awx.yaml](img/login_1.PNG)   
id: admin   
password: 아래 명령어를 쳐서 나오는 값을 패스워드로 사용   
```
kubectl get secret awx-admin-password -o jsonpath='{.data.password}' | base64 --decode
```


#### 기본 사용법 가이드
[AWX 기본 사용법 가이드](tutorial.md) 참고


#### Keycloak 연동
[keycloak 연동 가이드](keycloak.md) 참고


#### ssh-proxy 방법
[ssh-proxy 사용 가이드](ssh-proxy-guide.md) 참고


## Documents
[awx-operator Readme](https://github.com/ansible/awx-operator/tree/0.13.0)
