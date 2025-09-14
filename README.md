حتماً! 😄 بر اساس تمام تغییراتی که داشتیم (استفاده از `settings.json`، لاگ‌ها، نصب پکیج، Docker و اجرای برنامه از هر درایو)، این یک **فایل README جامع** برای GitHub است:

---

# Monitoring Project

**A lightweight website monitoring tool with email notifications.**
This project continuously checks a website's status and sends an email alert if it goes down. Logs are maintained for all checks.

---

## Features

* Monitor a website at configurable intervals.
* Send email notifications when the website is down.
* Maintain timestamped logs.
* Easy installation via `pip`.
* Docker support for containerized deployment.
* Configurable via `settings.json`.

---

## Requirements

* Python 3.10+
* pip
* Requests library (automatically installed with pip)
* Docker Desktop (optional, for container deployment)

---

## Project Structure

```
monitoring_project/
│
├── app/
│   ├── __init__.py
│   ├── main.py        # Main monitoring logic
│   ├── notifier.py    # Logging and email notifications
│   └── settings.json  # Configuration file  
│
├── logs/              # Log files (created automatically)
│   └──monitor.log
│
├── pyproject.toml     # Package metadata
└── README.md
```

---

## Installation (Local)

### 1. Clone the repository

```bash
git clone <repository_url>
cd monitoring_project_v.1.0.0
```

### 2. Optional: Create a virtual environment

```bash
python -m venv venv
# Activate the venv
venv\Scripts\activate   # Windows
source venv/bin/activate # Linux/macOS
```

### 3. Install the package

```bash
pip install .
```

### 4. Configuration

Edit `settings.json` to set:

```json
{
  "monitoring": {
    "url": "https://example.com",
    "interval_seconds": 30
  },
  "notifications": {
    "email": {
      "enabled": true,
      "sender_email": "your_email@gmail.com",
      "password": "your_app_password",
      "receiver_email": "receiver_email@gmail.com",
      "smtp_server": "smtp.gmail.com",
      "smtp_port": 587
    }
  }
}
```

> ⚠️ Use **App Passwords** for Gmail.

---

### 5. Running the program

* From CMD/PowerShell (any folder):

```bash
monitor
```

* Logs will be created in the **current working directory** under `logs/monitor.log`.

---

## Docker Deployment

### 1. Build Docker image

* Open Docker Desktop
* Go to **Images → Build**
* Select project folder containing `Dockerfile`
* Name: `monitoring_project`
* Tag: `latest`
* Click **Build**

### 2. Run Container

* Go to **Images → monitoring\_project\:latest → Run**
* Name container: `monitoring`
* Optional: Map logs folder

  ```
  Host Path: Y:/Projects/monitoring_project_v.1.0.0/logs
  Container Path: /app/logs
  ```
* Click **Run**

### 3. View Logs

* In Docker Desktop → **Containers / Apps** → select your container → **Logs**

---

Logging

All monitoring logs are saved in logs/monitor.log with timestamps:

[2025-09-14 03:00:00] [OK] https://example.com is up.
[2025-09-14 03:00:30] [DOWN] https://example.com is down!

---

## Notes

* Log files are **timestamped** and show status for each check.
* Program can run **from any directory**, logs will always be relative to the current working directory.
* Email notifications can be **disabled** in `settings.json`.

---

## Contributing

* Feel free to submit issues or pull requests.
* Follow Python best practices and keep logging consistent.

---

## License

'MIT © <AmirHaghbin>'

---

Contact

If you have any questions, suggestions, or need help:

Email: 'amirhaghbin7@gmail.com'

Linkedin: 'https://www.linkedin.com/in/amirhaghbin7'

---
