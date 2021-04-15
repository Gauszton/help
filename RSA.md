### Notatki do RSA na platformie linux/unix

#### Rodzaje kluczy RSA

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

