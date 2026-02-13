# Ansible WordPress Deployment

Автоматизация установки WordPress со стеком LAMP (Apache, MySQL, PHP) на Debian/Ubuntu с помощью Ansible.

## Структура проекта

- `inventory/hosts` – файл инвентаря (по умолчанию localhost)
- `inventory/group_vars/all.yml` – переменные (пароли, имена БД)
- `roles/` – роли для каждого компонента (apache, mysql, php, wordpress)
- `site.yml` – основной плейбук
- `log.txt` – лог успешного выполнения

## Требования

- Целевой хост: Debian или Ubuntu (чистая система)
- Управляющий узел: Ansible (версия 2.9+), коллекция community.mysql
- Доступ к целевым хостам по SSH с правами sudo (если не localhost)

## Переменные

Файл `inventory/group_vars/all.yml`:

```yaml
mysql_root_password: "StrongRootPass123"
wordpress_db_name: "wordpress"
wordpress_db_user: "wpuser"
wordpress_db_password: "StrongWpPass456"
Инструкция по запуску
1. Подготовка
bash
sudo apt update
sudo apt install ansible -y
ansible-galaxy collection install community.mysql
2. Настройка инвентаря
По умолчанию плейбук работает на локальной машине (localhost). Если нужен удалённый сервер, отредактируйте inventory/hosts:

text
[wordpress]
192.168.1.100 ansible_user=root ansible_become=yes
3. Запуск плейбука
bash
cd ansible-wordpress
ansible-playbook -i inventory/hosts site.yml -K
Флаг -K – запрос пароля sudo (если требуется)

Для удалённого хоста с паролем SSH добавьте -k

4. Проверка
Откройте браузер:

Локально: http://localhost

Удалённо: http://<IP_адрес_сервера>

Вы должны увидеть страницу выбора языка WordPress.

Возможные проблемы
Ошибка 403 Forbidden: выполните sudo chmod 755 /var/www/wordpress

Порт 80 занят: измените порт в шаблоне roles/apache/templates/wordpress.conf.j2 и добавьте Listen в ports.conf (задача уже есть в роли)

Лог выполнения
Файл log.txt приложен и содержит вывод успешного запуска на тестовой системе.
