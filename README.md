# Smart Roster Management System

A production-ready Flask and SQLite web application for planning, generating, and validating monthly employee rosters across brands, operational processes, and pods. Includes role-based dashboards, task management, leave tracking, holiday calendars, and CSV export.

---

## 🚀 Live Deployment on Vercel (Zero-Config)

This project is built to deploy directly to **[Vercel](https://vercel.com)** using Vercel's native **Zero-Configuration Python Runtime**. 

> **Note**: You **do not** need an `api/` folder or a `vercel.json` file. Vercel automatically detects `requirements.txt` and locates your Flask application instance (`app`) in `app.py` at the root.

---

### Option 1: Deploy via Vercel Dashboard & GitHub (Recommended)

1. **Push your code to GitHub**:
   ```bash
   git add .
   git commit -m "Prepare for zero-config Vercel deployment"
   git push origin main
   ```

2. **Import Project on Vercel**:
   - Go to [vercel.com/new](https://vercel.com/new).
   - Under **Import Git Repository**, select your `smartroster` repository.

3. **Configure Project Settings**:
   - **Framework Preset**: Leave as `Other` (Vercel will auto-detect Python).
   - **Root Directory**:
     - If `app.py` and `requirements.txt` are at the root of your GitHub repository, leave this as `./`.
     - If your files are inside a subfolder (e.g., `smartroster/app.py`), click **Edit** and set **Root Directory** to `smartroster`.
   - **Build and Output Settings**: Leave default (no build command needed; Vercel installs dependencies automatically).

4. **Add Environment Variables**:
   Under **Environment Variables**, add:
   - `SECRET_KEY`: Any long random secure string (e.g. `flask-secret-key-prod-982348234`).
   - `FLASK_ENV`: `production` *(optional)*.

5. **Deploy**:
   - Click **Deploy**. Vercel will build your Python environment, package your templates and static assets, and launch your live site.

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

3. **Deploy from your project directory**:
   ```bash
   # Run from the directory containing app.py and requirements.txt
   vercel
   ```
   Follow the interactive prompts:
   - *Set up and deploy?* `Y`
   - *Which scope?* (Select your team or personal account)
   - *Link to existing project?* `N`
   - *What's your project's name?* `smartroster`
   - *In which directory is your code located?* `./`

4. **Deploy to Production**:
   ```bash
   vercel --prod
   ```

---

## ⚙️ How Vercel Runs This Flask Application

- **Auto-Detection (`app.py` & `requirements.txt`)**:
  Vercel inspects `requirements.txt` for `Flask` and automatically routes web traffic to the WSGI `app` variable declared in `app.py`.
- **Templates & Static Assets**:
  Flask's `templates/` and `static/` folders are bundled alongside `app.py` and served natively.
- **Database on Serverless (`database.py`)**:
  - Vercel functions run in a serverless environment with a read-only filesystem, except for the temporary `/tmp` directory.
  - The application automatically detects Vercel through the built-in `VERCEL` environment variable and creates/seeds an SQLite database at `/tmp/smart_roster.db` upon initial container launch.
  - *Persistence Note*: Serverless instances are ephemeral and cold-start periodically. For persistent production data storage, connect an external managed database (e.g. Supabase PostgreSQL, Neon, or Turso SQLite).

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
pip install -r requirements.txt
```

### 4. Initialize Database
```bash
python database.py
```
This initializes the SQLite schema and seeds 30 employees, pods, brands, process rules, and default monthly rosters.

### 5. Start the Web Server
```bash
python app.py
```

Open your browser at **[http://127.0.0.1:5000](http://127.0.0.1:5000)**.

---

## 🔑 Demo Login Credentials

The application is pre-seeded with sample users for all operational roles:

| Role | Username | Password | Permitted Views & Capabilities |
| :--- | :--- | :--- | :--- |
| **Manager** | `manager` | `manager123` | Full access: roster generation, staffing rules, overrides, task assignment |
| **Pod Lead** | `podlead` | `podlead123` | Pod overview, member rosters, operational updates |
| **Employee** | `employee` | `employee123` | Personal workspace (`/my-work`), personal schedule, daily updates |
| **Client / Company** | `company_glowbeauty`<br>`company_urbanmart`<br>`company_freshbite`<br>`company_homenest`<br>`company_techzone` | `company123` | Scoped to their brand: view assigned resources, submit work requests |

> ⚠️ *Remember to set a custom, secure `SECRET_KEY` in production.*

---

## 🗺️ Application Routes

- `/` – Executive company operations dashboard and task queues
- `/roster` – Monthly roster generator, validation conflict checker, manager overrides, and leave approvals
- `/manage` – Assign and reassign employees to brand/process pairs
- `/views` – Multi-dimensional filtered roster tables (Company-wide, Pod, Brand, Process, Employee)
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
├── static/
│   ├── style.css           # Application styling and responsive layout
│   └── script.js           # Client-side validation and interactive UI helpers
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
├── app.py                  # Main Flask application & route controllers (Vercel entrypoint)
├── database.py             # SQLite schema, migrations, and auto-seeding logic
├── scheduler.py            # Deterministic roster generation & validation engine
├── requirements.txt        # Python dependencies (Flask)
├── .vercelignore           # Excludes local caches, venv, and local db from Vercel build
├── .env.example            # Environment variables template
└── README.md               # Documentation & deployment guide
```

---

## 🛠️ Vercel Deployment Troubleshooting

- **Error: 404 NOT_FOUND on routes like `/roster` or `/login`**:
  - In Vercel Project Settings > General, check the **Root Directory**. If your repository has a folder structure like `smartroster/app.py`, set Root Directory to `smartroster`.
- **Error: Internal Server Error on form submission / login**:
  - Make sure you added `SECRET_KEY` under **Project Settings > Environment Variables** in Vercel.
- **Database resets after inactivity**:
  - Vercel functions are stateless serverless containers. When idle, containers freeze or spin down. On subsequent cold-starts, `/tmp/smart_roster.db` re-initializes from seed data. For long-term persistent production data, connect an external cloud SQL provider (such as Neon, Supabase, or Turso).
