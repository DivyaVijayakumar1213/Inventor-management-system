# Student Management System (CRUD Web App)

A complete CRUD-based web application built per the SOP: **HTML/CSS/JavaScript frontend → Django REST Framework backend → SQLite database**.

## 1. Project Overview
Manage student records with full Create, Read, Update, Delete functionality, server- and client-side validation, search/sort, and a tested REST API.

**Entity: Student** — roll_no, first_name, last_name, email, phone, department, year, date_of_admission, is_active.

## 2. Architecture
```
Browser (HTML/CSS/JS, frontend/)
        |  fetch() -> JSON
        v
Django REST Framework API  (backend/sms_backend/)
        |  ORM
        v
SQLite Database (db.sqlite3, auto-created)
```

## 3. Folder Structure
```
student_management_system/
├── backend/sms_backend/
│   ├── manage.py
│   ├── requirements.txt
│   ├── .env.example
│   ├── sms_backend/        # project settings, urls, wsgi/asgi
│   └── students/           # app: models, serializers, views, urls, admin, tests
│       └── management/commands/seed_students.py
├── frontend/
│   ├── index.html
│   ├── style.css
│   ├── config.js           # API_BASE_URL
│   └── script.js
├── postman/
│   └── Student_Management_API.postman_collection.json
└── README.md
```

## 4. Prerequisites
- Python 3.9+
- pip
- A modern web browser
- (Optional) Postman for API testing

## 5. Backend Setup
```bash
cd backend/sms_backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

pip install -r requirements.txt

python manage.py migrate        # creates db.sqlite3 and tables
python manage.py seed_students  # optional: loads 4 sample students
python manage.py createsuperuser  # optional: for /admin/ access

python manage.py runserver
```
The API is now live at `http://127.0.0.1:8000/api/students/`.
Django admin (optional): `http://127.0.0.1:8000/admin/`.

## 6. Frontend Setup
No build step needed — it's plain HTML/CSS/JS.

- Open `frontend/index.html` directly in a browser, **or**
- Serve it (recommended, avoids some `file://` quirks):
  ```bash
  cd frontend
  python -m http.server 5500
  # then visit http://127.0.0.1:5500
  ```
If your backend runs on a different host/port, update `API_BASE_URL` in `frontend/config.js`.

CORS is already enabled on the backend (`django-cors-headers`) so the two origins can talk to each other.

## 7. REST API Reference
| Operation | Method | Endpoint                | Result                  |
|-----------|--------|--------------------------|--------------------------|
| Create    | POST   | `/api/students/`         | New record created (201) |
| Read all  | GET    | `/api/students/`         | Paginated list (200)     |
| Read one  | GET    | `/api/students/{id}/`    | Single record (200)      |
| Update    | PUT    | `/api/students/{id}/`    | Full update (200)        |
| Update    | PATCH  | `/api/students/{id}/`    | Partial update (200)     |
| Delete    | DELETE | `/api/students/{id}/`    | Record removed (200)     |

Query params on list: `?search=cs2024`, `?ordering=roll_no` or `?ordering=-created_at`.

### Example: create a student
```bash
curl -X POST http://127.0.0.1:8000/api/students/ \
  -H "Content-Type: application/json" \
  -d '{"roll_no":"CS2024099","first_name":"Test","last_name":"User","email":"test@example.com","department":"CS","year":2}'
```

## 8. Validation
- **Client-side** (script.js): required fields, email format, phone format (7–15 digits).
- **Server-side** (serializers.py / models.py): required fields, unique `roll_no` and `email`, email format, phone regex — enforced even if the client is bypassed (e.g. via curl/Postman).

## 9. Testing
Automated backend tests (create valid/missing/duplicate, read valid/invalid id, update valid/invalid id, delete valid/invalid id):
```bash
cd backend/sms_backend
python manage.py test
```

Manual API testing: import `postman/Student_Management_API.postman_collection.json` into Postman and run the requests in order (List → Create → Retrieve → Update → Delete, plus the negative-case requests).

Frontend: resize the browser window / use dev-tools device toolbar to confirm the responsive layout on desktop and mobile widths.

## 10. Security Notes
- `SECRET_KEY` and `DEBUG` are read from environment variables (see `.env.example`); the checked-in default is for local development only.
- `db.sqlite3` and `.env` are excluded via `.gitignore` — never commit real credentials.
- Server-side validation runs independently of the frontend.
- Turn `DEBUG=False` and set a real `ALLOWED_HOSTS` list before any real deployment.

## 11. Version Control
```bash
git init
git add .
git commit -m "Initial commit: Student Management System CRUD app"
git branch -M main
git remote add origin <your-repo-url>
git push -u origin main
```

## 12. Future Enhancements
- Authentication/authorization (JWT) for protected endpoints.
- Pagination controls and bulk import/export (CSV).
- React frontend as a drop-in replacement for the vanilla JS one.
- Dockerfile / docker-compose for one-command startup.

## 13. Demonstration Checklist (SOP Sec. 17)
- [x] App starts without errors (`runserver` + open `index.html`)
- [x] Database connects (`migrate` creates `db.sqlite3`)
- [x] Create works (form + POST)
- [x] Read/list works (table + GET, with search/sort)
- [x] Update works (Edit button + PUT)
- [x] Delete works (Delete button + confirm modal + DELETE)
- [x] Client + server validation
- [x] Search/filter implemented
- [x] API demonstrable via Postman collection
- [x] Automated tests included (`students/tests.py`)
