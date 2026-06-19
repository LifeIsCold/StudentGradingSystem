# Student Grading System (SGS)

Django web application for managing student grades at MIIT. Supports student lookup, grade queries, Excel export, CGPA-based recommendations, and admin authentication.

## Tech stack

- **Backend:** Django 5.x, SQLite
- **Libraries:** django-cors-headers, django-import-export, pandas, xlsxwriter
- **Production server:** Gunicorn (via Docker)

## Project structure

```
SGS/              # Django project settings and URLs
grading/          # Models: Student, Course, Semester, Enrollment, SGPA
api/              # REST-style views, login, search, recommendations
excel/            # Excel export views
templates/        # HTML templates (login, admin, Excel)
static/           # CSS and JavaScript
docker/           # Container entrypoint
```

## Features

- Admin sign-in and session management
- Search students by roll number, name, batch, or CGPA
- Fetch grades and SGPA per student
- Export student data to Excel
- Course recommendation based on student performance

## Quick start (Docker)

```bash
cp .env.example .env   # edit DJANGO_SECRET_KEY and ALLOWED_HOSTS
docker compose up -d --build
```

Open **http://localhost:8000**

Create an admin user (first run):

```bash
docker compose exec web python manage.py createsuperuser
```

Admin panel: **http://localhost:8000/admin/**

## Local development (without Docker)

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

## Environment variables

| Variable | Description | Default |
|----------|-------------|---------|
| `DJANGO_SECRET_KEY` | Secret key for production | (required in prod) |
| `DJANGO_DEBUG` | Enable debug mode | `False` |
| `DJANGO_ALLOWED_HOSTS` | Comma-separated hosts | `localhost,127.0.0.1` |
| `DJANGO_DB_PATH` | SQLite file path (Docker) | `/app/data/db.sqlite3` |
| `SGS_PORT` | Host port for Docker | `8000` |
| `GUNICORN_WORKERS` | Gunicorn worker count | `2` |

## API routes (high level)

| Route | Purpose |
|-------|---------|
| `/` | Login page |
| `/signin`, `/signout` | Authentication |
| `/admin_home/` | Admin dashboard |
| `/all_students/<input>/` | Search by roll number |
| `/specific_student/<input>/` | Search by name or roll |
| `/getStudentGrades/<input>/` | Student grades |
| `/recommendation/<input>/` | Course recommendations |
| `/excel_export` | Export to Excel |

## Deploy on a Linux server

1. Clone the repo and copy `.env.example` to `.env`
2. Set `DJANGO_SECRET_KEY`, `DJANGO_DEBUG=False`, and your server IP in `DJANGO_ALLOWED_HOSTS`
3. Run `docker compose up -d --build`
4. Put a reverse proxy (nginx/Caddy) in front of port 8000 for HTTPS

Database data persists in the Docker volume `sgs_data`.
