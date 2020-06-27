# Описание

Стенд для практики к уроку «Автоматизация администрирования. Ansible.»  

Разворачивается два сервера: `host1` и `host2`. При развертывании Vagrant запускается Ansible [playbook](provisioning/playbook.yml). 

# Инструкция по применению
## Перед запуском

Если вы еще не настроили Vagrant и VirtualBox, то вот краткая [инструкция](https://gitlab.com/otus_linux/docs/blob/master/vagrant_quick_start.md).

Далее необходимо установить Ansible. Это можно сделать по [инструкции](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-release-via-dnf-or-yum). Или достаточно: `yum install ansible`

Проверим что Ansible установлен: `ansible --version`

Все дальнейшие действия нужно делать из текущего каталога.

## Запускаем и работаем со стендом

Поднимем виртуальные машины: `vagrant up`

Запустим роль: `ansible-playbook fail2ban.yml`  
Так выглядит основной playbook `fail2ban.yml`, который уже в свою очередь ссылается на роль `fail2ban`:
```yml
- name: Fail2Ban # Имя таски
  hosts: all # На каких хостах будет выполняться
  become: True # Нужно ли нам sudo
  roles: # Директива, указывающая, что будут использваоны роли
    - fail2ban # Имя роли по каталогу
```

Если мы поправили конфигурационные файлы и хотим их заново скопировать на сервера:
`ansible-playbook fail2ban.yml --tags "configuration"`

Что еще можно попробовать:
```bash
ansible all -m ping # Пингануть сервера Ansibl-ом
ansible all -m setup # Собрать данные с серверов
ansible all -m setup -a 'filter=ansible_eth[0-2]' # Собрать данные и показать только сетевые интефейсы eth[0-2]
ansible all -m setup -a 'filter=ansible_os_family' # Собрать данные и показать только семейство ОС
ansible all -a 'uname -r' # Выполнить произвольную команду на серверах
```

# Некоторые полезные команды

Создать дерево каталогов для роли:
```bash
ansible-galaxy init <rolename> 
```

Получить документацию по модулю:
```bash
ansible-doc <modulename>
```

Проверить синтаксис:
```bash
ansible-playbook fail2ban.yml --syntax-check
```

Посмотреть список хостов на которых будет выполняться роль. При этом сами 
таски не выполняются.
```bash
ansible-playbook fail2ban.yml --list-hosts
```

Посмотреть все таски, которые входят в роль:
```bash
ansible-playbook fail2ban.yml --list-tasks
```

Посмотреть все теги в роли:
```bash
ansible-playbook fail2ban.yml --list-tags
```