Title: Деплой WordPress на локальный и удаленный сервера
Date: 2021-07-25 23:30
Category: Deploy WordPress
Tags: tutorial, wordpress
Slug: deploy-wo
Authors: Alexandr Ivanov
Status: published

## Подготовка

На хосте c Ubuntu 18.04, на котором будет установлен WordPress должен быть установлен Ansible.
```
sudo apt update
sudo apt install python
sudo apt install ansible
```
На локальный хост клонируем репозиторий
```
git clone https://github.com/slavnyj/ansible-wordpress.git
cd ansible-wordpress
```
## Для локальной установки WordPress, выполняем
```
ansible-playbook --connection=local --inventory 127.0.0.1, site.yaml
```
## Для установки WordPress на удаленном сервере
#### Все действия производятся на локальном сервере, куда был склонирован репозиторий https://github.com/slavnyj/ansible-wordpress.git

1. Создаем пары ключей SSH
```
ssh-keygen
```
2. Настраиваем удаленный хост, где `username` - пользователь на удаленном сервере, под которым будет выполняться установка WordPress (см. `remote_user` ниже в таблице), `remote_host` - ip или dns удаленного сервера.
```
ssh-copy-id username@remote_host
```
3. Добавляем удаленный хост в Ansible Inventory File 
```
sudo nano /etc/ansible/hosts
```
Добавляем в файл запись вида
```
server_name ansible_host=[server_ip]
```
Например, `server1 ansible_host=10.3.54.3`

Запускам установку WordPress
```
ansible-playbook site.yaml server_name
```


В файле `/group_vars/all` можно установить
| Переменная  | Значение |
| ------------- | ------------- |
|mysql_root_password|пароль пользователя root БД |
|wp_db_name|имя БД, в которую будет установлен WordPress|  
|wp_db_user|имя пользователя БД WordPress|
|wp_db_password|пароль пользователя БД WordPress|
|mysql_port|порт на котором будет БД|
|server_hostname|имя сервера, используется в конфигурации Nginx для WordPress|
|remote_user|имя пользователя под которым будет выполняться установка и подключение у удаленному хосту|
|admin_email|email администратора сайта|
|timezone|временная зона, которая будет установлена на хосте|

На сервере будет установлен:
- WordPress (последняя версия)
- UFW
- MariaDB (последняя версия)
- NGINX (последняя версия)
- Postfix
- PHP-FPM 7.4
- phpMyAdmin
- Redis
- SFTP

