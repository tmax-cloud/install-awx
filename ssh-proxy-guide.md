특정 host(jump host)를 통해서만 target host에 접근할 수 있는 상황 (jump host를 ssh-proxy로 사용하는 상황)
ansible 설치 노드, jump host, target host 총 3대가 있다고 가정

### 1. ansible 설치 노드에서 아래와 같이 ssh config 파일 수정

```
vim ~/.ssh/config
------------------------------------------------------
HOST jump
	User ec2-user
	Hostname 11.11.11.11  
	IdentityFile {pem-file-path}
  
  

HOST target
  User ec2-user
	Hostname 22.22.22.22    
	IdentityFile {pem-file-path}
  ProxyCommand ssh bastion -W %h:%p
```

### 2. ansible hosts 추가
```
vim /etc/ansible/hosts
------------------------------------------------------
~~~
~~~
jump
target

```

### 3. 테스트
```
ansible all -m ping
-------------------------------------
jump | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "ping": "pong"
}
target | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "ping": "pong"
}
```
