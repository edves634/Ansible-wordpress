```markdown
# Ansible WordPress Deployment

Проект для автоматической установки WordPress (Apache + MySQL + PHP) на Debian/Ubuntu с помощью Ansible.

## Переменные (inventory/group_vars/all.yml)

```yaml
mysql_root_password: "StrongRootPass123"
wordpress_db_name: "wordpress"
wordpress_db_user: "wpuser"
wordpress_db_password: "StrongWpPass456"
```

При необходимости измените пароли перед запуском.

## Запуск на локальной машине (по умолчанию)

1. Установите Ansible и коллекцию для MySQL:
   ```bash
   sudo apt update
   sudo apt install ansible -y
   ansible-galaxy collection install community.mysql
   ```

2. Перейдите в папку проекта и запустите плейбук:
   ```bash
   cd ansible-wordpress
   ansible-playbook -i inventory/hosts site.yml -K
   ```
   Флаг `-K` запросит пароль sudo (если требуется).

3. После завершения откройте браузер:  
   `http://localhost` — вы увидите страницу установки WordPress.

## Запуск на удалённом сервере

Отредактируйте файл `inventory/hosts`, указав IP и пользователя:

```
[wordpress]
192.168.1.100 ansible_user=your_user ansible_become=yes
```

Запуск с паролями SSH и sudo:
```bash
ansible-playbook -i inventory/hosts site.yml -k -K
```

## Ожидаемый результат

- Apache слушает порт 80 (или 8080, если меняли), MySQL запущен.
- В `/var/www/wordpress` распакован WordPress, создан `wp-config.php`.
- База данных `wordpress` и пользователь `wpuser` созданы.
- Сайт доступен в браузере.

## Примечания

- Если порт 80 занят, измените его в шаблоне `roles/apache/templates/wordpress.conf.j2` (замените `*:80` на `*:8080`) и добавьте `Listen 8080` в `/etc/apache2/ports.conf` (в плейбуке есть задача для этого).
- При ошибке 403 выполните вручную: `sudo chmod 755 /var/www/wordpress` (это исправлено в текущей версии роли).

## Лог выполнения

Файл `log.txt` содержит вывод успешного запуска плейбука.

## Последовательность действий для проверяющего

1. Склонировать репозиторий:
   ```bash
   git clone https://github.com/edves634/Ansible-wordpress.git
   ```
2. Перейти в каталог:
   ```bash
   cd Ansible-wordpress
   ```
3. Установить коллекцию:
   ```bash
   ansible-galaxy collection install community.mysql
   ```
4. Запустить плейбук (инвентарь уже настроен на localhost):
   ```bash
   ansible-playbook -i inventory/hosts site.yml -K
   ```
5. Открыть `http://localhost` и убедиться, что WordPress запущен.
```
