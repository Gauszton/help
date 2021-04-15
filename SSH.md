### Notatki do SSH na platformie linux/unix

### Kopiowanie kluczy i przesyłanie kluczy na zdalny host 

Metoda 1:</br>
```ssh-copy-id -i ~/.ssh/s_kluczPodGita user@host```</br>

Metoda 2:</br>
```cat ~/.ssh/s_kluczPodGita```</br>

Na zdalnym hoście:</br>
```mkdir -p ~/.ssh```</br>
```nano ~/.ssh/authorized_keys```</br>

Metoda 3:</br>
Skopiowanie klucza do przybornika</br>
```pbcopy < ~/.ssh/s_kluczPodGita```</br>

Na zdalnym hoście:</br>
```cat ~/.ssh/s_kluczPodGita | ssh username@remote_host "mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod -R go= ~/.ssh && cat >> ~/.ssh/authorized_keys"```</br>
```nano ~/.ssh/authorized_keys ```</br>

### Uprawnienia na pliku z kluczami prywatnymi

Dla katalogu ```~/.ssh/```</br>
```chmod 700 ~/.ssh/```</br>
```chown -R $USER:$USER ~/.ssh```</br>

Dla wszystkich kluczy</br>
```chmod 600 ~/.ssh/*```</br>

Dla pliku ```~/.ssh/authorized_keys```</br>
```chmod 644 ~/.ssh/authorized_keys```</br>

### Instalacja serwera SSH

Ubuntu:</br>
```sudo apt-get install openssh-server -y```

Debian:</br>
```sudo apt-get install openssh-server -y```

CentOS 7:</br>
```sudo yum –y install openssh-server```

CentOS 8:</br>
```sudo dnf –y install openssh-server```

RHEL:</br>
```sudo yum -y install openssh-server```

Fedora: </br>
```sudo dnf install openssh-server -y```

#### Podstawowe komendy na serwerze SSH 

Status usługi:</br>
```sudo service ssh status``` </br>

Start/Stop/Restart usługi:</br>
```sudo service ssh start```</br>
```sudo service ssh stop```</br>
```sudo service ssh restart```</br>

Uruchomienie usługi przy starcie systemu:</br>
```sudo systemctl enable ssh```</br>
Dodanie wykluczenia dla ufw w Ubuntu:</br>
```sudo ufw allow 22```</br>


##### Ważne pliki 
Klient:</br>
```~/.ssh/authorized_keys```,</br>

```~/.ssh/config```,</br>

```~/.ssh/known_hosts```,</br>

Server:</br>
```/etc/ssh/ssh_config```, </br>

```/etc/ssh/sshd_config```</br>

##### Plik config po stronie klienta </br>
Tworzymy plik: ```~/.ssh/config```</br>

Dodajemy nasze parametry: </br>
```
Host test
  HostName 123.123.123.123
  Port xxxx
  IdentityFile ~/.ssh/s_kluczPodGita
  User mojuser 
```
Możemy się logować z poziomu terminala:</br>
``` ssh test ```

### Podstawowe utwardzenie serwera SSH

Wszystkich modyfikacji w tym przypadku dokonujemy w pliku ```/etc/ssh/sshd_config```</br>

Zamieniamy domyślnego portu ssh </br>
```Port 22``` na ```Port xx```</br>

Dodanie HostKey:</br>
```
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
```

Dodanie dopuszczone metody szyfrowania: </br>
```
# Ciphers and keying
KexAlgorithms curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha256
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr
MACs hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,umac-128-etm@openssh.com
HostKeyAlgorithms ssh-ed25519,ssh-ed25519-cert-v01@openssh.com,sk-ssh-ed25519@openssh.com,sk-ssh-ed25519-cert-v01@openssh.com,rsa-sha2-256,rsa-sha2-512,rsa-sha2-256-cert-v01@openssh.com,rsa-sha2-512-cert-v01@openssh.com
```

Włączenie generowania logów dla ssh:</br>
```
#Logging
SyslogFacility AUTH
LogLevel INFO
```
Wyłączenie logowania roota:</br>
```PermitRootLogin no```

Wyłączenie użycia hasła do logowania: </br>
```PasswordAuthentication no```

Wyłączenie komunikacji x11 jeśli nie jest pogrzebana </br>
```X11Forwarding no```

Włączenie użycia PAM </br>
```UsePAM yes```

Włączenie automatycznego wylogowywani usera po określonym czasie </br>
```ClientAliveInterval 300```

### Test / Audyt serwera SSH

Testy serwera ssh to temat drażliwy, ja dla własnego sprawdzenia wykorzystywałem ten skrypt:</br>
https://github.com/jtesta/ssh-audit

### Przydatne linki, cześć z nich pokrywa się z tematem ale rozwija elementy o których nie wspominam: 
https://www.ssh.com/academy/ssh/keygen</br>
https://morfikov.github.io/post/uwierzytelniajace-klucze-ssh/</br>
https://pomoc.home.pl/baza-wiedzy/logowanie-ssh-gdzie-znalezc-klucze-rsa-do-polaczenia</br>
https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys</br>
https://chrisjean.com/ssh-tutorial-for-ubuntu-linux/</br>