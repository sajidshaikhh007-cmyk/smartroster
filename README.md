# Smart Roster Management System

A Flask and SQLite web application for planning, generating, and validating monthly employee rosters across brands, processes, and operational pods. Includes role-based dashboards, task management, leave tracking, holiday calendars, and CSV export.

---

## 🚀 Live Demo & Deployment on Vercel

This repository is pre-configured with `api/index.py`, `vercel.json`, and `.vercelignore` for serverless deployment on [Vercel](https://vercel.com).

### Option 1: Deploy via Vercel Dashboard & GitHub (Recommended)

1. **Push your code to GitHub**:
   ```bash
   git add .
   git commit -m "Configure for Vercel deployment"
   git push origin main
   ```
2. **Import into Vercel**:
   - Go to [vercel.com/new](https://vercel.com/new).
   - Connect your GitHub account and select your `smartroster` repository.
3. **Configure Project Settings**:
   - **Framework Preset**: Select `Other`.
   - **Root Directory**:
     - If your project files (`app.py`, `vercel.json`) are at the repository root, leave it as `./`.
     - If your repository contains a nested folder (e.g., `smartroster/app.py`), set the **Root Directory** to `smartroster`.
4. **Environment Variables**:
   Under **Environment Variables**, add:
   - `SECRET_KEY`: Enter a random secure string (e.g. generate via `openssl rand -hex 32` or any long random string).
   - `FLASK_ENV`: `production` (optional)
5. **Deploy**:
   - Click **Deploy**. Vercel will install dependencies from `requirements.txt`, bundle `api/index.py`, and launch your live site.

---

### Option 2: Deploy via Vercel CLI

1. **Install Vercel CLI**:
   ```bash
   npm install -g vercel
   ```
2. **Login to Vercel**:
   ```bash
   vercel login
   ```
3. **Deploy from project directory**:
   ```bash
   # Navigate to the folder containing vercel.json and app.py
   cd smartroster
   vercel
   ```
4. **Deploy to Production**:
   ```bash
   vercel --prod
   ```

---

## ⚙️ How Vercel Serverless Works with This Project

- **Entrypoint (`api/index.py`)**: Acts as the WSGI adapter exposing `handler = app` to Vercel's Python Serverless Runtime.
- **Routing & Assets (`vercel.json`)**:
  - Rewrites all incoming requests (`/(.*)`) to the serverless entrypoint `/api/index.py`.
  - Bundles HTML templates and static assets (CSS/JS) using `"includeFiles": "{templates/**,static/**}"`.
- **Database on Serverless (`database.py`)**:
  - Vercel functions have a read-only filesystem except for the temporary `/tmp` directory.
  - When running on Vercel (detected automatically via the `VERCEL` environment variable), the application creates and seeds the SQLite database at `/tmp/smart_roster.db`.
  - *Note on Persistence*: Serverless instances are stateless and ephemeral; the `/tmp` database will re-initialize when a container cold-starts. This is ideal for evaluations, interviews, and portfolio demonstrations. For permanent production persistence across instances, connect a hosted cloud database (e.g. Turso SQLite via libsql, Supabase, or Neon PostgreSQL) or host on a continuous container service (such as Render or Railway).

---

## 💻 Local Development Setup

### 1. Prerequisites
- Python 3.10, 3.11, 3.12, or 3.13 installed.

### 2. Setup Virtual Environment
```powershell
# Windows (PowerShell)
python -m venv .venv
.\.venv\Scripts\activate

# macOS / Linux (Bash)
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install Dependencies
```bash
python -m pip install -r requirements.txt
```

### 4. Initialize Database
```bash
python database.py
```
This sets up tables and populates sample data (30 employees, pods, brands, process rules, and default rosters).

### 5. Start the Web Server
```bash
python app.py
```

Open your browser at **[http://127.0.0.1:5000](http://127.0.0.1:5000)** or **[http://localhost:5000](http://localhost:5000)**.

---

## 🔑 Demo Login Credentials

The database comes pre-seeded with sample users for all roles:

| Role | Username | Password | Permitted Views & Capabilities |
| :--- | :--- | :--- | :--- |
| **Manager** | `manager` | `manager123` | Full access: roster generation, staffing rules, overrides, task assignment |
| **Pod Lead** | `podlead` | `podlead123` | Pod overview, member rosters, operational updates |
| **Employee** | `employee` | `employee123` | Personal workspace (`/my-work`), personal schedule, work updates |
| **Client / Company** | `company_glowbeauty`<br>`company_urbanmart`<br>`company_freshbite`<br>`company_homenest`<br>`company_techzone` | `company123` | Scoped to their brand: view assigned resources, submit work requests |

> ⚠️ *Remember to change default credentials and set a secure `SECRET_KEY` before public production use.*

---

## 🗺️ Application Routes

- `/` – Executive company operations dashboard and task queues
- `/roster` – Monthly roster generator, validation conflict checker, manager overrides, and leave approvals
- `/manage` – Assign and reassign employees to brand/process pairs
- `/views` – Filtered roster tables (Company-wide, Pod, Brand, Process, and Employee)
- `/employees` – Searchable employee directory with weekly-off and pod metadata
- `/rules` – Operational calendars (365-day, Sundays off, Holidays off) and minimum staffing rules
- `/calendar` – Visual monthly employee schedule grid
- `/my-work` – Personalized employee portal for daily updates and task progress
- `/export/tasks.csv?month=YYYY-MM` – Export monthly work tasks in CSV format
- `/login` / `/logout` – Authentication endpoints

---

## 📁 Project Structure

```text
smartroster/
├── api/
│   └── index.py            # Vercel serverless function entrypoint (handler)
├── static/
│   ├── style.css           # Application styling and responsive grid
│   └── script.js           # Client-side scripts
├── templates/
│   ├── _topbar.html        # Shared navigation header
│   ├── calendar.html       # Monthly grid calendar view
│   ├── company.html        # Company overview screen
│   ├── employees.html      # Employee directory
│   ├── index.html          # Main dashboard
│   ├── login.html          # Authentication view
│   ├── manage.html         # Brand/process assignment manager
│   ├── my_work.html        # Employee task board
│   ├── roster.html         # Roster generation & operations board
│   ├── rules.html          # Staffing rules & operating calendar config
│   └── views.html          # Multi-dimensional roster views
├── app.py                  # Main Flask application & route controllers
├── database.py             # SQLite schema, migrations, and seed data generator
├── scheduler.py            # Deterministic roster generation & validation engine
├── vercel.json             # Vercel deployment, rewrites, and file bundling config
├── .vercelignore           # Excludes local caches, venv, and local db from Vercel build
├── requirements.txt        # Python package dependencies (Flask)
├── .env.example            # Environment variables template
└── README.md               # Documentation & deployment guide
```

---

## 🛠️ Vercel Deployment Troubleshooting

- **Error: 404 NOT_FOUND on routes like `/roster` or `/login`**:
  Ensure `vercel.json` is present in the root of the directory deployed to Vercel, containing the rewrites to `/api/index.py`.
- **Error: TemplateNotFound or static CSS not loading**:
  Check `vercel.json` to confirm `"functions": { "api/index.py": { "includeFiles": "{templates/**,static/**}" } }` is present.
- **Root Directory setting on Vercel**:
  If your GitHub repo has a subfolder named `smartroster`, make sure in your Vercel Project Settings > General, the **Root Directory** is configured to `smartroster`.
- **Database resets on reload**:
  Serverless lambdas shut down when idle. When invoked again on a new container, SQLite re-populates from the seed script in `/tmp/smart_roster.db`. For persistent data storage across serverless restarts, integrate an external cloud SQL provider.
