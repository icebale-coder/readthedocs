title: Установка MkDocs

# Установка MkDocs

#### 0. Установка python

MkDocs поддерживает python версий 3.5, 3.6, 3.7, 3.8.

=== "Linux"

	```bash
	# Debian/Ubuntu
	sudo apt install python3.8
	   
	# CentOS/Fedora
	sudo apt install python3.8
	```

=== "Windows"

   Нужно перейти на [страницу скачиваний Python](https://www.python.org/downloads/) и скачать поддерживаемую mkdocs версию

#### 1. Установка pip

**pip** - пакетный менеджер python, по умолчанию может быть не предустановлен

=== "Linux"

	```bash
	# Debian/Ubuntu
	sudo apt install pip3
	
	# CentOS/Fedora
	wget https://bootstrap.pypa.io/get-pip.py
	python get-pip.py
	```

=== "Windows"

	1. Откройте консоль python через Пуск/Поиск
	2. Скачайте скрипт [get-pip.py](https://bootstrap.pypa.io/get-pip.py)
	3. Запустите его из консоли python

#### 2. Установка mkdocs

Перейдите в консоль вашей системы, введите `python3 -m pip install mkdocs`

## Литература

* https://www.mkdocs.org/#installation - основной мануал по установке mkdocs