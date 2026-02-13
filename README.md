## README.md для проекта Ansible WordPress

```markdown
# Ansible WordPress Deployment

Данный проект автоматизирует установку WordPress со стеком LAMP (Apache, MySQL, PHP) на серверы под управлением Debian/Ubuntu с помощью Ansible.

## Структура проекта

```
ansible-wordpress/
├── inventory/
│   ├── hosts                  # файл инвентаря (по умолчанию localhost)
│   └── group_vars/
│       └── all.yml             # общие переменные (пароли, имена БД)
├── roles/
│   ├── apache/                 # роль для установки Apache
│   │   ├── tasks/
│   │   │   └── main.yml
│   │   ├── handlers/
│   │   │   └── main.yml
│   │   └── templates/
│   │       └── wordpress.conf.j2
│   ├── mysql/                  # роль для установки MySQL
│   │   ├── tasks/
│   │   │   └── main.yml
│   │   └── handlers/
│   ├── php/                    # роль для установки PHP и модулей
│   │   ├── tasks/
│   │   │   └── main.yml
│   │   └── handlers/
│   └── wordpress/              # роль для скачивания и настройки WordPress
│       ├── tasks/
│       │   └── main.yml
│       └── templates/
│           └── wp-config.php.j2
├── site.yml                     # основной плейбук
├── log.txt                      # лог выполнения (приложен для проверки)
└── README.md                    # данный файл
```

## Требования

- Целевой хост: **Debian** или **Ubuntu** (чистая система).
- На управляющем узле (где запускается Ansible):
  - Установленный Ansible (версия 2.9 или выше)
  - Коллекция `community.mysql` (устанавливается командой ниже)
- Доступ к целевым хостам по SSH (если не localhost) с правами sudo.

## Переменные

Все переменные заданы в файле `inventory/group_vars/all.yml`:

```yaml
mysql_root_password: "StrongRootPass123"   # пароль root MySQL
wordpress_db_name: "wordpress"             # имя базы данных WordPress
wordpress_db_user: "wpuser"                # пользователь БД WordPress
wordpress_db_password: "StrongWpPass456"   # пароль пользователя БД
```

При необходимости измените их перед запуском.

## Инструкция по запуску

### 1. Подготовка окружения

Установите Ansible и необходимую коллекцию:

```bash
sudo apt update
sudo apt install ansible -y
ansible-galaxy collection install community.mysql
```

### 2. Настройка инвентаря

По умолчанию плейбук настроен на локальный запуск (`localhost`). Если вы хотите развернуть WordPress на удалённом сервере, отредактируйте файл `inventory/hosts`:

```
[wordpress]
<IP_адрес_сервера> ansible_user=<пользователь> ansible_become=yes
```

Например:

```
[wordpress]
192.168.1.100 ansible_user=root ansible_become=yes
```

Для локального запуска оставьте как есть:

```
[wordpress]
localhost ansible_connection=local
```

### 3. Запуск плейбука

Перейдите в каталог проекта и выполните:

```bash
cd ansible-wordpress
ansible-playbook -i inventory/hosts site.yml -K
```

- Флаг `-K` (или `--ask-become-pass`) запросит пароль для sudo (если требуется). Если sudo настроен без пароля, флаг можно опустить.
- Для удалённого хоста с парольной аутентификацией SSH добавьте также `-k`.

### 4. Проверка результата

После успешного выполнения откройте браузер и перейдите по адресу:

- При локальном запуске: `http://localhost`
- При удалённом запуске: `http://<IP_адрес_сервера>`

Вы должны увидеть страницу выбора языка WordPress. Далее можно завершить установку через веб-интерфейс.

## Ожидаемый результат

- Установлены и запущены Apache, MySQL, PHP с необходимыми модулями.
- Создана база данных и пользователь для WordPress.
- В директорию `/var/www/wordpress` скопированы файлы WordPress и сгенерирован `wp-config.php`.
- Веб-сервер настроен на обработку запросов к WordPress.

## Примечания

- Если порт 80 на целевой машине уже занят, измените порт в шаблоне `roles/apache/templates/wordpress.conf.j2` (замените `*:80` на нужный, например `*:8080`) и добавьте соответствующую директиву `Listen` в `/etc/apache2/ports.conf` (в плейбуке есть задача для этого, но нужно убедиться, что порт совпадает).
- В некоторых случаях может потребоваться настроить права на директорию `/var/www/wordpress` (вручную: `sudo chmod 755 /var/www/wordpress`), если после выполнения плейбука возникает ошибка 403. Это исправлено в последних версиях роли.

## Лог выполнения

Файл `log.txt` содержит вывод успешного выполнения плейбука на тестовой системе.

## Последовательность действий для проверяющего (пример)

1. Склонировать репозиторий:
   ```bash
   git clone https://github.com/edves634/Ansible-wordpress.git
   ```
2. Перейти в каталог проекта:
   ```bash
   cd Ansible-wordpress
   ```
3. Установить коллекцию community.mysql:
   ```bash
   ansible-galaxy collection install community.mysql
   ```
4. Убедиться, что инвентарь настроен на localhost (по умолчанию).
5. Запустить плейбук:
   ```bash
   ansible-playbook -i inventory/hosts site.yml -K
   ```
   (при необходимости ввести пароль sudo)
6. После завершения открыть в браузере `http://localhost` и проверить работу WordPress.
```

Этот README полностью соответствует заданию и содержит все необходимые разделы. Не забудьте заменить ссылку на репозиторий на актуальную.
