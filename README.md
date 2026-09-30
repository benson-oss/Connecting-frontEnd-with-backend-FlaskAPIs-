# Study Sprint Tracker — Student Backend Practical

## Purpose

The client side is already complete. Your job is to build the Flask backend routes that make it work.

This practical is designed to help you practise:

- Flask routes and HTTP methods
- JSON request and response bodies
- CRUD operations
- Login using Flask `session`
- Persisting values across requests using sessions
- Reading and writing browser cookies
- Choosing useful HTTP status codes
- Debugging requests with the browser Network tab

The project deliberately gives you **one working page route** and **one working API GET route**. The remaining backend routes are yours to create.

---

## Folder structure

```text
study_sprint_student_starter/
├── README.md
├── requirements.txt
└── server/
    ├── app.py
    └── static/
        ├── index.html
        ├── css/
        │   └── styles.css
        └── js/
            └── app.js
```

You should mainly work in:

```text
server/app.py
```

The HTML, CSS, and JavaScript have already been provided.

---

## Setup

From inside the project folder:

```bash
python -m venv .venv
```

### Windows PowerShell

```powershell
.venv\Scripts\Activate.ps1
```

### macOS/Linux

```bash
source .venv/bin/activate
```

Install the dependency:

```bash
pip install -r requirements.txt
```

Run the Flask app:

```bash
python server/app.py
```

Open:

```text
http://127.0.0.1:5000
```

---

# What is already implemented?

## `GET /`

Serves `index.html`.

Expected status:

```text
200 OK
```

## `GET /api/study-sessions`

Returns every study sprint.

Example response:

```json
[
  {
    "id": 1,
    "topic": "Understand GET requests",
    "minutes": 25,
    "completed": false
  }
]
```

Expected status:

```text
200 OK
```

This is your example of a working Flask API route.

---

# Routes you must build

## 1. `GET /api/me` — Read session + cookie data

This route should show what Flask remembers about the current learner.

### Request body

None.

### What your route should do

- Increase a session value called `visits` every time the route is called.
- Read `username` from the Flask session.
- Read `focus_mode` from the browser cookie.
- If the cookie does not exist yet, use `standard` as the default.

### Expected JSON response shape

```json
{
  "username": "Amina",
  "visits": 3,
  "focus_mode": "deep"
}
```

If no learner has logged in yet, `username` may be `null`.

### Expected status

```text
200 OK
```

### Useful clues

Look up:

- `session.get(...)`
- `request.cookies.get(...)`

---

## 2. `POST /api/login` — Login with a Flask session

The browser sends a username to the backend.

### Expected request JSON

```json
{
  "username": "Amina"
}
```

### What your route should do

- Read the JSON body.
- Remove unnecessary spaces around the username.
- Reject an empty username.
- Store the username in Flask `session`.
- Set/reset the session visit counter to `0`.

### Successful response example

```json
{
  "message": "Login successful",
  "username": "Amina"
}
```

### Success status

```text
201 Created
```

### Invalid request example

```json
{
  "error": "Username is required."
}
```

### Invalid request status

```text
400 Bad Request
```

### Useful clues

Look up:

- `request.get_json()`
- `session["username"] = ...`

---

## 3. `POST /api/logout` — Clear the session

### Request body

None.

### What your route should do

Clear all values currently stored in the Flask session.

### Example response

```json
{
  "message": "Session cleared. You are logged out."
}
```

### Expected status

```text
200 OK
```

---

## 4. `POST /api/study-sessions` — CREATE

Create a new study sprint.

### Expected request JSON

```json
{
  "topic": "Practise POST requests",
  "minutes": 40
}
```

The browser may send `minutes` as a string because it comes from an HTML input, so your backend should safely convert it to an integer.

### Validation

Reject the request when:

- `topic` is empty
- `minutes` cannot be converted to a number

### New object requirements

A new sprint should look like:

```json
{
  "id": 3,
  "topic": "Practise POST requests",
  "minutes": 40,
  "completed": false
}
```

Create an id that does not clash with an existing item.

### Success status

```text
201 Created
```

### Validation status

```text
400 Bad Request
```

Possible error JSON:

```json
{
  "error": "Topic is required."
}
```

or

```json
{
  "error": "Minutes must be a number."
}
```

---

## 5. `PATCH /api/study-sessions/<session_id>` — UPDATE

The supplied interface uses this route to mark a sprint as completed.

For example:

```text
PATCH /api/study-sessions/2
```

### Request body

None is required for this exercise.

### What your route should do

- Find the sprint with the id from the URL.
- Change `completed` to `true`.
- Return the updated object.

### Successful response example

```json
{
  "id": 2,
  "topic": "Practice Flask routes",
  "minutes": 30,
  "completed": true
}
```

### Success status

```text
200 OK
```

### Missing id response

```json
{
  "error": "Study session not found."
}
```

### Missing id status

```text
404 Not Found
```

A helper named `find_study_session(session_id)` is already provided in `app.py`.

---

## 6. `DELETE /api/study-sessions/<session_id>` — DELETE

For example:

```text
DELETE /api/study-sessions/2
```

### Request body

None.

### What your route should do

- Find the requested sprint.
- Remove it from `study_sessions`.
- Return a confirmation response.

### Example response

```json
{
  "message": "Study session deleted.",
  "deleted": {
    "id": 2,
    "topic": "Practice Flask routes",
    "minutes": 30,
    "completed": false
  }
}
```

### Success status

```text
200 OK
```

### Missing id status

```text
404 Not Found
```

### Missing id response

```json
{
  "error": "Study session not found."
}
```

---

## 7. `POST /api/focus-mode` — Set a browser cookie

This route is for practising a manually created cookie.

### Expected request JSON

```json
{
  "mode": "deep"
}
```

### Allowed values

```text
standard
deep
revision
practice
```

### What your route should do

- Validate the incoming mode.
- Return a `400` response if it is invalid.
- Create a Flask response.
- Attach a cookie named `focus_mode`.
- Make it last for 7 days.
- Use `SameSite=Lax`.

### Successful JSON response

```json
{
  "message": "Focus mode saved.",
  "focus_mode": "deep"
}
```

### Success status

```text
200 OK
```

### Invalid value response

```json
{
  "error": "Invalid focus mode."
}
```

### Invalid value status

```text
400 Bad Request
```

### Useful clues

Look up:

- `make_response(...)`
- `response.set_cookie(...)`

Seven days in seconds can be calculated as:

```text
60 × 60 × 24 × 7
```

---

# Suggested build order

Work in this order so you can test one concept at a time:

1. Confirm `GET /api/study-sessions` works.
2. Build `POST /api/study-sessions`.
3. Build `PATCH /api/study-sessions/<id>`.
4. Build `DELETE /api/study-sessions/<id>`.
5. Build `POST /api/login`.
6. Build `GET /api/me`.
7. Build `POST /api/logout`.
8. Build `POST /api/focus-mode`.
9. Uncomment `runSafely(loadUser);` at the bottom of `static/js/app.js` after `/api/me` works.

---

# Testing checklist

Use both the page and your browser developer tools.

## CRUD

- [ ] Existing sprints load when the page opens.
- [ ] A new sprint can be added without refreshing the page.
- [ ] A sprint can be marked complete.
- [ ] A sprint can be deleted.
- [ ] An unknown sprint id returns `404`.

## Login + session

- [ ] Enter a username and click **Login**.
- [ ] Refresh the browser and confirm the username is still remembered.
- [ ] Call `/api/me` more than once and confirm the visit counter changes.
- [ ] Click **Logout** and confirm session data is cleared.

## Cookie

- [ ] Select a focus mode and save it.
- [ ] Open DevTools and find the `focus_mode` cookie.
- [ ] Refresh and confirm `/api/me` can read the cookie.

## Network tab

For every action, inspect:

- HTTP method
- endpoint
- request payload
- response JSON
- status code

---

# Important learning distinction

These three ideas are related, but they are not the same:

### Login

Login is the action where the user identifies themselves to the server.

### Session

A session lets the server remember information across multiple requests. In this exercise, Flask remembers the learner's username and visit count.

### Cookie

A cookie is stored by the browser and sent back to the server with later requests. In this exercise, you manually create a cookie only for the non-sensitive `focus_mode` preference.

Flask's default session implementation also uses a signed session cookie internally, which is another useful point to inspect in DevTools.

---

# Challenge questions

1. Why does `GET /api/study-sessions` need no request body?
2. Why is `POST` appropriate when creating a sprint?
3. Why is `PATCH` suitable when changing only `completed`?
4. Why should an unknown id return `404` instead of `200`?
5. What is the difference between the manually created `focus_mode` cookie and values stored in Flask `session`?
6. Why should you avoid storing passwords or other sensitive information directly in a normal browser cookie?
7. What happens to `study_sessions` when the Flask server restarts, and why?

The goal is not only to make the buttons work. Be able to explain **what request the browser sends, what the Flask route does, what state is stored, and what response comes back**.
