## help - baza wiedzy Gausztona
### Zbiór wszystkich przydanych instrukcji i poleceń w jednym miejscu dostępny z terminala bash i zsh.

Stawiając serwer czy konfigurując nowy komputer, za każdym razem muszę wyszukać i skonfigurować klucze RSA, usługę serwera SSH oraz odpowiednio je zabezpieczyć. Tym razem postanowiłem spisać kroki jakie wykonuje, tak by powstała mała instrukcja. 
Jest to moja konfiguracja, dotyczy VPS czy VM w mojej sieci, nie musi być odpowiednia dla Ciebie.</br>
 
Jeśli zauważysz błąd, chcesz coś dodać, śmiało napisz. Będę poprawiał / dodawał co jakiś czas nowości do instrukcji tak by była aktualna. </br>

</br>
Działanie: </br>

``` git clone https://github.com/gauszton/help ```

Do znanej nam lokalizacji, </br>
``` ~/help```

następnie do pliku ```bashrc``` lub ```zshrc``` dodajemy funkcje, która przeszuka ten plik w poszukiwaniu konkretnej frazy, tagów.

``` w trakcie przygotowywania ```

Po znalezieniu pojawi się na ekranie polecenie lub zestaw poleceń, których szukamy np: 

```generowanie klucza ssh ```

```
ssh-keygen -t rsa -b 4096 -o -f ~/.ssh/s_kluczPodGita -C "$(whoami)@$(hostname)-$(date -I)"

-t dsa | ecdsa | ed25519 | rsa = rodzaj algorytmu, zaleca się wybranie RSA,
-b = wskazuje moc szyfrowania w bitach, dla RSA zalecana się 4096 bit,
-a = parametr pozwalający na zidentyfikowanie ilość testów jakim zostanie poddany klucz, domyślnie 100, można pomijać lub ustawić więcej niż 100,
-f ~/.ssh/s_kluczPodGita = ścieżka oraz nazwa pliku wynikowego z kluczami, bez rozszerzenia utworzony zostanie klucz prywatny, z rozszerzeniem .pub klucz publiczny,
-C "$(whoami)@$(hostname)-$(date -I)" = komentarz, może być dowolny, w wym przypadki dodany na końcu klucza zostanie nazwa username@hostname-data,
-o = generowanie klucza w nowym formacie OpenSSH,
-m PEM = sformatuj klucz w formie PEM,
-N mypassphrase = Passphrase, możemy dodać tutaj lub w trakcie generowania pary kluczy,
```

Wyszukiwanie będzie odbywało się po tagach, słowach kluczowych, formuła w trakcie przygotowywania,

Plan: 
1. Zebranie wszystkich ważnych poleceń i zagadnień do których często wracam,
2. Przygotowanie plików .md na każde z tych zagadnień,
3. Przetłumaczenie wszystkiego na język Angielski, 
4. Przygotowanie funkcji przeszukującej do bashrc i zshrc,
5. Wypuszczenie wersji 1.0