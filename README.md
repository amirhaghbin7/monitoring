# Website Monitor

**A simple Python-based website monitoring tool** that checks a website's availability, logs the up/down status to a file, and sends an email notification when the site goes down.

---

## Status

* **Language:** Python
* **Current features:**

  * Check a single website's HTTP status (up/down)
  * Save each check result to a log file (timestamp, URL, status code, status)
  * Send an email alert when the website is detected as down
* **Planned / Future features:**

  * Monitor multiple websites concurrently
  * Record ping (ICMP) round-trip times in the logs
  * Add a web-based UI/UX dashboard
  * Support richer notification channels (SMS, Slack, webhook)

---

## Table of Contents

1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [Usage](#usage)
5. [Logging format](#logging-format)
6. [Email notifications](#email-notifications)
7. [Run as service / Docker](#run-as-service--docker)
8. [Testing](#testing)
9. [Contributing](#contributing)
10. [License](#license)
11. [Contact](#contact)

---

## Requirements

* Python 3.8+
* A working SMTP email account for sending alerts (Gmail, SMTP provider, etc.)
* Recommended Python packages (example):

  * `requests` — for HTTP checks
  * `python-dotenv` — optional, for loading environment variables
  * `smtplib` / `email` — built-in modules used for sending email
  * `concurrent.futures` or `asyncio` — for future concurrent checks

You can install runtime dependencies with:

```bash
pip install -r requirements.txt
```

---

## Installation

1. Clone the repository:

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

2. (Optional) Create and activate a Python virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate   # macOS / Linux
.\.venv\Scripts\activate  # Windows
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Configuration

Create a `.env` file (or edit `config.yaml` / JSON if you prefer) with the following environment variables:

```
# Target site to monitor
TARGET_URL=https://example.com

# Checking interval in seconds
CHECK_INTERVAL=60

# Log file path
LOG_FILE=monitor.log

# Email settings (SMTP)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@example.com
SMTP_PASSWORD=your-smtp-password-or-app-password
EMAIL_FROM=your-email@example.com
EMAIL_TO=recipient@example.com

# Optional: HTTP expected statuses (comma separated)
EXPECTED_STATUS_CODES=200,301,302
```

**Important:** If using Gmail, create an app password (recommended) or enable the appropriate security settings for SMTP. Never commit real credentials to Git — use `.gitignore` to exclude `.env`.

Add `.env` to `.gitignore`:

```
# Ignore environment files
.env
config.yaml
secrets.json
```

---

## Usage

Run the monitoring script from the repository root:

```bash
python monitor.py
```

Typical behavior:

* On each interval (configured by `CHECK_INTERVAL`), the script sends an HTTP GET request to `TARGET_URL`.
* The result (timestamp, URL, HTTP status, up/down) is appended to `LOG_FILE`.
* If the site is considered "down" (non-expected status code, connection error, or timeout), an email alert is sent to `EMAIL_TO`.

### Example command with environment file

```bash
# If using python-dotenv and a .env file
export $(cat .env | xargs)        # macOS / Linux
python monitor.py
```

---

## Logging format

Each check appends a single line to the log file. Example format (ISO 8601 timestamp):

```
2025-09-14T12:34:56Z | https://example.com | 200 | UP | response_time=0.142s
2025-09-14T12:35:56Z | https://example.com | (timeout) | DOWN | error=ConnectionTimeout
```

Fields:

* Timestamp (UTC recommended)
* URL
* HTTP status code (or error description)
* Status (UP / DOWN)
* Optional metadata (response time, ping, etc.)

---

## Email notifications

The project includes a simple email sender that uses SMTP. The email contains:

* Subject: `ALERT: <TARGET_URL> is DOWN`
* Body: timestamp, URL, status code or error, last successful check time

**Tips:**

* Use app-specific passwords (Gmail) rather than your main account password.
* Consider rate-limiting alerts to avoid email spam (for example, send one alert per downtime event, not every failed check).
* Add a recovery/`UP` email to notify when the site is back up.

---

## Run as a service / Docker

### Systemd service (Linux)

Create a `website-monitor.service` file under `/etc/systemd/system/`:

```ini
[Unit]
Description=Website Monitor Service
After=network.target

[Service]
WorkingDirectory=/path/to/your/repo
ExecStart=/path/to/your/.venv/bin/python monitor.py
Restart=always
User=youruser

[Install]
WantedBy=multi-user.target
```

Then:

```bash
sudo systemctl daemon-reload
sudo systemctl enable website-monitor
sudo systemctl start website-monitor
sudo journalctl -u website-monitor -f
```

### Docker (optional)

Example `Dockerfile`:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt
CMD ["python", "monitor.py"]
```

Build and run:

```bash
docker build -t website-monitor .
docker run -d --env-file .env --name website-monitor website-monitor
```

---

## Testing

* Unit tests: add tests for the HTTP-checking functions and the email sender.
* Local manual test: point `TARGET_URL` to a test endpoint that returns 500 / times out to confirm `DOWN` behavior.
* Integrations: test with a real SMTP provider and a test recipient.

---

## Contributing

Contributions are welcome. Suggested workflow:

1. Fork the repo
2. Create a branch `feature/my-feature`
3. Implement code and tests
4. Open a Pull Request describing changes

Please follow the existing code style, add unit tests for new logic, and ensure secrets are not committed.

---

## License

Specify your license here (e.g., MIT). Example:

`MIT © <Amir Haghbin>`

---

## Contact

If you have questions or need help, contact: `amirhaghbin7@gmail.com`.

---

### Helpful notes for implementing future features

* **Multiple sites:** use `concurrent.futures.ThreadPoolExecutor` or `asyncio` + `aiohttp` to check many sites without blocking.
* **Ping measurement:** use system `ping` via subprocess or a library like `pythonping` and record round-trip times.
* **UI/UX:** create a small Flask/FastAPI app and a React or simple HTML dashboard. Store recent checks in a small SQLite DB for quick retrieval.
* **Alert deduplication:** implement state tracking (e.g., last known status per site) so you only send an alert on state change.

---

*README generated by the project maintainer tooling.*
