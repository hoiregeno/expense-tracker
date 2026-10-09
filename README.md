# Expense Tracker

A simple personal expense tracker built with a decoupled architecture: a static HTML/CSS/JS frontend that talks to a PHP and MySQL backend through a JSON API.

> **Status:** In development. See [Roadmap](#roadmap).

## Features

- Add, edit, and delete expenses
- Organize expenses by category, each with its own color
- Filter expenses by month and category
- Monthly summary: total spent, transaction count, top category, and per-category breakdown

## Tech stack

| Layer             | Technology                              |
| ----------------- | --------------------------------------- |
| Frontend          | HTML, CSS, vanilla JavaScript (`fetch`) |
| Backend           | PHP with mysqli prepared statements     |
| Database          | MySQL / MariaDB                         |
| Local environment | XAMPP, VS Code                          |
| Version control   | Git                                     |

## Architecture

The frontend and backend are separate and communicate only over HTTP.

```
Browser (frontend/)  --- fetch, JSON --->  PHP API (backend/)  --- SQL --->  MySQL
```

- The frontend is static files only and never touches the database.
- The backend never outputs HTML, only JSON.
- All queries that take user input use prepared statements.

## Project structure

```
expense-tracker/
├── .gitignore
├── README.md
├── frontend/
│   ├── js/
│       ├── api.js              # shared fetch helper
|       ├── expenses.js
|       └── categories.js
|   ├── css/
|        └── style.css
│   ├── index.html          # summary, expenses table, add/edit modal
│   ├── categories.html     # manage categories
│   ├── style.css   
└── backend/
    ├── db.php              # credentials, JSON headers, CORS, mysqli connection
    ├── categories.php      # categories endpoint
    ├── expenses.php        # expenses endpoint + monthly summary
    └── database.sql        # schema
```

## Getting started

### Prerequisites

- [XAMPP](https://www.apachefriends.org/) with Apache and MySQL
- A modern browser
- Git

### Installation

1. Clone the repository into XAMPP's `htdocs` folder:

   ```bash
   cd path/to/xampp/htdocs
   git clone <your-repo-url> expense-tracker
   ```

2. Start **Apache** and **MySQL** from the XAMPP control panel.

3. Open phpMyAdmin (`http://localhost/phpmyadmin`) and import `backend/database.sql`. This creates the `expense_tracker` database and its tables.

4. Open `backend/db.php` and check the credentials at the top match your MySQL setup. XAMPP's defaults are user `root` with an empty password.

5. Open the app:

   ```
   http://localhost/expense-tracker/frontend/index.html
   ```

> **Security note:** Database credentials live in `backend/db.php` and are committed with the code. This is fine for local development with XAMPP's defaults. Move them to a separate ignored file before pushing to a public or shared repository, or before deploying.

## Database

Two tables, one-to-many:

- `categories` (`id`, `name`, `color`, `created_at`)
- `expenses` (`id`, `category_id`, `title`, `amount`, `expense_date`, `note`, `created_at`, `updated_at`)

`expenses.category_id` references `categories.id` with `ON DELETE RESTRICT`, so a category that still has expenses cannot be deleted.

## API reference

Base URL: `http://localhost/expense-tracker/backend/`

All endpoints accept and return JSON.

### Categories: `categories.php`

| Method | Request                  | Description         |
| ------ | ------------------------ | ------------------- |
| GET    | `categories.php`         | List all categories |
| POST   | `categories.php`         | Create a category   |
| PUT    | `categories.php?id={id}` | Update a category   |
| DELETE | `categories.php?id={id}` | Delete a category   |

### Expenses: `expenses.php`

| Method | Request                                       | Description                                                              |
| ------ | --------------------------------------------- | ------------------------------------------------------------------------ |
| GET    | `expenses.php?month=YYYY-MM&category_id={id}` | List expenses and the summary for the period. Both filters are optional. |
| POST   | `expenses.php`                                | Create an expense                                                        |
| PUT    | `expenses.php?id={id}`                        | Update an expense                                                        |
| DELETE | `expenses.php?id={id}`                        | Delete an expense                                                        |

`GET expenses.php` returns `{ expenses, summary }`, where `summary` holds the total, the transaction count, and per-category totals.

### Response format

```json
{ "success": true, "data": {} }
```

```json
{ "success": false, "error": "Description of what went wrong" }
```

Errors use appropriate HTTP status codes (400 validation, 404 not found, 409 conflict, 500 server error).

## Development workflow

- `main` always contains working code.
- Each phase is built on a short-lived branch (`feature/...`, `chore/...`) and merged when complete.
- Commit messages follow `type(scope): summary`, for example `feat(backend): add categories GET and POST`.
  - Types: `feat`, `fix`, `refactor`, `docs`, `chore`
  - Scopes: `frontend`, `backend`, `db`
- Releases are tagged at working milestones.

## Roadmap

- [ ] **Phase 0:** Project setup, repository, README
- [ ] **Phase 1:** Database schema (`database.sql`)
- [ ] **Phase 2:** `db.php` and categories API
- [ ] **Phase 3:** Categories UI (`v0.1.0`)
- [ ] **Phase 4:** Expenses API with filters and summary
- [ ] **Phase 5:** Expenses UI and summary cards (`v1.0.0`)
- [ ] **Phase 6:** Hardening: CORS test with Live Server, error states, README check

## Next

Phase 1: create `backend/database.sql`.
