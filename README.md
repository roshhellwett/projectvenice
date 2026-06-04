![Stars](https://img.shields.io/github/stars/roshhellwett/projectvenice?style=for-the-badge)
![Forks](https://img.shields.io/github/forks/roshhellwett/projectvenice?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Telegram Bot](https://img.shields.io/badge/Telegram-Bot%20API-0088cc?style=for-the-badge&logo=telegram&logoColor=white)

# PROJECT VENICE

The Telegram delivery layer for **India Verified**. Reads verified posts from Supabase the moment they are published and broadcasts them to a Telegram forum supergroup, one topic per category. Includes an interactive bot for on-demand browsing and search.

```
RSS / scrapers → worker → supabase.posts ─┬─ frontend (Next.js)
                                          └─ venice (this package)
```

---

## Key Features

### Real-Time News Distribution
- **Instant Push**: Listens to Supabase realtime on `posts` for immediate delivery to Telegram
- **Safety-Net Poller**: 60-second fallback poller against `posts_pending_channel_delivery` view
- **Idempotent Deliveries**: Unique constraint ensures messages aren't duplicated across restarts

### Smart Rate Limiting
- **Token-Bucket Algorithm**: Respects Telegram's global limits (30 msg/s) and per-chat limits (1 msg/s)
- **Graceful Degradation**: Queues messages when limits are reached

### Rich Message Formatting
- **Polished HTML Cards**: Each post rendered with credibility score, source list, and preview
- **Interactive Buttons**: Read full, All sources, Save, Share actions
- **Topic-Based Organization**: Posts organized by category in a Telegram forum supergroup

### Interactive Bot Commands
- `/start` — Welcome & setup
- `/latest` — Browse latest verified news
- `/categories` — Filter by category
- `/search` — Search verified posts
- `/about` — Project information
- `/help` — Command reference

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Bot Framework | aiogram 3.x (async Telegram framework) |
| Backend | Python 3.11+ |
| Database | Supabase (PostgreSQL) + Realtime API |
| Validation | Pydantic 2.x |
| Logging | structlog (JSON in prod, pretty in dev) |
| Deployment | Systemd service |

---

## Project Structure

```
projectvenice/
├── src/venice/
│   ├── main.py                  # entrypoint
│   ├── config.py                # pydantic Settings
│   ├── logging_setup.py         # structured logging
│   ├── utils/                   # hostname, time_ago, html escape
│   ├── data/                    # supabase client + queries + models
│   │   ├── client.py
│   │   ├── queries.py
│   │   └── models.py
│   ├── render/                  # card rendering + formatting
│   │   ├── card.py
│   │   ├── credibility.py
│   │   ├── sources.py
│   │   └── bullets.py
│   ├── bot/                     # aiogram handlers
│   │   ├── dispatcher.py
│   │   ├── keyboards.py
│   │   ├── callbacks.py
│   │   └── commands/
│   │       ├── start.py
│   │       ├── latest.py
│   │       ├── categories.py
│   │       ├── search.py
│   │       ├── about.py
│   │       └── help.py
│   └── publisher/
│       ├── scheduler.py         # token-bucket rate limiter
│       ├── dispatch.py          # idempotent send logic
│       ├── poller.py            # 60s safety net
│       └── realtime.py          # supabase realtime listener
├── tests/
├── deploy/
│   └── systemd/
│       └── venice.service
├── Dockerfile
└── requirements.txt
```

---

## How It Works

```
Supabase Realtime Listener → Deduplication Check → Rate Limiter
  → Render Card → Send to Telegram → Log Delivery
```

1. **Realtime Listener**: Supabase notifies about new verified posts
2. **Deduplication**: Check `telegram_deliveries` table to prevent resends
3. **Rate Limiting**: Token-bucket ensures Telegram API limits are respected
4. **Card Rendering**: Generate HTML card with credibility, sources, and buttons
5. **Telegram Send**: Dispatch message to appropriate topic in forum supergroup
6. **Safety Poller**: 60-second fallback checks for any missed deliveries
7. **Logging**: Structured JSON logs for observability

---

## Configuration

Set environment variables:

```bash
TELEGRAM_TOKEN=your_bot_token
TELEGRAM_CHANNEL_ID=your_channel_id
SUPABASE_URL=your_supabase_url
SUPABASE_KEY=your_supabase_key
ENVIRONMENT=production  # or 'development'
```

---

## License

MIT — Open source and free to use.

---

## Transparency

- Full source code available on GitHub
- Real-time delivery tracking via database
- Credibility scores verified from India Verified pipeline
- All original source links preserved
- Structured logging for complete audit trail

---

© 2026 [Zenith Open Source Projects](https://zenithopensourceprojects.vercel.app/). All Rights Reserved.
Zenith is an Open Source Project Idea by @roshhellwett
