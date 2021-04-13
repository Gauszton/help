### Notatki do SSH / RSA na platformie linux/unix

Stawiając serwer czy konfigurując nowy komputer, za każdym razem muszę wyszukać i skonfigurować klucze RSA, usługę serwera SSH oraz odpowiednio je zabezpieczyć. Tym razem postanowiłem spisać kroki jakie wykonuje, tak by powstała mała instrukcja. 
Jest to moja konfiguracja, dotyczy VPS czy VM w mojej sieci, nie musi być odpowiednia dla Ciebie.</br>
 
Jeśli zauważysz błąd, chcesz coś dodać, śmiało napisz. Będę poprawiał / dodawał co jakiś czas nowości do instrukcji tak by była aktualna. </br>

#### Rodzaje kluczy SSH

##### Polecane:
```RSA``` - najpopularniejszy i najczęściej wykorzystywan.  Zalecana minimalna wersja klucza to 2048 bit (możliwe, że nie będzie niedługo wspierana). Zalecana wybrać wersje 4096 bit.</br>
```EdDSA``` - poprawiona wersja ECDSA, implementuje Curve25519 w EdDSA można użyć w Ed25519.</br>

##### Nie polecane:
```DSA``` - nie jest dostępny domyślnie w OpenSSH 7.0 ze względu na niewystarczający poziom skomplikowania, </br>
```ECDSA``` - implementacja DSA w formie krzywej eliptycznej, ze względu na poziom skomplikowania nie jest zalecany tak samo jak DSA, </br>

##### Generowanie klucza RSA

**```ssh-keygen -t rsa -b 4096 -o -f ~/.ssh/s_kluczPodGita -C "$(whoami)@$(hostname)-$(date -I)"```**</br>

  ```-t dsa | ecdsa | ed25519 | rsa``` = rodzaj algorytmu, zaleca się wybranie RSA,</br>
  ```-b``` = wskazuje moc szyfrowania w bitach, dla RSA zalecana się 4096 bit,</br>
  ```-a``` = parametr pozwalający na zidentyfikowanie ilość testów jakim zostanie poddany klucz, domyślnie 100, można pomijać lub ustawić więcej niż 100, </br>
  ```-f ~/.ssh/s_kluczPodGita``` = ścieżka oraz nazwa pliku wynikowego z kluczami, bez rozszerzenia utworzony zostanie klucz prywatny, z rozszerzeniem .pub klucz publiczny, </br>
  ```-C "$(whoami)@$(hostname)-$(date -I)"``` = komentarz, może być dowolny, w wym przypadki dodany na końcu klucza zostanie nazwa username@hostname-data</br>
  ```-o``` = generowanie klucza w nowym formacie OpenSSH</br>

  ---
  Dodatkowo:

 ```-m PEM``` = sformatuj klucz w formie PEM</br>
 ```-N mypassphrase``` = Passphrase, możemy dodać tutaj lub w trakcie generowania pary kluczy,</br>

##### Generowanie klucza publicznego na podstawie prywatnego

```ssh-keygen -y -t rsa -f ~/.ssh/s_kluczPodGita > ~/.ssh/s_kluczPodGita.pub```</br>

##### Sprawdzanie odcisku klucza

```ssh-keygen -l -f ~/.ssh/s_kluczPodGita```</br>

##### Dodanie Passphrase do klucza prywatnego

```ssh-keygen -p -f ~/.ssh/s_kluczPodGita```</br>

##### Usuniecie Passphrase z klucza prywatnego

```ssh-keygen -p -f ~/.ssh/s_kluczPodGita```</br>

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