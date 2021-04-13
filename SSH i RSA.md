### Notatki do SSH / RSA na platformie linux/unix

Stawiając serwer czy konfigurując nowy komputer, za każdym razem muszę wyszukać i skonfigurować klucze RSA, usługę serwera SSH oraz odpowiednio je zabezpieczyć. Tym razem postanowiłem spisać kroki jakie wykonuje, tak by powstała mała instrukcja. 
Jest to moja konfiguracja, dotyczy VPS czy VM w mojej sieci, nie musi być odpowiednia dla Ciebie.
 
Jeśli zauważysz błąd, chcesz coś dodać, śmiało napisz. Będę poprawiał / dodawał co jakiś czas nowości do instrukcji tak by była aktualna. 

#### Rodzaje kluczy SSH

##### Polecane:
```RSA``` - najpopularniejszy i najczęściej wykorzystywan.  Zalecana minimalna wersja klucza to 2048 bit (możliwe, że nie będzie niedługo wspierana). Zalecana wybrać wersje 4096 bit.
```EdDSA``` - poprawiona wersja ECDSA, implementuje Curve25519 w EdDSA można użyć w Ed25519.

##### Nie polecane:
```DSA``` - nie jest dostępny domyślnie w OpenSSH 7.0 ze względu na niewystarczający poziom skomplikowania, 
```ECDSA``` - implementacja DSA w formie krzywej eliptycznej, ze względu na poziom skomplikowania nie jest zalecany tak samo jak DSA, 

##### Generowanie klucza RSA

**```ssh-keygen -t rsa -b 4096 -o -f ~/.ssh/s_kluczPodGita -C "$(whoami)@$(hostname)-$(date -I)"```**

  ```-t dsa | ecdsa | ed25519 | rsa``` = rodzaj algorytmu, zaleca się wybranie RSA,
  ```-b``` = wskazuje moc szyfrowania w bitach, dla RSA zalecana się 4096 bit,
  ```-a``` = parametr pozwalający na zidentyfikowanie ilość testów jakim zostanie poddany klucz, domyślnie 100, można pomijać lub ustawić więcej niż 100, 
  ```-f ~/.ssh/s_kluczPodGita``` = ścieżka oraz nazwa pliku wynikowego z kluczami, bez rozszerzenia utworzony zostanie klucz prywatny, z rozszerzeniem .pub klucz publiczny, 
  ```-C "$(whoami)@$(hostname)-$(date -I)"``` = komentarz, może być dowolny, w wym przypadki dodany na końcu klucza zostanie nazwa username@hostname-data
  ```-o``` = generowanie klucza w nowym formacie OpenSSH

  ---
  Dodatkowo:

 ```-m PEM``` = sformatuj klucz w formie PEM
 ```-N mypassphrase``` = Passphrase, możemy dodać tutaj lub w trakcie generowania pary kluczy,

##### Generowanie klucza publicznego na podstawie prywatnego

```ssh-keygen -y -t rsa -f ~/.ssh/s_kluczPodGita > ~/.ssh/s_kluczPodGita.pub```

##### Sprawdzanie odcisku klucza

```ssh-keygen -l -f ~/.ssh/s_kluczPodGita```

##### Dodanie Passphrase do klucza prywatnego

```ssh-keygen -p -f ~/.ssh/s_kluczPodGita```

##### Usuniecie Passphrase z klucza prywatnego

```ssh-keygen -p -f ~/.ssh/s_kluczPodGita```

### Kopiowanie kluczy i przesyłanie kluczy na zdalny host 

Metoda 1:
```ssh-copy-id -i ~/.ssh/s_kluczPodGita user@host```

Metoda 2:
```cat ~/.ssh/s_kluczPodGita```

Na zdalnym hoście:
```mkdir -p ~/.ssh```
```nano ~/.ssh/authorized_keys```

Metoda 3:
Skopiowanie klucza do przybornika
```pbcopy < ~/.ssh/s_kluczPodGita```

Na zdalnym hoście:
```cat ~/.ssh/s_kluczPodGita | ssh username@remote_host "mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod -R go= ~/.ssh && cat >> ~/.ssh/authorized_keys"```
```nano ~/.ssh/authorized_keys ```

### Uprawnienia na pliku z kluczami prywatnymi

Dla katalogu ```~/.ssh/```
```chmod 700 ~/.ssh/```
```chown -R $USER:$USER ~/.ssh```

Dla wszystkich kluczy
```chmod 600 ~/.ssh/*```

Dla pliku ```~/.ssh/authorized_keys```
```chmod 644 ~/.ssh/authorized_keys```

### Instalacja serwera SSH

Ubuntu:
```sudo apt-get install openssh-server -y```

Debian:
```sudo apt-get install openssh-server -y```

CentOS 7:
```sudo yum –y install openssh-server```

CentOS 8:
```sudo dnf –y install openssh-server```

RHEL:
```sudo yum -y install openssh-server```

Fedora: 
```sudo dnf install openssh-server -y```

#### Podstawowe komendy na serwerze SSH 

Status usługi:
```sudo service ssh status```

Start/Stop/Restart usługi:
```sudo service ssh start```
```sudo service ssh stop```
```sudo service ssh restart```

Uruchomienie usługi przy starcie systemu:
```sudo systemctl enable ssh```
Dodanie wykluczenia dla ufw w Ubuntu:
```sudo ufw allow 22```


##### Ważne pliki 
Klient:
```~/.ssh/authorized_keys```,

```~/.ssh/config```,

```~/.ssh/known_hosts```,

Server:
```/etc/ssh/ssh_config```, 

```/etc/ssh/sshd_config```

##### Plik config po stronie klienta 
Tworzymy plik: ```~/.ssh/config```

Dodajemy nasze parametry: 
```
Host test
  HostName 123.123.123.123
  Port xxxx
  IdentityFile ~/.ssh/s_kluczPodGita
  User mojuser 
```
Możemy się logować z poziomu terminala:
``` ssh test ```

### Podstawowe utwardzenie serwera SSH

Wszystkich modyfikacji w tym przypadku dokonujemy w pliku ```/etc/ssh/sshd_config```

Zamieniamy domyślnego portu ssh 
```Port 22``` na ```Port xx```

Dodanie HostKey:
```
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
```

Dodanie dopuszczone metody szyfrowania: 
```
# Ciphers and keying
KexAlgorithms curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha256
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr
MACs hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,umac-128-etm@openssh.com
HostKeyAlgorithms ssh-ed25519,ssh-ed25519-cert-v01@openssh.com,sk-ssh-ed25519@openssh.com,sk-ssh-ed25519-cert-v01@openssh.com,rsa-sha2-256,rsa-sha2-512,rsa-sha2-256-cert-v01@openssh.com,rsa-sha2-512-cert-v01@openssh.com
```

Włączenie generowania logów dla ssh:
```
#Logging
SyslogFacility AUTH
LogLevel INFO
```
Wyłączenie logowania roota:
```PermitRootLogin no```

Wyłączenie użycia hasła do logowania: 
```PasswordAuthentication no```

Wyłączenie komunikacji x11 jeśli nie jest pogrzebana 
```X11Forwarding no```

Włączenie użycia PAM 
```UsePAM yes```

Włączenie automatycznego wylogowywani usera po określonym czasie 
```ClientAliveInterval 300```

### Test / Audyt serwera SSH

Testy serwera ssh to temat drażliwy, ja dla własnego sprawdzenia wykorzystywałem ten skrypt:
https://github.com/jtesta/ssh-audit

### Przydatne linki, cześć z nich pokrywa się z tematem ale rozwija elementy o których nie wspominam: 
https://www.ssh.com/academy/ssh/keygen
https://morfikov.github.io/post/uwierzytelniajace-klucze-ssh/
https://pomoc.home.pl/baza-wiedzy/logowanie-ssh-gdzie-znalezc-klucze-rsa-do-polaczenia
https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys
https://chrisjean.com/ssh-tutorial-for-ubuntu-linux/