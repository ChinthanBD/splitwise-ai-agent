## 📜 Software Requirements Specification (SRS)

### 1. Purpose
To build a Telegram-based expense sharing and settlement platform, similar to Splitwise, from scratch. The system will allow users to track shared expenses, manage group balances, simplify debts, and leverage AI to automate and enhance user interactions such as categorization and summarization.

---

### 2. Scope
The system includes:
- A **Telegram bot interface** for user interaction.
- A custom **backend** with user/group/expense management.
- A **local or remote database** to store user data.
- An **AI agent** that classifies, summarizes, and suggests actions based on spending patterns.

---

### 3. Functional Requirements
#### Telegram Bot Interface
- `/start`: Register a new user
- `/add`: Add an expense (split with user(s) or group)
- `/balance`: Show what others owe or are owed
- `/group`: Create/join/leave groups
- `/summary`: Monthly or category-based spending summaries
- `/remind`: Remind users to settle up

#### Core Logic
- Handle group and individual expenses
- Automatically calculate debts
- Debt simplification algorithm (minimize number of transactions)
- Log all transactions per user and group

#### AI Agent Features
- Natural language expense parsing (e.g. “Paid 1200 for dinner with Alice and Bob”)
- Auto-categorize expenses
- Summarize spending by category, date, or user
- Optional smart reminders for overdue settlements

---

### 4. Non-Functional Requirements
- Modular and extensible codebase
- Responsive Telegram UX (under 1s reply)
- Secure user isolation and data access
- AI components must fallback gracefully if API keys not configured

---

### 5. Users
- **Registered Users**: Can create groups, add expenses, view balances
- **Group Admins**: Can add/remove members
- **Bot Admin**: Oversees app-level configs (optional)

---

### 6. Assumptions
- Users are Telegram users and interact only via bot
- One Telegram user = one user in system (auth via Telegram user ID)
- Backend will be hosted or run locally (no frontend needed)

---

## 🧠 Software Design Document (SDD)

### 1. System Architecture

```
[Telegram User]
    ↕
[Telegram Bot Handler (python-telegram-bot)]
    ↕
[Command Router] → [Expense Processor] → [Debt Engine]
          ↘︎                        ↘︎
       [AI Parser]             [Summary Generator]
            ↓                         ↓
       [DB Layer (SQLite/Postgres)] → [Export / Log]
```

---

### 2. Modules

#### `bot.py`
- Entry point for the Telegram bot
- Handles incoming commands and messages

#### `handlers/`
- `add.py`: Parse and add new expenses
- `balance.py`: Fetch current balances for user/group
- `group.py`: Create, join, leave groups
- `summary.py`: Show monthly/category stats

#### `models.py`
- ORM classes for Users, Groups, Expenses, Balances

#### `ai_agent.py`
- NLP expense parser using regex or OpenAI API
- Categorization and summarization logic

#### `debt_engine.py`
- Simplifies debts using graph-based algorithm

#### `db.py`
- Database connection and query abstraction
- CRUD helpers for expenses, users, groups

#### `config.py`
- Secrets (Telegram token, OpenAI key)
- Environment variables

---

### 3. Database Design (Simplified ERD)

**Tables:**
- `users(id, telegram_id, name, created_at)`
- `groups(id, name, created_by, created_at)`
- `group_members(group_id, user_id)`
- `expenses(id, paid_by, amount, description, group_id, category, created_at)`
- `expense_splits(expense_id, user_id, owed_amount)`
- `transactions_log(id, type, from_user, to_user, amount, timestamp)`

---

### 4. AI Enhancements

- **Natural Language Parsing** (via regex + OpenAI fallback)
  - Input: "Paid 800 for groceries with Sam and Neha"
  - Output: `payer=you`, `amount=800`, `category=groceries`, `shared_with=[Sam, Neha]`

- **Auto-categorization**:
  - Use basic rules, then fine-tune with labeled data or GPT

- **Summarization**:
  - Generate weekly/monthly summaries with GPT or pre-defined templates

---

### 5. Libraries & Tools

| Purpose                | Tool                    |
|------------------------|-------------------------|
| Telegram Bot           | `python-telegram-bot`   |
| Web Framework (optional) | `FastAPI`             |
| DB ORM                 | `Postgresql`            |
| NLP / AI               | `openai`, `re`, `sklearn`|
| PDF/CSV Export         | `fpdf`, `pandas`        |
| Background jobs        | `APScheduler` (optional)|
| Env Config             | `python-dotenv`         |

---

### 6. Deployment

- Can be deployed on:
  - Local machine
  - VPS (DigitalOcean, etc.)
  - Serverless (for advanced setups)
- Bot token and OpenAI key managed via `.env`

---

## ✅ MVP Checklist

| Feature                              | Status |
|--------------------------------------|--------|
| Telegram Bot Command Handler         | ⬜      |
| User & Group Creation                | ⬜      |
| Expense Add + Split Logic            | ⬜      |
| Balance Display                      | ⬜      |
| Debt Simplification Engine           | ⬜      |
| NLP Parsing + Categorization (AI)    | ⬜      |
| Summary Command                      | ⬜      |
| Database Integration                 | ⬜      |
| Hosting (optional local/server)      | ⬜      |

---
