# GIT Advanced

## Гитыч - Продвинутый (Advanced)

### Стандартный набор функций для работы с гитом

#### 1. Настройка учетной записи в gitlab

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

#### 2. Инициируем новый репозиторий

```bash
     mkdir test1
     cd ./test1

     "Команда инициализации репозитория"
     git init
     
     "Команда git add - добавляет все измененные и новые файлы (кроме удаленных) из текущей директории и всех поддиректорий в индекс.
     Команда предназначена для перемещения изменений из рабочего каталога в индекс (staging area) #(область подготовленных файлов)."
     git add .
     
     "Команда git commit - создает «снимок» (snapshot) текущего состояния файлов, подготовленных в области промежуточного хранения (staging area) с помощью git add.
     # Это основная единица истории проекта, фиксирующая изменения, автора, дату и сообщение, что позволяет управлять версиями и откатывать код"
     git commit -m "Initial project version"
     
     "Команда "git remote add origin <URL-адрес>" связывает локальный репозиторий с удаленным (например, на GitHub/GitLab), присваивая этому удаленному серверу имя origin."
     git remote add origin git@gitlab.user1:user1/test1.git 
    
     "Если адреса remote ориджина указан некорректно (ошибка при вводе предыдущей комианды), то его можно поменять, путем у даления и задания нового."
     git remote remove origin
     git remote add origin git@gitlab.user1:user1/test2.git


     "Команда "git push --set-upstream origin master" (или сокращенно "git push -u origin master") 
     отправляет локальные изменения ветки master на удаленный репозиторий origin и устанавливает связь между ними."
     git push --set-upstream origin master 
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

