# OpenLDAP Ansible Playbook

## Описание
Этот плейбук Ansible разворачивает OpenLDAP сервер на Ubuntu LTS и состоит из 4 ролей:

1. **openldap** – устанавливает OpenLDAP сервер и выполняет начальную инициализацию.  
2. **openldap-modules** – добавляет модули `refint` и `memberof` для OpenLDAP сервера.  
3. **openldap-config** – создаёт ACL, организационные единицы (`users` и `groups`), добавляет пользователей и группы.  
4. **openldap-verify** – проверяет корректность установки и аутентификацию пользователей.


## Запуск

1. Склонировать репозиторий:

```bash
git clone https://github.com/Hestci/ldap-setup.git
cd ldap-setup
```

2. Передать SSH-ключи на сервер для авторизации:

```bash
ssh-copy-id 192.168.1.110
```

3. Настроить `hosts.ini`:

```ini
[ldap-server]
192.168.1.110 ansible_user=hest
```

4. Создать зашифрованный файл с секретами:

``` bash
ansible-vault create vault.yml
```

Внутри vault.yml можно хранить, например:
```yaml
ldap_admin_pass: "adminpass"
user_passwords:
  user1: "user1pass"
  user2: "user2pass"
```

5. Запустить playbook и ввести пароль sudo и пароль от vault.yml:

```bash
ansible-playbook -i hosts.ini ldap-setup.yml --ask-become-pass --ask-vault-pass
```

6. Проверка успешного добавления пользователей:
```text
TASK [openldap-verify : Проверка аутентификации пользователей] ***************************
ok: [192.168.1.110] => (item={'uid': 'user1', 'cn': 'User One', 'sn': 'User1', 'uidNumber': 10001, 'gidNumber': 5001, 'homeDirectory': '/home/user1', 'loginShell': '/bin/bash', 'password': 'user1pass'})
ok: [192.168.1.110] => (item={'uid': 'user2', 'cn': 'User Two', 'sn': 'User2', 'uidNumber': 10002, 'gidNumber': 5002, 'homeDirectory': '/home/user2', 'loginShell': '/bin/bash', 'password': 'user2pass'})
```
