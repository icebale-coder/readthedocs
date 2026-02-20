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
 
 "Команда git add - добавляет все измененные и новые файлы (кроме удаленных)" 
 "из текущей директории и всех поддиректорий в индекс."
 "Команда предназначена для перемещения изменений из рабочего каталога" 
 "в индекс (staging area) - область подготовленных файлов."
 git add .
 
 "Команда git commit - создает «снимок» (snapshot) текущего состояния файлов," 
 "подготовленных в области промежуточного хранения (staging area) с помощью git add."
 "Это основная единица истории проекта, фиксирующая изменения, автора, дату и сообщение,"
 "что позволяет управлять версиями и откатывать код"
 git commit -m "Initial project version"
 
 "Команда "git remote add origin <URL-адрес>" связывает локальный репозиторий с удаленным" 
 "(например, на GitHub/GitLab), присваивая этому удаленному серверу имя origin."
 git remote add origin git@gitlab.user1:user1/test1.git 

 "Если адреса remote ориджина указан некорректно (ошибка при вводе предыдущей комианды),"
 "то его можно поменять, путем у даления и задания нового."
 git remote remove origin
 git remote add origin git@gitlab.user1:user1/test2.git


 "Команда "git push --set-upstream origin master" (или сокращенно "git push -u origin master")" 
 "отправляет локальные изменения ветки master на удаленный репозиторий origin и устанавливает связь между ними."
 git push --set-upstream origin master 
```

#### 3. Работа с репозиторием - Добаление/удаление файлов в проекте  
```bash
 "После этого можно модифицировать проект, а
 "также отправлять изменения в удаленный репозиторий набором команд"
 git add .
 git commit -m "New comment"
 git push
```

#### 4. Клонирование существующего проекта
```bash
 "Для клонирования репозитария должно быть выполнено 2 условия:"
  1. Должна быть учетная запись на gitlab
  2. Должны быть разрешения на доступ у данному репозиторю (invite у пользователя или общий достук у репозитария)
     - Project -> Members -> Invite members

 "Клонирование"
  git clone git@gitlab.local:user1/test1.git
```

#### 5. Создание новой ветки
```bash
 "Создание локально новой ветки "branch1" и переключение на нее"
 git checkout -b branch1

 "Добаление/удаление файлов и их содержимого в новой ветке"

 "Добавления новых файлов индексирование git"
 git add .
 git commit -m "add new file"

 "Отправка новой ветки на удаленный сервер (аля git push --set-upstream origin branch1)"
 git push -u origin branch1
```

#### 6. Переключение между ветками
```bash
 "Переключаемся на ветку main"
 git checkout main

 "Переключимться на ветку branch1" 
 git checkout branch1
```

#### 7. Удаление ветки на примере ветки "branch1"
```bash
 "Переключаемся на другую ветку (нельзя удалить ветку, в которой вы находитесь):"
 git checkout master

 "Удаляем local ветку"
 git branch -d branch1

 "Удаляем remote ветку"
 git push origin --delete branch1
```

#### 8. MR (mirge request) - объединение веток
```bash
 "Данная процедура позволяет объединить 2 ветки одного репозитория в одну."
 Прозводится это чз web интерфейс gitlab/github.
 Merge Requests -> Create Merge Request -> выбираем "Source branch" и "Target branch" -> Compare -> 
 Create Merge Request -> (устанавливаем галку "delete source branch" если это необходимо) -> В "changes" можно посмотреть что произойдет при merge -> Merge
 ИТОГО: ветка "branch1" объединилась с веткой "master" и в результате ветка "branch1" еще и удалилась.   
```

