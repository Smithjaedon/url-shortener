# Snip

URL shortener in the spirit of Bitly. Paste a long URL, get a short link, visiting it redirects. Backend is Django, frontend is separate.

---

## Demo

[Url Shortener Demo](https://www.youtube.com/watch?v=OOrBvUCa1zI)

---

## Tech Stack

### Backend
| Technology | Role |
|---|---|
| **Django** | Web framework |
| **Django REST Framework** | API layer |
| **PostgreSQL** | Storage for URL mappings |
| **Celery** | Task scheduling (expiry cleanup) |
| **Redis** | Celery broker and task queue |
| **Docker** | Redis container |

### Frontend
| Technology | Role |
|---|---|
| SvelteKit | UI framework |
| Bun | Runtime and package manager |
| Vite | Build tool and dev server |

---

## How It Works

1. Paste a long URL in the Snip frontend.
2. Click **Shorten**. The frontend sends a `POST` request to the Django API.
3. The backend generates a **random alphanumeric code**, stores `code → original URL` in PostgreSQL, and returns the short URL.
4. **Copy** the link or **click** it in the UI.
5. Visiting the short link calls the backend with the code, which looks it up and **redirects** to the original URL.

---

## Backend Architecture

### URL Shortening Endpoint
- Accepts a long URL via `POST` request
- Generates a unique random alphanumeric slug
- Persists `slug → URL` in PostgreSQL
- Returns the shortened URL

### Redirect Endpoint
- Accepts a slug via `GET` request
- Looks up the original URL in PostgreSQL
- Issues an HTTP redirect

### Garbage Collection (Celery + Redis)
Links expire automatically. A **scheduled Celery task**, brokered through **Redis**:

- Finds links not accessed in the last **5 minutes**
- Deletes them from PostgreSQL
- Runs continuously in the background

---

## Backend Concepts Covered

- **REST API design** with Django REST Framework
- **Relational modelling** with PostgreSQL
- **Async task processing** with Celery
- **Message brokering** with Redis
- **Scheduled jobs** for data lifecycle management
- **Slug-based redirect logic**
- **Containerisation** with Docker for Redis
