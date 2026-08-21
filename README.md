# Masło3D

System zamówień wydruków 3D dla społeczności szkolnej.

https://wegiel360.github.io/maslo3d/

## Możliwości

**Sklep:** produkty w czasie rzeczywistym, koszyk (lista, dane, dostawa, podsumowanie), animowane podglądy 360 stopień (GIF generowany raz w panelu), gwizdki (Web Audio), zestawy (bundle), czasy druku per drukarka (Makerbot Sketch / Bambus), imię + inicjał nazwiska, dostawa Szkoła + lokalizacja, antyspam (2 min), 3-cyfrowe kody zamówień, śledzenie ze stepperem statusu, porównywarka ofert.

**Panel administratora:** produkty (edycja, miniaturka i animacja 360 generowane z modelu STL, zdjęcia z kompresją, bundle, gwizdki), tabela zamówień ze zmianą statusu i przypisaniem drukarki, kolejka druku per drukarka, zbiorcza edycja stanów magazynowych, ustawienia (tryb kolejka/aktywny, baner, gwizdek), hasło (SHA-256, podwójne potwierdzenie przy pierwszym logowaniu).

## Technologia

- Jeden plik `index.html` (vanilla JS + Tailwind CDN + Firebase Firestore compat SDK)
- Three.js (ES module) do renderowania miniaturki i animacji 360 z modelu STL; gifenc do kodowania GIF
- Base122 (chunkowany) do przechowywania plików 3D, miniaturek i animacji w Firestore (subkolekcje `model/` i `preview/`)
- Hosting: GitHub Pages (`404.html` obsługuje przekierowania `/admin` i `/koszyk`)

## Development

Wszystkie zmiany w `index.html`. Reguły bazy w `firestore.rules` (wdrożenie: `firebase deploy --only firestore:rules`).
