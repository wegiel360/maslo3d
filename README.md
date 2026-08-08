# Maslo3D

System zamówień wydruków 3D dla społeczności szkolnej. Responsywna aplikacja typu
Single Page App (SPA) działająca w języku polskim, z ciemnym, technologicznym
interfejsem (Tailwind CSS) i danymi przechowywanymi w **Firebase Realtime Database**.

Aplikacja jest w 100% gotowa do wdrożenia na **GitHub Pages** (jeden plik `index.html`).

## Dostęp do aplikacji

Aplikacja ma trzy tryby dostępu, przełączane parametrami w adresie URL:

| Tryb | Adres |
|------|-------|
| **Witryna sklepowa** (dla uczniów) | `https://wegiel360.github.io/maslo3d/` |
| **Śledzenie zamówienia** | `https://wegiel360.github.io/maslo3d/?orderId=UUID` |
| **Panel administratora (ERP)** | `https://wegiel360.github.io/maslo3d/?admin=true` |

Panel administratora nie wymaga hasła — działa w trybie publicznym bazy.
Chroń dostęp do niego (np. nie upubliczniaj linku), ponieważ statusy są weryfikowane
wyłącznie przez unikalne identyfikatory UUID zamówień.

---

## Funkcje

**Witryna sklepowa (uczniowie, bez haseł):**
- Nagłówek z logo i nazwą **Maslo3D**.
- **Wycena za gram**: administrator ustawia cenę za gram (np. `0,20 zł/gr`), a cena
  każdego produktu wyliczana jest z jego wagi (`waga x cena za gram`).
- Dynamiczna lista ofert pobierana w czasie rzeczywistym z Firebase.
- Karty produktów: zdjęcie, nazwa, opis, waga (gramy), cena za gram, aktualny stan.
- **Koszyk**: dodawanie produktów, zmiana ilości, łączna waga i cena.
- **Miejsce dostawy**: wyłącznie opcja **Szkoła** (odbiór).
- **Brak płatności online** - rozliczenie przy odbiorze w szkole.
- **Tryb zbierania kolejki**: w trakcie wakacji (do 1 września) zamówienia trafiają
  do kolejki oczekujących, a baner informuje o realizacji po rozpoczęciu roku szkolnego.
- **Udostępnianie strony kodem QR** - przycisk "QR" w nagłówku generuje kod QR adresu sklepu.
- Generowanie unikalnego identyfikatora UUID dla każdego zamówienia.
- Karta podsumowania z linkiem do śledzenia (`?orderId=UUID`) i kodem QR.
- Ekran śledzenia ze stepperem: [W kolejce] -> [Drukuje się] -> [W drodze] -> [Doręczone].
- Informacja o anulowaniu zamówienia wraz z podaniem powodu przez administratora.

**Panel administratora (ERP):**
- Statystyki na żywo: wszystkie zamówienia, aktywne wydruki, liczba osób w kolejce,
  wydrukowane gramy, szacowany zarobek.
- **Ustawienia sklepu**: cena za gram, tryb (kolejka / aktywny), baner informacyjny.
- Dodawanie i edycja produktów (nazwa, opis, **waga w gramach**, stan, zdjęcie)
  z **lokalną kompresją zdjęć** (HTML5 Canvas, max 600 px, jakość 0.6, WebP/JPEG,
  zapis jako Base64 w bazie).
- Edycja stanu magazynowego i usuwanie produktów.
- Tabela zamówień (ID, imię/klasa, produkty, waga/cena, data, status, link, akcje).
- Zmiana statusu w locie (dropdown) z natychmiastową aktualizacją Firebase.
- Anulowanie i usuwanie zamówień z wyborem powodu (modal) i archiwum.
- Przycisk "Kopiuj Link Śledzenia" do wysyłki uczniowi (np. przez Discord lub Messenger).

---

## Konfiguracja Firebase

Firebase jest już skonfigurowane dla projektu **maslo-3d**:
- aplikacja webowa `maslo3d-web` (prawdziwy `apiKey`, `appId`, `databaseURL` w `index.html`),
- **Realtime Database** `maslo-3d-default-rtdb` (region `europe-west1`),
- reguły publiczne (odczyt/zapis bez logowania) wdrożone z `database.rules.json`.

Węzły bazy można edytować w [konsoli Firebase](https://console.firebase.google.com/u/0/project/maslo-3d/database)
lub w panelu administratora aplikacji (`?admin=true`).

> Uwaga: baza działa publicznie (zgodnie z założeniem projektu - bez logowania).
> W produkcji warto ograniczyć zapis lub dodać uwierzytelnianie administratora.

---

## Struktura bazy danych

Dane zapisywane są w czterech węzłach głównych:

```
maslo-3d-default-rtdb/
├── settings/
│   ├── pricePerGram   // cena za gram w zł (np. 0.20)
│   ├── mode           // "queue" (tryb kolejki) | "active"
│   └── banner         // komunikat na stronie (np. o wakacjach)
├── products/
│   └── {productId}/
│       ├── name       // nazwa produktu
│       ├── desc       // opis
│       ├── weight     // waga w gramach (podstawa wyceny)
│       ├── stock      // stan magazynowy (szt.)
│       ├── image      // zdjęcie Base64 (skompresowane WebP/JPEG)
│       └── createdAt  // znacznik czasu utworzenia
└── orders/
    └── {orderId}/
        ├── name          // imię ucznia
        ├── className     // klasa (opcjonalnie)
        ├── delivery      // miejsce dostawy ("Szkoła")
        ├── payment       // "Brak (rozliczenie przy odbiorze w szkole)"
        ├── items[]       // [{productId, productName, weight, qty, pricePerGram}]
        ├── totalWeight   // łączna waga (gramy)
        ├── totalPrice    // łączna cena (zł)
        ├── status        // "W kolejce" | "Drukuje się" | "W drodze" | "Doręczone"
        ├── queuedBacklog // flaga "BRAK W MAGAZYNIE" (kolejka oczekujących)
        ├── createdAt     // znacznik czasu złożenia
        ├── isDeleted     // true = zamówienie anulowane/usunięte
        └── deleteReason  // powód anulowania
```

---

## Wdrożenie na GitHub Pages

1. Wypchnij repozytorium na GitHub (`wegiel360/maslo3d`).
2. W GitHub: **Settings > Pages**.
3. W sekcji **Source** wybierz **Deploy from a branch**, gałąź `main`, katalog **/ (root)**.
4. Zatwierdź. Strona będzie dostępna pod adresem `https://wegiel360.github.io/maslo3d/`.

Aplikacja to pojedynczy plik `index.html` w katalogu głównym, więc nie wymaga budowania.

---

## Struktura plików

```
maslo3d/
├── index.html            # cała aplikacja (HTML + Tailwind + Firebase + JS)
├── logo.png              # logo używane w interfejsie
├── favicon.ico           # ikona strony
├── firebase.json         # konfiguracja Firebase CLI (database)
└── database.rules.json   # reguły bezpieczeństwa Realtime Database (publiczne)
```

## Licencja

Projekt prywatny. Wszelkie prawa zastrzeżone.
