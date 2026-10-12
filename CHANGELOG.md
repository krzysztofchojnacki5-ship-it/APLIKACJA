# AI Master Pro — v3.0.0

## Naprawa błędów „zły model / model nie istnieje”
- Provider wykrywa odpowiedź „model nie istnieje / wycofany”, pobiera listę modeli od dostawcy,
  wybiera najbliższy istniejący, ponawia zapytanie i zapisuje poprawną nazwę (`ModelRecovery.kt`).
- Gdy nic nie pasuje, komunikat podaje dostępne modele i wskazuje, co zrobić.
- Modele → „Napraw nazwy modeli” sprawdza wszystkie aktywne modele naraz; „Lista” w edycji modelu
  też podmienia nieistniejącą nazwę.
- Nowe, stabilne domyślne nazwy (np. `gemini-flash-latest`, `openrouter/auto`).
- Nazwy modeli nie trafiają już do promptu MASTER.

## Wygląd jak ChatGPT
- Ciemna paleta ChatGPT (#212121 / #171717 / #2F2F2F), zielony akcent.
- Czat na pełnym ekranie, panel boczny: wyszukiwarka, Nowy czat, sekcje (Modele, Agenci, Zadania,
  Pamięć, Ustawienia) i historia czatów z menu „…” (usuń).
- Odpowiedzi bez bąbelka, bąbelek tylko dla użytkownika; pulsująca kropka podczas myślenia;
  pasek akcji pod odpowiedzią: kopiuj, udostępnij, wygeneruj ponownie.
- Pole wpisywania w kształcie pigułki z białym przyciskiem wyślij / stop.
- Markdown: nagłówki, listy, tabele, kod z przyciskiem Kopiuj.

## Zespół agentów (bez zmian z v2)
- MASTER odpowiada; agenci pracują w tle, ich rozmowę otwiera „Rozmowa agentów” pod odpowiedzią.
- Przypisanie modeli do ról: Agenci / Modele; „Auto-przydziel role”.
