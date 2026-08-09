# Roadmapsa — Maslo3D 0.1-ALPHA

> **Status:** aktywna wersja rozwojowa (ALPHA). Strona: https://wegiel360.github.io/maslo3d/
> Cel: potwierdzona, prostolinijna aplikacja zamówień wydruków 3D dla społeczności szkolnej.

---

## 1. Czym jest Maslo3D?

Single Page App (czysty `index.html`, zero builda) do składania i obsługi zamówień
wydruków 3D. Działa na **Firebase Realtime Database** (tryb publiczny, bez logowania),
wygląd w **Tailwind CSS (Dark Mode)** z delikatnym **glassmorphismem**, pełen responsywny
i gotowy na urządzenia mobilne i komputery. Dostępny jako **GitHub Pages** pod adresem
`https://wegiel360.github.io/maslo3d/`.

### Tryby dostępu
| Tryb | Adres |
|------|-------|
| Witryna sklepowa | `…/maslo3d/` |
| Śledzenie zamówienia | `…/maslo3d/?o=294` (3-cyfrowy numer) lub `?orderId=UUID` |
| Panel administratora | `…/maslo3d/?admin=true` (hasło: w hashu w bazie) |

---

## 2. Co już działa (0.1-ALPHA)

### Witryna sklepowa (uczniowie)
- Lista ofert na żywo z Firebase; karty: zdjęcie, nazwa, opis, waga, cena (ze wzoru `waga × cena za gram`), stan.
- **Wycena za gram** — administrator ustawia `cena za gram` w zł/gr (domyślnie 0).
- **Koszyk** — dodawanie produktów, zmiana ilości, łączna waga i cena; **max 6 różnych ofert** na zamówienie.
- **Zestawy (bundles)** — pozycja z `bundle.items` rozwija się automatycznie w koszyk.
- **Gwizdek 3D** — przycisk odtwarza dźwięk (Web Audio, sinusoida o frekwencji `whistleFreq`) dla wybranego produktu; ładunek lazy (brak WP w startcie).
- **Dostawa**: tylko **Szkoła** — odbiór; **płatność**: brak płatności online, rozliczenie przy odbiorze.
- **Tryb zbierania kolejki** (`mode = queue`, wakacje): brak odejmowania stocku, zamówienia czekają. Po przełączeniu na `active`, stock jest odejmowany i kolejka realizowana na bieżąco.
- **3-cyfrowe numery zamówień** (`001`–`999`), unikalne wśród aktywnych; anulowane/usunięte zwalniają numer.
- Baner trybu + **przycisk "Udostępnij (QR)"** oraz **przycisk "Śledź paczkę"** (wpisz numer → `?o=KOD`).
- **Karta podsumowania** po zamówieniu: numer, link do śledzenia, kod QR.
- **Antyspam**: max 1 zamówienie na 2 minuty; imię + klasa obowiązkowe; notatka, że dane są tylko do dostawy.

### Śledzenie zamówienia
- Stepper: **W kolejce -> Drukuje się -> W drodze -> Doręczone**.
- Wyświetla: imię, klasę, pozycje (produkt, waga, ilość), wagę łączną, płatność, status, baner anulowania z powodem.
- Po anulowaniu: komunikat i powód od administratora.

### Panel administratora (ERP)
- **Statystyki na żywo**: wszystkie zamówienia, aktywne wydruki, w kolejce, wydrukowane gramy, szacowany zarobek + **wykres** zamówień i wagi z ostatnich 7 dni.
- **Ustawienia**: cena za gram, tryb (`queue`/`active`), baner, hasło (hash w bazie).
- **Produkty**: dodawanie/edycja (nazwa, opis, waga, cena za gram, **czas druku [s] ręczny / wymagany**, stan, zdjęcie z kompresją Canvas→WebP/JPEG Base64, gwizdek + częstotliwość, zestaw + pozycje).
- **Tabela zamówień**: ID, imię/klasa, produkty, waga/cena, data, status (dropdown na żywo), link (kopiuj), anuluj+usuń (modal z powodem → archiwum).
- **Kolejka druku**: lista pozycji posortowana rosnąco po szacowanym czasie — **long polling** (Firebase `on('value')`), nie spamowane odświeżanie.

### Infrastruktura
- Firebase RTDB `maslo-3d-default-rtdb` (europe-west1), publiczne reguły.
- `firebase.json` + `database.rules.json` w repo.
- Optymalizacje: skompresowane obrazy (`bg.jpg`, `logo.png`), lazy-load QR, `preconnect`.
- Hasło panelu: `ZAQ!2wsxz` → hash `8738d8af…` w `settings/adminPasswordHash` (hasło **nigdy** w kodzie ani README).

## 4. Technologia i uruchomienie lokalnie

- **Jedyny wymóg**: nowoczesna przeglądarka (Web Crypto, Web Audio). Czyli Chrome/Firefox/Edge najnowsze.
- **Brak builda**: otwórz `index.html` w przeglądarce (dla dev można odpalić prosty serwer, bo Web Crypto wymaga `https`/`localhost`):
  ```bash
  npx serve .     # albo python3 -m http.server 8000
  ```
- **Firebase** jest wykonane (RTDB + reguły + domyślne `settings`). W razie potrzeby:
  ```bash
  firebase init database   # (interaktywne, razem z utworzeniem DB)
  firebase deploy --only database
  ```
- **Struktura repozytorium**:
  ```
  maslo3d/
  ├── index.html             # cała aplikacja
  ├── logo.png               # logo (skompresowane)
  ├── favicon.ico
  ├── bg.jpg                 # tło (skompresowane)
  ├── firebase.json          # konfig CLI
  ├── database.rules.json    # publiczne reguły RTDB
  ├── README.md
  └── ROADMAP-0.1-ALPHA.md   # ta roadmapa
  ```

---

## 5. Bezpieczeństwo i prywatność

- Dane osobowe (imię, klasa) są **tylko do dostawy** — nie są wykorzystywane do konta ani marketingu.
- Brak nazwiska — celowo.
- Hasło panelu admina przechowywane tylko jako hash SHA-256 w bazie; **nie ma w kodzie ani repo**.
- Baza działa publicznie (bez auth) — celowy design na potrzeby szkoły; `isDeleted` chroni przed spamem trollów, a antyspam limituje częstotliwość zamówień.

---

*Wersja 0.1-ALPHA — "Masło" rośnie, kolejka do września.*
