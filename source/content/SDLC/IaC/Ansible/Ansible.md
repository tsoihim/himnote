## Ansible이란
Ansible은 원격 시스템들이 사용자가 원하는 상태에 있을 수 있도록 관리를 자동화, 아래와 같은 일 수행 가능
- Workflow를 간단화하고 중복되는 일 제거
- 시스템 설정 등을 관리하고 유지
- 복잡한 소프트웨어에 대한 Continuous Deployment (CD)
- 제로 다운타임 롤링 업데이트
![[Pasted image 20250517200646.png|350]]

위 그림과 같이 대부분의 Ansible 환경은 세 개의 구성요소를 가짐:
**Control node**
Ansible이 설치된 시스템. 컨트롤 노드에서 `ansible` 혹은 `ansible-inventory` 등의 명령을 내릴 수 있음
- 컨테이너 이미지로 패키징된 Ansible 컨트롤 노드인 Execution Environments(EEs)도 존재
**Managed node**
Ansible이 관리하는 원격시스템 혹은 호스트
**Inventory**
논리적으로 조직된 Managed node의 목록. Ansible에 deployment 정보를 전달하기 위해 컨트롤 노드에 인벤토리를 생성할 수 있음

**Playbook**
Ansible은 playbook이라고 불리는 스크립트를 이용하여 태스크를 자동화함. 사용자는 로컬 혹은 원격 시스템이 가져야할 상태를 해당 스크립트에 기술. Ansible은 해당 시스템들이 그 상태에 있을 수 있도록 구현됨
- **Playbook**
    - 전체 목표를 수행하기 위한 play의 ordered list
- **Play**
    - 인벤토리의 관리 노드에 매핑되는 task의 ordered list
    - **Role**
        - Play 내부에서 사용되는 재사용가능한 Ansible Content (Task, Variable, Plugin 등)
        - 사용을 위해서 Play에 import되어야함
- **Task**
    - Ansible이 수행할 동작들을 정의한 module의 list
    - **Handler**는 특수한 형태의 task로, 이전 task에 의해 status가 바뀔 때만 실행됨
- **Module**
    - 관리 노드에서 Ansible이 실행할 코드 유닛
    - Ansible 모듈들은 각 모듈이 가지는 FQCN 이용하여 그룹화됨

아래는 Ansible의 구현 철학:
**Agent-less architecture, Simplicity**
Ansible 환경에서 agent 등의 추가적인 소프트웨어 설치를 하지 않아 간단. SSH를 이용해 원격 시스템들에 접속
또한, playbook은 YAML syntax를 사용, 문서와 같이 human-readable함
**Idempotence and predictability**
멱등성을 지원하여 여러 번 태스크를 수행해도 그 결과인 시스템의 상태는 달라지지 않음


**References**
- https://docs.ansible.com/ansible/devel/getting_started/index.html