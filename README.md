# Masło3D

System zamówień wydruków 3D dla szkoły. Aplikacja jednoplikowa (`index.html`), Tailwind CSS, ciemny motyw, Firebase Firestore, język polski.

**Adres:** https://wegiel360.github.io/maslo3d/

## Tryby

| Tryb | Adres URL |
|------|-----------|
| Sklep | `maslo3d/` |
| Śledzenie zamówienia | `maslo3d/?o=KOD` (3-cyfrowy numer) |
| Panel administratora | `maslo3d/?admin=true` |

## Możliwości

**Sklep:** produkty w czasie rzeczywistym, koszyk trójstopniowy (lista → zamówienie → kolejka), gwizdki (Web Audio), zestawy (bundle), wycena za czas druku (godziny × minuty), imię + inicjał nazwiska, dostawa Szkoła + lokalizacja, antyspam (2 min), 3-cyfrowe kody zamówień, śledzenie ze stepperem statusu.

**Panel administratora:** statystyki, wykres tygodniowy, kolejka druku (posortowana wg czasu), tabela zamówień ze zmianą statusu, anulowanie z powodem, produkty (edycja, zdjęcia z kompresją, bundle, gwizdki, tagi), ustawienia (cena za sekundę, tryb, baner, szacowany czas dostawy), hasło (SHA-256, podwójne potwierdzenie przy pierwszym logowaniu).

## Technologie

- Firebase Firestore (SDK compat)
- Tailwind CSS (CDN)
- Web Crypto SHA-256 (hasło administratora)
- Web Audio API (dźwięk gwizdków)
- HTML5 Canvas (kompresja zdjęć)

## Struktura plików

```
maslo3d/
├── index.html          # cała aplikacja
├── assets.json         # tła SVG (Base122) dobierane do proporcji ekranu
├── firestore.rules     # reguły Firestore (publiczne)
├── firebase.json       # konfiguracja Firebase CLI
├── logo.png            # logo
└── favicon.ico
```
