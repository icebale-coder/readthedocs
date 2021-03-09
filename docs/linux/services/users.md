title: Добавление пользователей

# Добавление пользователей

### Подготовка машины

Проверить установлен ли пакет `ssh`, установить если нет, в `/etc/ssh/sshd_config` проверить:

```bash
Port $PORT # Номер порта, на котором слушает OpenSSH сервер
ListenAddress $ADDRESS # Адреса, на которых слушает OpenSSH сервер
			  0.0.0.0 # - на всех IPv4
			  10.10.10.10 # - на определённом 10.10.10.10 IPv4
			  :: # - на всех IPv6
			  2001:a:b:900 # - на определённом 2001:a:b:900 IPv6
PermitRootLogin {yes|no|prohibit-password} # разрешение/запрет входа под рутом
```

Также, чтобы каждый раз при sudo не вводить пароль, нужно сделать следующее:

```bash
sudo echo "%sudo ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers
```

### Шаблон для добавления пользователей

```bash
sudo useradd -m -s /bin/bash -p '$SHA512_PASSW' $USERNAME
sudo mkdir /home/$USERNAME/.ssh
sudo echo '$PUBKEY' > /home/$USERNAME/.ssh/authorized_keys
sudo usermod -aG sudo $USERNAME
```