# GIT

## Гитыч - Основы

### Стандартный набор функций для работы с  гитом

#### Заведение доступа на github и настройка git

```bash
# 1. Создаем пользователя на github-e

# 2. Создаем ключ ssh
  ssh-keygen -t rsa -b 4096 -C "username.user@example.com"

  в директории /home/ИМЯ_ПОЛЬЗОВАТЕЛЯ/.ssh
  создаются файлы:
  id_rsa
  id_rsa.pub

# 3. Привязываем публичный ключ к учетке github
  Содержимое файла id_rsa.pub помещаем в раздел учетной записи на github 
  "Settings" -> "SSH and GPG keys" -> "New SSH key"

# 4. Настраиваем git. Задаем имя пользователя и почтовый ящик в конфиге git
  git config --global user.name "username"
  git config --global user.email "username.user@example.com"

# 5. Запускаем агента ssh
  eval "$(ssh-agent -s)"

# 6. Добавить ключ в ssh-агент
  ssh-add ~/.ssh/id_rsa

# Проверяем
  ssh -T git@github.com

Если выдается ошибка вида: ssh -T github.com
  Unable to negotiate with 140.82.121.3 port 22: 
  no matching key exchange method found. 
  Their offer: curve25519-sha256,curve25519-sha256@libssh.org,
  ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256

#!!! То необходимо 
# в файл ./ssh/config 
Добавить: 

host *
    stricthostkeychecking no
    kexalgorithms diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,
    diffie-hellman-group1-sha1,diffie-hellman-group16-sha512,diffie-hellman-group-exchange-sha256
    ciphers aes128-cbc,3des-cbc,aes192-cbc,aes256-cbc,aes128-ctr

#После этого выдается:
~/.ssh$ ssh -T git@github.com
Hi icebale-coder! You've successfully authenticated, but GitHub does not provide shell access.

```

#### Клонирование существующего репозитария:
Если на github уже есть репозитарий, то можно склонировать его.

```bash
  git clone git@github.com:icebale-coder/readthedocs.git
```

#### Модификация/Редактирования данных в репозитарии:
```bash
# 1. Модифим/Редактируем данные репозитария

# 2. Подхватываем текщие изменения для комитта
# to include in what will be committed
git add .

# 3. Фиксируем текущие изменения в отдельном комитте
# commit 
git commit -m "My diff"

# 4. Отправляем изменения на github
# to push to github repo
git push
```

