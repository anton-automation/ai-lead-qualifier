# AI Lead Qualifier

Telegram-бот для автоматической квалификации входящих лидов с помощью GPT-4o-mini.

## Что делает

Принимает заявку в Telegram.
AI анализирует текст → классифицирует лид
отправляет менеджеру уведомление с приоритетом.
Google Sheets логирование

## Категории лидов

| Категория | Приоритет | Описание |
|-----------|-----------|----------|
| 🔥 Горячий | 1 | Явный интерес + бюджет + срочность |
| 🌡 Тёплый | 2 | Интерес есть, нет срочности или бюджета |
| ❄️ Холодный | 3 | Общий интерес, нет конкретики |
| 🚫 Нецелевой | 4 | Спам, реклама, не по теме |

## Архитектура
Telegram Trigger → OpenAI → Parse_lead → Sheet_logs → Route_by_category
                                  ↓ (error)
                            Telegram_error

Route_by_category → Telegram_hot/warm/cold/spam → Merge → Telegram_confirm

## Стек

- n8n (Docker, localhost)
- OpenAI GPT-4o-mini
- Telegram Bot API
- Google Sheets (Service Account)
- 
## Как запустить

1. Импортируй `ai_lead_qualifier_v3.json` в n8n
2. Добавь credentials:
   - OpenAI API key
   - Telegram Bot token
3. Активируй workflow

## Демо

![demo](screenshots/ai_lead_qualifier.png)

## Бизнес-ценность

Менеджер получает только приоритизированные уведомления.
Горячие лиды обрабатываются немедленно — холодные не теряются.

Подходит для: отделов продаж, агентств, фрилансеров с входящими заявками.

## Версии

### v1 — базовая классификация
- Classification prompt (4 категории: hot/warm/cold/spam)
- Switch node — роутинг по категории
- 4 Telegram уведомления менеджеру

### v2 — логирование
- Google Sheets: timestamp, name, contact, category, priority, reason
- Telegram подтверждение пользователю

### v3 — error handling
- Parse_lead: On Error → Continue (using error output)
- Telegram_error — уведомление при сбое AI парсинга
- Workflow не падает при невалидном JSON от OpenAI
