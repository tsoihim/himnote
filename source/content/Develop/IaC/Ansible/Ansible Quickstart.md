## Ansible 설치
```
dev@nara-MacBook:~ pip3 install ansible
...
dev@nara-MacBook:~ ansible --version
ansible [core 2.16.2]
```

## Inventory 생성
### 디렉토리 생성
```
# Inventory 디렉토리 생성
dev@nara-MacBook:~ mkdir ansible_quickstart; cd ansible_quickstart
dev@nara-MacBook:ansible_quickstart
```
### Inventory 파일 생성
- Inventory 디렉토리에 ini 혹은 yaml 파일로 생성
- host의 IP 혹은 FQDN을 기재
    - SSH 연결을 위해 아래와 같이 미리 ssh-copy-id를 수행
    - `ssh-copy-id -i ~/.ssh/id_rsa.pub [USERNAME][@[HOST]](<mailto:root@10.0.113.101>)`
    - 직접 pass를 전달하려면 sshpass가 설치되어있어야함

**inventory.ini**
```
[myhosts]
10.0.113.101
10.0.113.102
```

**inventory.yaml**
```yaml
myhosts:
  hosts:
    my_host_01:
      ansible_host: 10.0.113.101
    my_host_02:
      ansible_host: 10.0.113.102
```
### Inventory Actions
```
# Verify
dev@nara-MacBook:ansible_quickstart ansible-inventory -i inventory.yaml --list
{
    "_meta": {
        "hostvars": {
            "my_host_01": {
                "ansible_host": "10.0.113.101"
            },
            "my_host_02": {
                "ansible_host": "10.0.113.102"
            }
        }
    },
    "all": {
        "children": [
            "ungrouped",
            "myhosts"
        ]
    },
    "myhosts": {
        "hosts": [
            "my_host_01",
            "my_host_02"
        ]
    }
}

# Ping Check (컨트롤 노드와 user가 달라 u option 사용)
atto@nara-MacBook:ansible_quickstart ansible myhosts -m ping -i inventory.yaml -u root
my_host_02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
my_host_01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
### Metagroup 설정
아래와 같이 여러 그룹을 묶어주는 metagroup을 사용하여 그룹 분류가 가능
```yaml
group1:
  hosts:
    my_host_01:
      ansible_host: 10.0.113.101

group2:
  hosts:
    my_host_02:
      ansible_host: 10.0.113.102

test:
   children:
      group1:
      group2:

```
### Variable 설정
아래와 같이 관리노드를 위한 IP, FQDN, OS, SSH 유저 등의 값을 설정값으로 지정하여 사용 가능
- host별, 그룹별 variable 지정이 가능
```yaml
# host별 설정
myhosts:
  hosts:
    my_host_01:
      ansible_host: 10.0.113.101
      ansible_user: root
    my_host_02:
      ansible_host: 10.0.113.102
      ansible_user: root

# 그룹별 설정
myhosts:
  hosts:
    my_host_01:
      ansible_host: 10.0.113.101
    my_host_02:
      ansible_host: 10.0.113.102
  vars:
    ansible_user: root
```
## Playbook 생성
Inventory 디렉토리 내에 아래와 같은 형태로 playbook.yaml을 생성
- 두 개의 play를 정의
    - 각 play에 task를 하나씩 정의
```yaml
- name: Ping Play
  hosts: myhosts
  tasks:
   - name: Ping my hosts
     ansible.builtin.ping:

- name: Echo Play
  hosts: myhosts
  tasks:
   - name: Print message
     ansible.builtin.debug:
        msg: Hello world
```

아래와 같이 ansible-playbook 실행
```
dev@nara-MacBook:ansible_quickstart ansible-playbook -i inventory-vars.yaml playbook.yaml

PLAY [Ping Play] ***************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [my_host_01]
ok: [my_host_02]

TASK [Ping my hosts] ***********************************************************************************************************************************
ok: [my_host_01]
ok: [my_host_02]

PLAY [Echo Play] ***************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [my_host_01]
ok: [my_host_02]

TASK [Print message] ***********************************************************************************************************************************
ok: [my_host_01] => {
    "msg": "Hello world"
}
ok: [my_host_02] => {
    "msg": "Hello world"
}

PLAY RECAP *********************************************************************************************************************************************
my_host_01                 : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
my_host_02                 : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
 ```


**References**
- https://docs.ansible.com/ansible/devel/getting_started/index.html