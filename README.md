# Описание

Стенд для практики к уроку «Автоматизация администрирования. Ansible.»  

Разворачивается два сервера: `host1` и `host2`

# Инструкция по применению
## Перед запуском

На хосте необходимо установить Ansible. Совсем достаточно: `yum install ansible`

Проверим что Ansible установлен: `ansible --version`
```
# ansible --version
ansible 2.9.10
  config file = /home/paul/stands-ansible/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /bin/ansible
  python version = 2.7.5 (default, Apr  2 2020, 13:16:51) [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)]
```
Все дальнейшие действия нужно делать из текущего каталога.

## Запускаем и работаем со стендом

Поднимем виртуальные машины: `vagrant up`

Запустим плэйбук: `ansible-playbook nginx.yml`  
Так выглядит основной playbook `nginx.yml`:
```yml
---
- name: NGINX | Install and configure NGINX
  hosts: host1 host2
  become: true
  vars:
    nginx_listen_port: 8080

  tasks:
    - name: NGINX | Install EPEL Repo package from standart repo
      yum:
        name: epel-release
        state: present
      tags:
        - epel-package
        - packages

    - name: NGINX | Install NGINX package from EPEL Repo
      yum:
        name: nginx
        state: latest
      notify:
        - restart nginx
      tags:
        - nginx-package
        - packages

    - name: NGINX | Create NGINX config file from template
      template:
        src: ./templates/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify:
        - reload nginx
      tags:
        - nginx-configuration
    - name: mc | Install midnight-commander
      yum:
        name: mc
        state: latest

  handlers:
    - name: restart nginx
      systemd:
        name: nginx
        state: restarted
        enabled: yes

    - name: reload nginx
      systemd:
        name: nginx
        state: reloaded

    - name: start service
      systemd:
        name: nginx
        state: started

```
Таким образом, для проверки работы заходим на каждый хост и выполняем ss -tulpn
```
Netid  State      Recv-Q Send-Q          Local Address:Port                         Peer Address:Port
tcp    LISTEN     0      128                         *:8080                                    *:*                   users:(("nginx",pid=4485,fd=6),("nginx",pid=4410,fd=6))
```
