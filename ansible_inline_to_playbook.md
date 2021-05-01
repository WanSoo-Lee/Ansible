# shell scripts add users
ansible all -m shell -a "echo 'jojoldu  ALL=(ALL)   NOPASSWD:ALL' > /etc/sudoers.d/jojoldu"

# ansible module add users
ansible all -m copy -a "content='jojoldu ALL=(ALL) NOPASSWD:ALL' dest=/etc/sudoers.d/jojoldu mode=0644 validate='/usr/sbin/sudo -c -f  \'%s\''"


# ansible playbook add users
---
- name: 사용자 추가
  hosts: all
  become: true
  tasks:
    - name: 사용자 이름 생성
      user:
        name: "{{ USER_NAME }}"
    - name: 패스워드 변경
      user:
        name: "{{ USER_NAME }}"
        password: "{{ PASSWORD | password_hash('sha512') }}"
    - name: sudoers.d 추가
      copy:
        content: |
          %{{USER_NAME}} ALL=(ALL) NOPASSWD:ALL
        dest: "/etc/sudoers.d/{{USER_NAME}}"
        owner: root
        group: root
        mode: 0440
        validate: "/usr/sbin/visudo -c -f '%s'"

# playbook 실행 명령
ansible-playbook site.yml --extra-vars "USER_NAME=사용자명 PASSWORD=패스워드" -u ubuntu
