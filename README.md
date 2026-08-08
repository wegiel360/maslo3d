# Szkolny Warsztat 3D

System zamówień wydruków 3D dla społeczności szkolnej. Responsywna aplikacja typu
Single Page App (SPA) działająca w języku polskim, z ciemnym, technologicznym
interfejsem (Tailwind CSS) i danymi przechowywanymi w **Firebase Realtime Database**.

Aplikacja jest w 100% gotowa do wdrożenia na **GitHub Pages** (jeden plik `index.html`).

## Spis treści

- [Dostep do aplikacji](#dostep-do-aplikacji)
- [Funkcje](#funkcje)
- [Konfiguracja Firebase](#konfiguracja-firebase)
- [Struktura bazy danych](#struktura-bazy-danych)
- [Wdrożenie na GitHub Pages](#wdrozenie-na-github-pages)
- [Struktura plików](#struktura-plikow)

---

## Dostep do aplikacji

Aplikacja ma trzy tryby dostepu, przełączane parametrami w adresie URL:

| Tryb | Adres |
|------|-------|
| **Witryna sklepowa** (dla uczniów) | `https://wegiel360.github.io/maslo3d/` |
| **Śledzenie zamówienia** | `https://wegiel360.github.io/maslo3d/?orderId=UUID` |
| **Panel administratora (ERP)** | `https://wegiel360.github.io/maslo3d/?admin=true` |

Panel administratora nie wymaga hasła — działa w trybie publicznym bazy.
Chroń dostep do niego (np. nie upubliczniaj linku), ponieważ statusy są weryfikowane
wyłącznie przez unikalne identyfikatory UUID zamówień.

---

## Funkcje

**Witryna sklepowa (uczniowie, bez haseł):**
- Atrakcyjny nagłówek z logo i nazwa "Szkolny Warsztat 3D".
- Dynamiczna lista ofert pobierana w czasie rzeczywistym z Firebase.
- Karty produktów: zdjęcie, nazwa, opis, cena, aktualny stan magazynowy.
- Logika kolejkowania: gdy stan magazynowy to 0 lub zamawiana ilość przewyższa stan,
  pokazywany jest komunikat *"BRAK W MAGAZYNIE - zamawiając, trafiasz do kolejki oczekujących"*.
- Generowanie unikalnego identyfikatora UUID dla każdego zamówienia.
- Karta podsumowania z unikalnym linkiem do śledzenia (`?orderId=UUID`).
- Ekran śledzenia ze stepperem: [W kolejce] -> [Drukuje się] -> [W drodze] -> [Doręczone].
- Informacja o anulowaniu zamówienia wraz z podaniem powodu przez administratora.

**Panel administratora (ERP):**
- Statystyki na żywo: wszystkie zamówienia, aktywne wydruki, liczba osób w kolejce,
  wydrukowane sztuki, szacowany zarobek.
- Dodawanie i edycja produktów z **lokalną kompresją zdjęć** (HTML5 Canvas,
  max 600 px, jakość 0.6, konwersja do WebP/JPEG, zapis jako Base64 w bazie).
- Edycja stanu magazynowego i usuwanie produktów.
- Potężna tabela zamówień (ID, imię/klasa, produkt, ilość, data, status, link, akcje).
- Zmiana statusu w locie (dropdown) z natychmiastową aktualizacją Firebase.
- Anulowanie i usuwanie zamówień z wyborem powodu (modal) i przenoszeniem do archiwum.
- Przycisk "Kopiuj Link Śledzenia" do wysyłki uczniowi (np. przez Discord lub Messenger).

---

## Konfiguracja Firebase

1. Przejdź do [konsoli Firebase](https://console.firebase.google.com/u/0/project/maslo-3d/overview)
   i otwórz projekt **maslo-3d**.
2. W menu **Build > Realtime Database** utwórz bazę danych w trybie testowym.
3. W **Project settings (ikona trybika) > General > Your apps > Web app** dodaj aplikację
   webową i skopiuj obiekt konfiguracyjny.
4. Otwórz plik `index.html` i w samym nagłówku skryptu uzupełnij obiekt `firebaseConfig`:

```js
const firebaseConfig = {
  apiKey: "TU_WSTAW_API_KEY",
  authDomain: "maslo-3d.firebaseapp.com",
  databaseURL: "https://maslo-3d-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "maslo-3d",
  storageBucket: "maslo-3d.appspot.com",
  messagingSenderId: "TU_WSTAW_SENDER_ID",
  appId: "TU_WSTAW_APP_ID"
};
```

5. (Zalecane) Ustaw reguły bezpieczeństwa Realtime Database. W trybie publicznym
   możesz zezwolić na odczyt i zapis:

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

> Uwaga: powyższe reguły daja pełny publiczny dostep. W produkcji warto ograniczyć
> zapis np. do wskazanych węzłów lub wprowadzić uwierzytelnianie administratora.

---

## Struktura bazy danych

Dane zapisywane sa w dwóch węzłach głównych:

```
maslo-3d-default-rtdb/
├── products/
│   └── {productId}/
│       ├── name         // nazwa produktu
│       ├── desc         // opis
│       ├── price        // cena jednostkowa (zł)
│       ├── stock        // stan magazynowy (szt.)
│       ├── image        // zdjęcie w formacie Base64 (skompresowane WebP/JPEG)
│       └── createdAt    // znacznik czasu utworzenia
└── orders/
    └── {orderId}/
        ├── name          // imię ucznia
        ├── className     // klasa (opcjonalnie)
        ├── productId     // identyfikator produktu
        ├── productName   // nazwa produktu (kopia)
        ├── qty           // zamawiana ilość
        ├── price         // cena jednostkowa w momencie zamówienia
        ├── status        // "W kolejce" | "Drukuje się" | "W drodze" | "Doręczone"
        ├── queuedBacklog // flaga "BRAK W MAGAZYNIE" (true, gdy zamówienie w kolejce oczekujących)
        ├── createdAt     // znacznik czasu złożenia
        ├── isDeleted     // true = zamówienie anulowane/usunięte
        └── deleteReason  // powód anulowania
```

---

## Wdrożenie na GitHub Pages

1. Wypchnij repozytorium na GitHub (`wegiel360/maslo3d`).
2. W GitHub: **Settings > Pages**.
3. W sekcji **Source** wybierz **Deploy from a branch**, gałąź `main`, katalog **/ (root)**.
4. Zatwierdź. Strona bedzie dostepna pod adresem `https://wegiel360.github.io/maslo3d/`.

Aplikacja to pojedynczy plik `index.html` w katalogu głównym, więc nie wymaga budowania
ani dodatkowej konfiguracji base-href.

---

## Struktura plików

```
maslo3d/
├── index.html      # cała aplikacja (HTML + Tailwind + Firebase + JS)
├── logo.png        # logo używane w interfejsie (nagłówek, ekrany)
└── favicon.ico     # ikona strony w pasku przeglądarki
```

## Licencja

Projekt prywatny. Wszelkie prawa zastrzeżone.
