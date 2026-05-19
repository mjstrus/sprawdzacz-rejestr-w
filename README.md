# Sprawdzacz Rejestrów VAT — Enova 365 vs JPK_FA

Narzędzie do weryfikacji rejestrów VAT (naliczony/należny) z Enova 365 względem plików JPK_FA (XML) z Saldeo Smart.

## Cechy

- ✅ **Wybór typu rejestru** — suwak ZAKUP/SPRZEDAŻ (zmieniają się reguły matchingu)
- ✅ **Parser Enova XLSX** — ekstraktuje rejestr VAT z plików eksportowanych z Enova 365
- ✅ **Parser JPK_FA XML** — czyta faktury z eksportu "Dokumenty" w Saldeo Smart
- ✅ **Composite key matching** — porównanie po (nr_dokument + NIP sprzedawcy)
- ✅ **Porównanie pozycji** — netto, VAT, brutto, stawki, numery dokumentów
- ✅ **Obsługa stawek VAT** — 23%, 8%, 5%, oraz **zw (zwolnione)**
- ✅ **Wyświetlanie stawek** — kolumna "Stawki VAT" w tabeli wyników
- ✅ **Weryfikacja Białej Listy MF** — sprawdzenie statusu VAT kontrahentów
- ✅ **Matching adresów** — rozmyte porównanie adresu z faktury z białą listą
- ✅ **Export CSV** — wszystkie wyniki z stawkami VAT

## Użycie

1. Otwórz `index.html` w przeglądarce (lub wdróż na GitHub Pages)
2. **Wybierz typ rejestru** — ZAKUP (naliczony) lub SPRZEDAŻ (należny)
3. Wgraj plik XLSX z rejestrem VAT z Enova
4. Wgraj jeden lub więcej plików XML (JPK_FA) z Saldeo Smart
5. Kliknij "Analizuj i porównaj"
6. Przejrzyj wyniki — kolumna "Stawki VAT" pokazuje rzeczywiste stawki z faktury
7. (Opcjonalnie) Kliknij "Sprawdź na Białej Liście" aby zweryfikować status kontrahentów

## Typy rejestrów

### ZAKUP (naliczony)
- **Kontrahent** = Dostawca (sprzedawca faktury)
- **Matching**: `nr_dokument + NIP dostawcy` z Enova = `nr_faktury + NIP sprzedawcy (P_4B)` z KSeF
- **Białe Lista**: weryfikuję NIP dostawcy

### SPRZEDAŻ (należny)
- **Kontrahent** = Odbiorca (kupujący faktury)
- **Matching**: `nr_dokument + Twój NIP` z Enova = `nr_faktury + NIP sprzedawcy (P_4B)` z KSeF
  - Twój NIP zawsze będzie sprzedawcą w JPK_FA
  - NIP odbiorcy czytam z `P_3A` dla weryfikacji
- **Białe Lista**: weryfikuję NIP odbiorcy (z P_3A)

## Obsługa stawki ZW

Faktury mogą mieć **stawki mieszane** (np. 23% + zw na jednej fakturze). Parser teraz:
- Czyta `P_13_4` (netto zwolnione) i `P_14_4` (VAT = 0)
- Wyświetla pełną listę stawek: np. "23%, zw" jeśli faktura ma obie
- Eksportuje stawki do CSV

**Przykład**: Faktura ŻABKA 1733733400 zawiera:
- 3 pozycje × 23% = 142,52 zł netto → 32,78 zł VAT
- 2 pozycje × zw = 11,07 zł netto → 0 zł VAT
- **Razem**: 153,59 zł netto, 32,78 zł VAT, 186,37 zł brutto
- **Wyświetlane stawki**: "23%, zw" ← przejrzyste dla audytora

## Formatty

### Input
- **XLSX**: Rejestr VAT naliczony/należny z Enova 365 (standardowy export)
- **XML**: JPK_FA z Saldeo Smart — eksport z zakładki "Dokumenty" (nie "Faktury"!)

### Output
- **Tabela HTML** — interaktywna, z filtrami i wyszukiwaniem
- **CSV** — do Excela/Libreoffice z pełnymi szczegółami
- **Białe Lista** — tabelka ze statusami VAT i rachunkami bankowymi
