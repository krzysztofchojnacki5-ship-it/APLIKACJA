# AI Master Pro — Dokumentacja techniczna

## 1. Struktura projektu

```
AI_MASTER_PRO/
├── app/
│   ├── build.gradle.kts
│   ├── proguard-rules.pro
│   └── src/main/
│       ├── AndroidManifest.xml
│       ├── java/com/aimasterpro/
│       │   ├── AIMasterApp.kt          # Application + DI ręczne
│       │   ├── MainActivity.kt
│       │   ├── data/
│       │   │   ├── local/              # Room DB, DAO
│       │   │   ├── model/              # Encje i modele domenowe
│       │   │   └── repository/
│       │   ├── domain/
│       │   │   ├── agent/              # Orchestrator, prompty
│       │   │   └── provider/           # Interfejs LLM + implementacja
│       │   └── ui/
│       │       ├── theme/
│       │       ├── navigation/
│       │       ├── MainViewModel.kt
│       │       └── screens/            # 7 zakładek
│       └── res/
├── build.gradle.kts
├── settings.gradle.kts
├── gradle.properties
├── gradlew
├── gradle/wrapper/
├── README.md
└── DOKUMENTACJA.md
```

## 2. Wersje zależności

| Komponent | Wersja |
|-----------|--------|
| AGP | 8.7.2 |
| Kotlin | 2.0.21 |
| Gradle | 8.9 |
| compileSdk | 35 |
| minSdk | 26 |
| Compose BOM | 2024.10.01 |
| Room | 2.6.1 (schemat v2, eksport do app/schemas) |
| OkHttp | 4.12.0 |

## 3. Orkiestracja agentów

Klasa `Orchestrator`:
- Tworzy `TaskEntity` w Room
- Wywołuje MASTER z poleceniem użytkownika
- Parsuje plan JSON (`{"plan":[...]}`)
- Dla każdego kroku: buduje kontekst (poprzednie wyniki + pamięć), wywołuje agenta
- Zapisuje każdy `AgentResultEntity`
- Uruchamia AUDITOR i SUMMARIZER
- Aktualizuje status zadania (COMPLETED / FAILED / CANCELLED)

Limity:
- `maxIterations` (domyślnie 8, konfigurowalne)
- Anulowanie przez `cancelRequested` + Job.cancel()

## 4. Dostawcy modeli

Wspólny interfejs `LlmProvider` + `OpenAiCompatibleProvider`.
Obsługuje: Groq, OpenRouter, OpenAI, dowolny endpoint `/v1/chat/completions`.

Klucze API przechowywane w tabeli `ai_providers` (pole `apiKeyEncrypted` — bez szyfrowania; świadomie, by uniknąć utraty kluczy przy awarii Keystore).
Obsługiwane: OpenAI-compatible (`OpenAiCompatibleProvider`, ze streamingiem SSE) oraz natywne API Claude (`AnthropicProvider`).
Przypisanie ról do modeli: tabela `settings`, klucze `role_provider_<ROLA>`. MASTER = model z flagą `isDefault`.
Rozmowy agentów: tabela `agent_messages` (v2). Historia czatów: `conversations` + `chat_messages.conversationId`.

## 5. Testy ręczne (do wykonania w Android Studio)

1. Uruchom aplikację → ekran Czat
2. Bez modelu → komunikat o braku konfiguracji
3. Modele AI → dodaj Groq z kluczem → ustaw domyślny
4. Wyślij proste polecenie np. „Co to jest Kotlin?”
5. Obserwuj log postępu i odpowiedź agentów
6. Sprawdź zakładkę Zadania — status COMPLETED, możliwość podglądu
7. Dodaj pozycję w Pamięci, zrestartuj aplikację — dane powinny przetrwać
8. Anuluj zadanie w trakcie pracy — status CANCELLED

## 6. Znane ograniczenia v1.0

- Brak automatycznych testów instrumented (środowisko bez pełnego SDK)
- Brak wyszukiwarki internetowej (agenci informują o braku)
- Klucze API niezaszyfrowane
- Brak WorkManager (zadanie nie kontynuuje się po zabiciu procesu, ale stan jest zapisany i można wznowić)
- Ikony launcher to adaptive XML (Android Studio może wygenerować pełne mipmapy)


## 7. Migracje bazy
Zobacz `CHANGELOG.md`. Nigdy nie dodawaj `fallbackToDestructiveMigration()`.
