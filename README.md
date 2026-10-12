# AI Master Pro — Autonomiczny system wielu agentów AI na Androida

**Wersja:** 3.0.0 (zob. CHANGELOG.md)  
**Język interfejsu:** Polski  
**Technologia:** Kotlin, Jetpack Compose, Room, Coroutines, OkHttp

---

## Co to jest?

AI Master Pro to aplikacja Android z prawdziwą orkiestracją wielu agentów AI:

| Agent | Rola |
|-------|------|
| **Master** | Koordynator — analizuje polecenie, tworzy plan, scala wyniki |
| **Planista** | Dzieli zadanie na etapy z zależnościami |
| **Badacz** | Szuka informacji, wskazuje niepewności |
| **Programista** | Tworzy kod i rozwiązania techniczne |
| **Audytor** | Krytycznie ocenia wyniki innych agentów |
| **Tester** | Projektuje testy i weryfikuje rezultaty |
| **Specjalista plików** | Analizuje i tworzy dokumenty |
| **Podsumowujący** | Przygotowuje ostateczną odpowiedź |

Agenci współpracują przez wspólny pipeline zadań (nie są niezależnymi chatbotami).

---

## Wymagania

- Android Studio Ladybug (2024.2) lub nowszy
- JDK 17
- Android SDK 35 (compileSdk), minSdk 26
- Telefon/emulator Android 8.0+

---

## Jak uruchomić

1. Rozpakuj archiwum `AI_MASTER_PRO_ANDROID.zip`
2. Otwórz folder w **Android Studio** (File → Open)
3. Skopiuj `local.properties.example` → `local.properties` i ustaw `sdk.dir`
4. Poczekaj na synchronizację Gradle
5. Uruchom na emulatorze lub telefonie (Run ▶)

```bash
# Alternatywnie z linii poleceń:
./gradlew assembleDebug
# APK: app/build/outputs/apk/debug/app-debug.apk
```

---

## Konfiguracja modeli AI (bez abonamentu)

Aplikacja **nie wymaga** płatnej subskrypcji. Używasz własnych kluczy:

### Opcja 1 — Groq (zalecana, darmowy limit)
1. Załóż konto: https://console.groq.com
2. Utwórz API Key
3. W aplikacji → **Modele AI** → edytuj „Groq” lub dodaj nowy:
   - Base URL: `https://api.groq.com/openai/v1`
   - Model: `llama-3.3-70b-versatile` (lub inny z listy Groq)
   - Wklej klucz API
   - Ustaw jako domyślny

### Opcja 2 — OpenRouter (modele darmowe)
- Base URL: `https://openrouter.ai/api/v1`
- Model np.: `meta-llama/llama-3.1-8b-instruct:free`
- Klucz z https://openrouter.ai

### Opcja 3 — Własny endpoint (Ollama, LM Studio, vLLM…)
- Base URL: np. `http://192.168.1.10:11434/v1`
- Model: nazwa lokalnego modelu
- Klucz: opcjonalny

Bez skonfigurowanego modelu aplikacja działa w trybie lokalnym (stub) i informuje o braku modelu.

---

## Funkcje działające w v1.0

- ✅ Czysty projekt Android Studio (Kotlin + Compose)
- ✅ 8 ról agentów z prawdziwą orkiestracją (Master → plan → agenci → audyt → podsumowanie)
- ✅ Zapis zadań i wyników w Room (przetrwa restart)
- ✅ Wznawianie / anulowanie zadań
- ✅ Pamięć długoterminowa (CRUD)
- ✅ Historia czatu
- ✅ Konfiguracja wielu dostawców AI (OpenAI-compatible)
- ✅ Limity iteracji, timeouty, obsługa błędów sieci
- ✅ Ciemny motyw z pomarańczowymi akcentami
- ✅ Interfejs w 100% po polsku
- ✅ Brak obowiązkowego abonamentu

## Funkcje wymagające dodatkowej konfiguracji / przyszłych wersji

- ⚙️ Wyszukiwanie internetowe (brak wbudowanego darmowego API — agenci informują o braku dostępu)
- ⚙️ Pełna integracja z plikami telefonu (SAF) — obecnie treść można wkleić w czacie
- ⚙️ Szyfrowanie kluczy API (obecnie przechowywane lokalnie w Room)
- ⚙️ WorkManager do kontynuacji w tle po zabiciu procesu
- ⚙️ Lokalne modele on-device (wymaga większych zasobów)

---

## Architektura

```
ui/          — Compose screens + ViewModel
domain/
  agent/     — Orchestrator, AgentPrompts
  provider/  — LlmProvider (OpenAI-compatible)
data/
  local/     — Room (Task, AgentResult, Chat, Memory, Providers)
  repository/
  model/
```

Pipeline:
1. Użytkownik → Master analizuje
2. Master tworzy plan JSON
3. Kolejni agenci wykonują kroki (z kontekstem poprzednich)
4. Audytor ocenia
5. Podsumowujący składa odpowiedź
6. Wszystko zapisane w Room

---

## Bezpieczeństwo

- Destrukcyjne operacje wymagają zgody (ustawienie)
- Brak sekretów w kodzie źródłowym
- Dane tylko lokalnie
- Brak automatycznego wysyłania danych na zewnątrz poza wywołaniami LLM

---

## Licencja

Projekt edukacyjny / własny użytek. Użytkownik ponosi odpowiedzialność za klucze API i koszty providerów.
