# Secure-Login-System
# 🔐 Secure Login System

A production-ready secure authentication web application built with **Node.js** and **Express**, featuring hashed passwords, input validation, session management, rate limiting, and optional Two-Factor Authentication (2FA).

---

## 🚀 Features

| Feature | Details |
|--------|---------|
| 🔒 **Password Hashing** | bcryptjs with 12 salt rounds |
| 🛡 **Input Validation** | express-validator — sanitizes all inputs, prevents injection |
| 🍪 **Session Management** | Secure, HttpOnly, SameSite cookies via express-session |
| 🚦 **Rate Limiting** | Blocks brute-force with express-rate-limit |
| 🔑 **Account Locking** | Auto-locks after 5 failed attempts for 15 minutes |
| 📱 **2FA (TOTP)** | QR-code setup, works with Google Authenticator / Authy |
| 🧢 **Security Headers** | Helmet.js — CSP, XSS, clickjacking protection |
| 💾 **Persistent Storage** | JSON file-based DB (easily swappable for PostgreSQL/MySQL) |
| 🎨 **Modern UI** | Dark, responsive SPA — no frameworks, vanilla JS |

---

## 📁 Project Structure

```
secure-login/
├── server.js              # Express app entry point
├── package.json
├── .env.example           # Environment variable template
├── .gitignore
│
├── config/
│   └── database.js        # JSON file-based user store
│
├── middleware/
│   └── auth.js            # Session guard middleware
│
├── routes/
│   └── auth.js            # All auth API routes
│
├── utils/
│   └── validators.js      # Input validation rules
│
└── public/
    ├── index.html         # Single-page app shell
    ├── css/styles.css     # Styling
    └── js/app.js          # Frontend SPA router & logic
```

---

## ⚙️ Getting Started

### Prerequisites
- Node.js v18+ 
- npm

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/secure-login-system.git
cd secure-login-system

# 2. Install dependencies
npm install

# 3. Set up environment
cp .env.example .env
# Edit .env and set a strong SESSION_SECRET

# 4. Start the server
npm start
```

Open your browser at **http://localhost:3000**

---

## 🔐 Security Implementation Details

### Password Hashing (bcryptjs)
Passwords are hashed using **bcrypt** with a cost factor of 12 — never stored in plain text.

```js
const passwordHash = await bcrypt.hash(password, 12);
// Verification
const valid = await bcrypt.compare(inputPassword, storedHash);
```

### Input Validation & SQL Injection Prevention
All inputs are validated and sanitized using **express-validator**:
- Usernames: alphanumeric + underscore only, escaped
- Emails: normalized and validated
- Passwords: minimum 8 chars, must include uppercase, lowercase, number, special character
- All values escaped before use — SQL injection impossible

### Session Management
```js
// Sessions configured with:
httpOnly: true      // JS cannot access cookies
secure: true        // HTTPS only in production
sameSite: 'strict'  // No cross-site transmission
maxAge: 24hrs       // Auto-expiry
name: 'sessionId'   // Non-default name
```

### Rate Limiting & Account Lockout
- API endpoints: max **20 requests / 15 min** per IP
- Failed logins: account locked for **15 minutes** after 5 failed attempts

### Two-Factor Authentication (TOTP)
- Uses the **TOTP standard** (RFC 6238)
- Generates secret → shows QR code → user scans with any authenticator app
- Token verified before granting session access

### Security Headers (Helmet.js)
- `Content-Security-Policy` — restricts resource origins
- `X-XSS-Protection` — legacy XSS filter
- `X-Frame-Options` — prevents clickjacking
- `X-Content-Type-Options` — no MIME sniffing

---

## 📡 API Reference

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Create new account |
| POST | `/api/auth/login` | Authenticate user |
| POST | `/api/auth/logout` | Destroy session |
| GET  | `/api/auth/user` | Get current user info |
| POST | `/api/auth/2fa/setup` | Generate 2FA QR code |
| POST | `/api/auth/2fa/enable` | Confirm and enable 2FA |
| POST | `/api/auth/2fa/disable` | Disable 2FA |
| POST | `/api/auth/2fa/verify` | Verify TOTP during login |

---

## 🌐 Deploying to GitHub

```bash
git init
git add .
git commit -m "Initial commit: Secure Login System"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/secure-login-system.git
git push -u origin main
```

> ⚠️ **Never push your `.env` file.** It's listed in `.gitignore`.

---

## 🔄 Upgrading the Database

The current implementation uses a JSON file store (`data/users.json`) for simplicity. To use a real database:

1. Install your driver: `npm install pg` (PostgreSQL) or `npm install mysql2`
2. Replace the functions in `config/database.js` with SQL queries
3. Use **parameterized queries** at all times to prevent SQL injection:
   ```js
   // ✅ Safe
   db.query('SELECT * FROM users WHERE username = $1', [username])
   // ❌ Dangerous
   db.query(`SELECT * FROM users WHERE username = '${username}'`)
   ```

---

## 🧪 Testing Checklist

- [x] Register with valid data → success
- [x] Register with duplicate username → error
- [x] Register with weak password → validation error
- [x] Login with correct credentials → session created
- [x] Login with wrong password 5x → account locked
- [x] Logout → session destroyed, cookie cleared
- [x] Access `/dashboard` without session → redirect to login
- [x] Enable 2FA → scan QR → verify token → 2FA active
- [x] Login with 2FA enabled → prompted for TOTP code
- [x] Rate limit exceeded → 429 response

---

## 📜 License

MIT — free to use and modify.

---

## 👤 Author

Built as part of a secure web development assignment.  
Due: 07 June 2026
