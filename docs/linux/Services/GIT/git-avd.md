# GIT

## Гитыч - Продвинутый (Advanced)

### Стандартный набор функций для работы с гитом

#### Настройка учетной записи в gitlab

```bash
   "Создаем нового(новых) пользователей в gitlab через web-морду"
   "Генерим ключ ssh для доступа к gitlab для user1 и user2"
    ssh-keygen -t ed25519 -f ~/.ssh/gitlablocal-user1
    ssh-keygen -t ed25519 -f ~/.ssh/gitlablocal-user2
   "Добавляем публичные ключи (файлы .pub) в соответствующие профили GitLab в разделе" 
     Settings > SSH Keys.
   "Настраиваем локально"  
   "Поинт в том, чтобы моджно было работать с обним и тем же gitlab/github под двумя разными пользователями" 
    [!] Важно, чтобы "Host" отличались друг от друга (даже если это учетки на одном gitlab сервере) 
    [!] Иначе, будет обрадатываться всегда только первая!

    "Файл ~/.ssh/config, чтобы система знала, какой ключ использовать для конкретного «псевдонима» хоста:"   
     cat ~/.ssh/config
     # user1 account
     Host gitlab.user1
     HostName gitlab.local
     User user1
     IdentityFile ~/.ssh/gitlablocal-user1     

     # user2 account
     Host gitlab.user2
     HostName gitlab.local
     User user2
     IdentityFile ~/.ssh/gitlablocal-user2
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

