# 🔗 LinkSlice
### Simple, fast URL shortener — Express + MongoDB

LinkSlice is a minimal URL-shortening service built with Node.js, Express, and MongoDB. It provides a REST API to shorten long URLs and a redirect route to expand short codes to their original URLs.

---

# 📌 Problem Statement

Long URLs are hard to share and track. LinkSlice solves this by providing:
- Short, memorable links
- Persistent storage of mappings
- Simple REST API for programmatic use

---

# 🚀 Key Features

## 🔍 URL Shortening

- Create short URLs from long URLs
- Reuse existing short links for the same long URL
- Generates URL codes using `shortid`

## 🔁 Redirect

- Short URLs redirect to their original long URL via a single GET endpoint.

## 🗄 Persistence

- Uses MongoDB with a simple Mongoose model `Url` to store mappings and timestamps.

## ⚙ Minimal, Production-Ready Patterns

- Config management via `config` package
- Input validation using `valid-url`
- Modular route/controllers

---

# 🧠 How It Works

1. Client sends `POST /api/url/shorten` with `{ "longUrl": "https://example.com/..." }`.
2. Server validates `longUrl` and checks if a short record already exists.
3. If not present, server generates `urlCode` with `shortid`, constructs `shortUrl` using `baseUrl` from config, saves to MongoDB and returns the record.
4. Short link usage: `GET /:code` looks up the code and redirects to `longUrl`.

---

# 🏗 System Architecture

```txt
Client (curl, JS frontend)
  └─ POST /api/url/shorten  GET /:code
           │
           ▼
      Express Server (index.js)
           │
  Routes: /api/url (routes/url.js)
          /:code  (routes/index.js)
           │
           ▼
         MongoDB (Url model)
```

---

# 🛠 Tech Stack

## Backend

- Node.js
- Express.js
- MongoDB
- Mongoose

## Libraries

- `shortid` — generate short unique codes
- `valid-url` — validate submitted URLs
- `config` — configuration management
- `nodemon` (dev)

---

# 📂 Project Structure

```txt
LinkSlice-main/
├── index.js                 # App entry
├── package.json
├── config/
│   ├── db.js                # MongoDB connection helper
│   └── default.json         # baseUrl & mongoURI
├── models/
│   └── Url.js               # Mongoose schema
├── routes/
│   ├── index.js             # redirect route GET /:code
│   └── url.js               # POST /api/url/shorten
├── requests/
│   └── api.http             # example HTTP requests (optional)
└── README.md
```

See [index.js](index.js) and [package.json](package.json) for start scripts and configuration.

---

# 📡 API Endpoints

## Create Short URL

```http
POST /api/url/shorten
Content-Type: application/json

{
  "longUrl": "https://example.com/some/very/long/path"
}
```

### Example Response

```json
{
  "_id": "60f...",
  "longUrl": "https://example.com/some/very/long/path",
  "shortUrl": "http://localhost:5000/AbCd12",
  "urlCode": "AbCd12",
  "date": "2023-01-01T00:00:00.000Z",
  "__v": 0
}
```

## Redirect

```http
GET /:code
```

The server looks up `urlCode` and issues an HTTP redirect to `longUrl`. If no code is found, returns `404`.

---

# ⚙ Installation

## 1. Clone repository

```bash
git clone <repo-url>
cd LinkSlice-main
```

## 2. Install dependencies

```bash
npm install
```

## 3. Configure environment

Edit `config/default.json` or set environment variables. At minimum set your MongoDB connection string:

```json
{
  "mongoURI": "mongodb://localhost:27017/linkslice",
  "baseUrl": "http://localhost:5000"
}
```

You can also set `process.env` variables in your environment if preferred.

## 4. Start the server

Dev (with auto-reload):

```bash
npm run dev
```

Production:

```bash
npm start
```

Server listens on port `5000` by default (see `index.js`).

---

# 🧪 Example Requests

Use `curl` or the provided `requests/api.http` to test.

Create short link with `curl`:

```bash
curl -X POST http://localhost:5000/api/url/shorten \
  -H "Content-Type: application/json" \
  -d '{"longUrl":"https://example.com/blog/post/123"}'
```

Follow redirect:

```bash
curl -I http://localhost:5000/PUT_CODE_HERE
```

---

# 📈 Future Improvements

* Add click analytics (count, referrer, geo)
* Expiration for short links
* User accounts and link management UI
* Custom alias support (vanity URLs)
* Rate limiting and abuse protection
* Dockerfile and Kubernetes manifests for easier deployment

---

# 💡 Engineering Highlights

* Minimal, well-structured Express app
* Mongoose model for persistent storage
* `valid-url` ensures basic validation to avoid bad redirects
* Reuses existing short URL when long URL already stored

---

# ⚠ Important Notes

* This is a simple demo/service implementation and should not be used as-is in production without hardening.
* Add authentication, rate limiting, input sanitization and secure configuration for production readiness.

---

# 🤝 Contributing

Contributions welcome — open issues or pull requests for:

- Features (analytics, UI)
- Security hardening
- Tests and CI

---

# 📜 License

This project is licensed under the MIT License.

---

# 👨‍💻 Author

Pradeep Kumar Sinwar

Backend Developer | Competitive Programmer

---

# 🏁 Final Goal

Provide a reliable, lightweight URL shortening service that is easy to run locally and simple to extend.
