title: Конфигурация MkDocs

# mkdocs.yml

Главный конфигурационный файл MkDocs - `mkdocs.yml`, находящийся в корневой директории репозитория

#### Базовые параметры

```yaml
# Basic info
site_name: Reactive Deer's networking
site_description: Everything I know so far
site_author: Dmitriy Salatkin
repo_name: reactive-deer/knowledge_database
repo_url: https://github.com/reactive-deer/knowledge_database
```

* `site_name` - имя сайта, которое будет отображаться на вкладке браузера

  ![image-20210309111837725](/home/rea7/.config/Typora/typora-user-images/image-20210309111837725.png)

* `site_description` - описание сайта

* `site_author` - автор сайта

* `repo_name` - название репозитория, из которого формируется данный ReadTheDocs

* `repo_url` - ссылка на репозиторий

#### Параметры расширений

```yaml
# Extensions to MD
markdown_extensions:
  - markdown.extensions.admonition
  - markdown.extensions.sane_lists
  - markdown.extensions.footnotes
  - markdown.extensions.meta
  - markdown.extensions.def_list
  - markdown.extensions.abbr
  - markdown.extensions.tables
  - markdown.extensions.codehilite:
      guess_lang: false
  - markdown.extensions.toc:
      permalink: True
      separator: "_"
  - pymdownx.extra
  - pymdownx.arithmatex
  - pymdownx.caret
  - pymdownx.critic
  - pymdownx.details
  - pymdownx.inlinehilite
  - pymdownx.magiclink
  - pymdownx.mark
  - pymdownx.keys
  - pymdownx.smartsymbols
  - pymdownx.superfences
  - pymdownx.tabbed
  - pymdownx.tilde
  - pymdownx.betterem:
      smart_enable: all
  - pymdownx.tasklist:
      custom_checkbox: true
  - pymdownx.emoji
```

#### Ссылки на социальные сети

Находятся внизу страницы, иконки берутся из расширения fontawesome

![image-20210309112307454](/home/rea7/.config/Typora/typora-user-images/image-20210309112307454.png)

```yaml
# Social network links
extra:
  social:
    - icon: fontawesome/brands/github
      link: https://github.com/reactive-deer
    - icon: fontawesome/brands/vk
      link: https://vk.com/reactive_deer
    - icon: fontawesome/brands/telegram
      link: https://t.me/reactive_deer
```

#### Параметры темы MkDocs

```yaml
# MKDocs theme params
theme:
    name: material
    language: ru
    palette:
        primary: black
        accent: amber
    font:
      text: "Roboto"
      code: "Roboto Mono"
    features:
      - tabs
      - instant
    favicon: img/icon.png
    logo: img/icon.png
#extra_css:
#  - stylesheets/extra.css
```

#### Плагины

```yaml
# Used plugins
plugins:
  - search
  - minify:
      minify_html: true
  - git-revision-date-localized:
      type: timeago
  - awesome-pages:
      collapse_single_pages: true
      strict: false
```

#### Авторские права

```yaml
# Copyright
copyright: "Dmitriy Salatkin &copy; 2021"
```

