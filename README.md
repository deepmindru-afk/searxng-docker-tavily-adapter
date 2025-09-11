# SearXNG Docker Tavily Adapter

**Бесплатная замена Tavily API на базе SearXNG** 🔍

Используйте SearXNG с точно таким же API как у Tavily - без ограничений, без API ключей, полная приватность!

> 🎯 **Готовый Docker Compose стек** с SearXNG + Tavily-совместимым API адаптером

## 🚀 Быстрый старт

```bash
# 1. Клонирование
git clone https://github.com/vakovalskii/searxng-docker-tavily-adapter.git
cd searxng-docker-tavily-adapter

# 2. Настройка конфигурации
cp config.example.yaml config.yaml
# Поменяйте secret_key в config.yaml

# 3. Запуск
docker compose up -d

# 4. Тест
curl -X POST "http://localhost:8000/search" \
     -H "Content-Type: application/json" \
     -d '{"query": "цена bitcoin", "max_results": 3}'
```

## 💡 Использование

### Drop-in замена для Tavily

```python
# Установите оригинальный Tavily клиент
pip install tavily-python

from tavily import TavilyClient

# Просто поменяйте base_url!
client = TavilyClient(
    api_key="не_важно",  # Игнорируется
    base_url="http://localhost:8000"  # Ваш адаптер
)

# Используйте как обычно
response = client.search(
    query="цена bitcoin",
    max_results=5,
    include_raw_content=True
)
```

### Простой API

```python
import requests

response = requests.post("http://localhost:8000/search", json={
    "query": "что такое машинное обучение",
    "max_results": 5,
    "include_raw_content": True
})

results = response.json()
```

## 📦 Что внутри

- **SearXNG** (порт 8999) - мощный мета-поисковик
- **Tavily Adapter** (порт 8000) - HTTP API совместимый с Tavily
- **Redis** - кэширование для SearXNG
- **Единый конфиг** - `config.yaml` для всех сервисов

## 🎯 Преимущества

| Tavily (оригинал) | SearXNG Adapter |
|-------------------|-----------------|
| 💰 Платный | ✅ Бесплатный |
| 🔑 Нужен API ключ | ✅ Без ключей |
| 📊 Лимиты запросов | ✅ Без лимитов |
| 🏢 Внешний сервис | ✅ Локальное развертывание |
| ❓ Неизвестные источники | ✅ Контролируете движки |

## 📋 API

### Запрос
```json
{
  "query": "поисковый запрос",
  "max_results": 10,
  "include_raw_content": false
}
```

### Ответ
```json
{
  "query": "поисковый запрос",
  "results": [
    {
      "url": "https://example.com",
      "title": "Заголовок",
      "content": "Краткое описание...",
      "score": 0.9,
      "raw_content": "Полный текст страницы..."
    }
  ],
  "response_time": 1.23,
  "request_id": "uuid"
}
```

## ⚙️ Настройка

Подробная инструкция: [CONFIG_SETUP.md](CONFIG_SETUP.md)

## 🏗️ Архитектура

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Ваш код       │───▶│  Tavily Adapter  │───▶│     SearXNG     │
│                 │    │   (порт 8000)    │    │   (порт 8999)   │
│ requests.post() │    │                  │    │                 │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                ▼                        ▼
                       ┌──────────────────┐    ┌─────────────────┐
                       │  Web Scraping    │    │ Google, Bing,   │
                       │  (raw_content)   │    │ DuckDuckGo...   │
                       └──────────────────┘    └─────────────────┘
```

## 🔧 Разработка

```bash
# Локальная разработка адаптера
cd simple_tavily_adapter
pip install -r requirements.txt
python main.py

# Тестирование
python test_client.py
```

## 📜 Лицензия

MIT License - используйте как хотите! 🎉