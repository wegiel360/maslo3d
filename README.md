# Masło3D

System zamówień wydruków 3D dla szkoły. SPA (jeden plik `index.html`), Tailwind CSS dark mode, Firebase Firestore, Polish language.

**Live:** https://wegiel360.github.io/maslo3d/

## Tryby

| Tryb | URL |
|------|-----|
| Sklep | `maslo3d/` |
| Śledzenie | `maslo3d/?o=KOD` (3 cyfry) |
| Admin | `maslo3d/?admin=true` |

## Funkcje

**Sklep:** produkty w czasie rzeczywistym, koszyk 3-krokowy (lista → zamówienie → kolejka), gwizdki (Web Audio), zestawy (bundle), wycena za czas druku (h×m), imię + inicjał nazwiska, dostawa Szkoła + lokalizacja, antyspam (2 min), 3-cyfrowe kody zamówień, śledzenie ze stepperem statusu.

**Admin:** statystyki, wykres 7-dniowy, kolejka druku (wg czasu), tabela zamówień ze zmianą statusu, anulowanie z powodem, produkty (edycja, zdjęcia z kompresją, bundle, gwizdki, tags), ustawienia (cena/sek, tryb, baner, szacowany czas dostawy), hasło (SHA-256, podwójne potwierdzenie przy pierwszym logowaniu).

## Tech

- Firebase Firestore (compat SDK)
- Tailwind CSS (CDN)
- Web Crypto SHA-256 (hasło admina)
- Web Audio API (gwizdki)
- HTML5 Canvas kompresja zdjęć

## Struktura

```
maslo3d/
├── index.html          # cała aplikacja
├── firestore.rules     # reguły Firestore (public)
├── firebase.json       # config Firebase CLI
├── logo.png            # logo
├── bg.jpg              # tło
└── favicon.ico
```
