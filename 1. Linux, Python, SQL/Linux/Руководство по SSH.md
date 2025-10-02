<h2>Руководство по SSH</h2>
<h4>1. Базовое подключение</h4>

```bash
ssh example.com                       # Подключение под текущим пользователем
ssh username@example.com              # Подключение под конкретным пользователем
ssh username@example.com -p 2222      # Подключение на нестандартном порту
ssh username@example.com 'ls -l /tmp' # Выполнение одной команды без входа в shell
```
<h4>2. Использование SSH ключей</h4>

<h5>Преимущества</h5>

- Высокая безопасность - ключи практически невозможно подобрать брутфорсом
- Удобство - не нужно вводить пароль при каждом подключении
- Автоматизация - скрипты могут работать без вмешательства пользователя
- Делегирование доступа - можно управлять доступом через authorized_keys
- Аудируемость - каждый ключ можно идентифицировать

<h5>Как работает</h5>

У пользователя есть пара ключей: публичный и приватный, публичный ключ передается на сервера, которыми пользователь хочет управлять, приватный хранится у пользователя в каталоге `~/.ssh`

1. Клиент сообщает серверу, что хочет аутентифицироваться по ключу
2. Сервер генерирует случайное число (challenge) и шифрует его публичным ключом
3. Клиент расшифровывает challenge своим приватным ключом
4. Клиент возвращает расшифрованное число серверу
5. Сервер проверяет, что число совпадает с исходным и разрешает доступ

<h5>Генерация</h5>

```bash
ssh-keygen -t ed25519 -C "comment"                            # Ed25519
ssh-keygen -t rsa -b 4096 -C "comment"                        # RSA 4096 бит
ssh-keygen -t ecdsa -b 521 -C "comment"                       # ECDSA
ssh-keygen -t ed25519 -a 100 -C "comment" -f ~/.ssh/work_key  # Ed25519 все параметры
```
Параметры:
- `-t` - тип алгоритма
- `-b` - длина ключа в битах (для RSA)
- `-C` - комментарий для идентификации
- `-f` - путь и имя для файлов ключей
- `-a` - количество циклов KDF (увеличивает стойкость к брутфорсу)

<h5>Копирование публичного ключа на сервер</h5>

```bash
ssh-copy-id -i ~/.ssh/work_key.pub user@server.com            # автоматическое
ssh-copy-id -i ~/.ssh/work_key.pub -p 2222 user@server.com    # автоматическое (с портом)
cat ~/.ssh/work_key.pub | ssh user@server.com "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"    # ручное
```

Публичный ключ добавляется в файл `~/.ssh/authorized_keys` на сервере

<h5>Настройка прав доступа</h5>

```bash
# На КЛИЕНТЕ (ваш компьютер)
chmod 700 ~/.ssh
chmod 600 ~/.ssh/work_key          # приватный ключ
chmod 644 ~/.ssh/work_key.pub      # публичный ключ
chmod 644 ~/.ssh/known_hosts
chmod 644 ~/.ssh/config

# На СЕРВЕРЕ (удаленный сервер)
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 644 ~/.ssh/known_hosts
```

<h5>Использование ключей</h5>

Явное указание ключа:
```bash
ssh -i ~/.ssh/work_key user@server.com
ssh -i ~/.ssh/work_key -p 2222 user@server.com
```

Использование SSH-агента:
```bash
eval "$(ssh-agent -s)"     # Запуск агента
ssh-add ~/.ssh/work_key    # Добавление ключа в агент
ssh-add -l                 # Просмотр загруженных ключей
ssh-add -D                 # Удаление всех ключей из агента
```

Автоматическое использование ключей с помощью `~/.ssh/config`:
```bash
Host work-server
    HostName server.company.com
    User john
    Port 22
    IdentityFile ~/.ssh/work_key
    IdentitiesOnly yes

Host github.com
    User git
    IdentityFile ~/.ssh/github_key

Host *
    AddKeysToAgent yes
    UseKeychain yes        # для macOS - сохраняет passphrase в keychain
    ServerAliveInterval 60
```

Подключение после настройки `~/.ssh/config`:
```bash
ssh work-server
```

<h5>Пример полного workflow</h5>

```bash
# 1. Генерация ключа
ssh-keygen -t ed25519 -C "alice@company.com - desktop" -f ~/.ssh/company_desktop

# 2. Установка правильных прав
chmod 700 ~/.ssh
chmod 600 ~/.ssh/company_desktop
chmod 644 ~/.ssh/company_desktop.pub

# 3. Копирование на сервер
ssh-copy-id -i ~/.ssh/company_desktop.pub alice@company-server.com

# 4. Добавление в агент
ssh-add ~/.ssh/company_desktop

# 5. Настройка конфига
echo 'Host company
    HostName company-server.com
    User alice
    IdentityFile ~/.ssh/company_desktop' >> ~/.ssh/config

# 6. Подключение
ssh company
```
<h4>3. SSH-туннелирование</h4>

SSH-туннелирование (проброс портов) - это техника, позволяющая создавать зашифрованные соединения между компьютерами через SSH-протокол для безопасной передачи данных.

<h5>Локальный проброс портов (Local Port Forwarding)</h5>

Назначение: доступ к удаленному серверу как к локальному
```bash
# Базовый синтаксис
ssh -L [локальный_порт]:[целевой_хост]:[целевой_порт] пользователь@ssh_сервер

# Пример: доступ к удаленному веб-серверу как к localhost:8080
ssh -L 8080:localhost:80 user@webserver.com

# Пример: доступ к БД на другом сервере через SSH-сервер
ssh -L 3306:db-server.internal:3306 user@bastion.company.com
```
<h5>Удаленный проброс портов (Remote Port Forwarding)</h5>

Назначение: предоставление доступа к локальному сервису извне
```bash
# Базовый синтаксис
ssh -R [удаленный_порт]:[локальный_хост]:[локальный_порт] пользователь@ssh_сервер

# Пример: сделать локальный веб-сервер доступным с удаленного сервера
ssh -R 8080:localhost:3000 user@remote-server.com

# Теперь на remote-server.com можно обратиться к localhost:8080
# и получить доступ к вашему локальному сервису на порту 3000
```

<h5>Динамический проброс портов (SOCKS proxy)</h5>

Назначение: создание уникального прокси для всего трафика
```bash
# Создание SOCKS прокси на порту 1080
ssh -D 1080 user@remote-server.com

# Использование в браузере: указать SOCKS proxy localhost:1080
# Весь трафик браузера будет идти через SSH-туннель
```

<h5>Настройки</h5>

Фоновый режим и управление сессиями:
```bash
# Создание туннеля в фоне без открытия shell
ssh -f -N -L 8080:localhost:80 user@server.com

# С сохранением сессии для управления
ssh -M -S /tmp/web-tunnel -f -N -L 8080:localhost:80 user@server.com

# Проверка статуса туннеля
ssh -S /tmp/web-tunnel -O check user@server.com

# Завершение туннеля
ssh -S /tmp/web-tunnel -O exit user@server.com
```

Опции:
- `-f` - переход в фоновый режим
- `-N` - не выполнять удаленные команды
- `-M` - включить master mode
- `-S` - указать socket для управления

Настройка в `~/.ssh/config`:
```bash
Host web-tunnel
    HostName server.company.com
    User username
    LocalForward 8080 localhost:80
    LocalForward 3306 db-host:3306
    ExitOnForwardFailure yes
    ServerAliveInterval 60
    ServerAliveCountMax 3

Host db-tunnel
    HostName db-bastion.com
    User dbuser
    LocalForward 5432 postgres-internal:5432
    IdentityFile ~/.ssh/db_key

Host dynamic-proxy
    HostName proxy-server.com
    User proxyuser
    DynamicForward 1080
    Compression yes
```

Использование:
```bash
ssh -f -N web-tunnel    # запуск туннеля в фоне
ssh -f -N dynamic-proxy # запуск SOCKS прокси
```

Ограничение доступа к туннелям:
```bash
# Разрешить подключение к туннелю только с localhost (по умолчанию)
ssh -L 8080:remote:80 user@server.com

# Разрешить подключение с других интерфейсов
ssh -L 0.0.0.0:8080:remote:80 user@server.com

# Разрешить подключение только с определенного IP
ssh -L 192.168.1.100:8080:remote:80 user@server.com
```
Мониторинг активных туннелей:
```bash
# На клиенте - просмотр установленных туннелей
ss -tlnp | grep ssh
netstat -tlnp | grep ssh

# На сервере - просмотр активных пробросов
sudo lsof -i -n | grep ssh
sudo netstat -tlnp | grep ssh
```

<h4>3. Настройка и использование git через SSH</h4>

1. Сгенерировать ключи
2. Добавить в файл `~/.ssh/config`:
```bash
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/personal_key
    IdentitiesOnly yes
```
3. Добавить публичный ключ на сервер с репозиторием (для github: "Settings" -> "SSH and GPG keys" -> "New SSH key" -> Вставляем содержимое файла personal_key.pub в поле "Key" -> "Add SSH key"