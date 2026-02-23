# FastAPI Beyond CRUD

This is the source code for the [FastAPI Beyond CRUD](https://youtube.com/playlist?list=PLEt8Tae2spYnHy378vMlPH--87cfeh33P&si=rl-08ktaRjcm2aIQ) course. The course focuses on FastAPI development concepts that go beyond the basic CRUD operations.

For more details, visit the project's [website](https://jod35.github.io/fastapi-beyond-crud-docs/site/).

## Table of Contents

1. [Getting Started](#getting-started)
2. [Prerequisites](#prerequisites)
3. [Project Setup](#project-setup)
4. [Running the Application](#running-the-application)
5. [Running Tests](#running-tests)
6. [CI/CD Workflows](#cicd-workflows)
7. [Contributing](#contributing)

## Getting Started
Follow the instructions below to set up and run your FastAPI project.

### Prerequisites
Ensure you have the following installed:

- Python >= 3.10
- PostgreSQL
- Redis

### Project Setup
1. Clone the project repository:
    ```bash
    git clone https://github.com/jod35/fastapi-beyond-CRUD.git
    ```

2. Navigate to the project directory:
    ```bash
    cd fastapi-beyond-CRUD/
    ```

3. Create and activate a virtual environment:
    ```bash
    python3 -m venv env
    source env/bin/activate
    ```

4. Install the required dependencies:
    ```bash
    pip install -r requirements.txt
    ```

5. Set up environment variables by copying the example configuration:
    ```bash
    cp .env.example .env
    ```

6. Run database migrations to initialize the database schema:
    ```bash
    alembic upgrade head
    ```

7. Open a new terminal and ensure your virtual environment is active. Start the Celery worker (Linux/Unix shell):
    ```bash
    sh runworker.sh
    ```

## Running the Application
Start the application:

```bash
fastapi dev src/
```
Alternatively, you can run the application using Docker:
```bash
docker compose up
```

## Running Tests
Run the tests using this command
```bash
pytest
```

## CI/CD Workflows

This project uses two GitHub Actions workflows.

### 1. Conventional Commits Check

**File:** `.github/workflows/conventional-commits.yml`

Runs automatically whenever a Pull Request is opened, edited, or reopened.

- Validates that the PR title follows the [Conventional Commits](https://www.conventionalcommits.org/) specification
- If the title is invalid, the PR is automatically closed and a comment is posted explaining the required format
- An email notification is sent to the configured address

**Valid PR title format:**
```
type(scope): description
```

**Valid types:** `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`

**Examples:**
- `feat: add user authentication`
- `fix(auth): resolve token expiry issue`
- `docs: update README`

---

### 2. Nightly Build

**File:** `.github/workflows/nightly-build.yml`

Runs automatically every night at 12am UTC. Can also be triggered manually from the Actions tab.

- Installs dependencies and runs the full test suite
- If tests pass, builds the Docker image and pushes it to GitHub Container Registry (ghcr.io) with two tags:
  - `ghcr.io/<owner>/<repo>:nightly` — always points to the latest nightly build
  - `ghcr.io/<owner>/<repo>:nightly-<run_number>` — numbered for historical reference
- If tests fail, the image is NOT pushed and an email notification is sent

---

### Required GitHub Secrets

Both workflows require the following secrets to be configured in the repository under **Settings > Secrets and variables > Actions**:

| Secret | Description |
|--------|-------------|
| `SMTP_HOST` | SMTP server hostname (e.g. `smtp.ethereal.email`) |
| `SMTP_PORT` | SMTP port (e.g. `587`) |
| `SMTP_USERNAME` | SMTP username |
| `SMTP_PASSWORD` | SMTP password |
| `SMTP_FROM` | Sender email address |
| `NOTIFICATION_EMAIL` | Recipient email address for notifications |

Email notifications are sent via SMTP and are compatible with [Ethereal Email](https://ethereal.email) (recommended for testing) or [SendGrid](https://sendgrid.com).

---

### Steps Taken to Set Up CI/CD

1. Created an Ethereal Email account at [https://ethereal.email](https://ethereal.email) to use as the SMTP provider for email notifications
2. Added the 6 required GitHub Secrets to the repository (SMTP credentials and notification email)
3. Enabled read/write workflow permissions in the repository under Settings > Actions > General > Workflow permissions
4. Created `.github/workflows/conventional-commits.yml` to validate PR titles, auto-close invalid PRs, and send email notifications
5. Created `.github/workflows/nightly-build.yml` to run tests nightly, push Docker images to GitHub Container Registry on success, and send email notifications on failure
6. Fixed Docker image tag to use lowercase repository name (required by Docker)
7. Committed and pushed all workflow files to the main branch

## Contributing
I welcome contributions to improve the documentation! You can contribute [here](https://github.com/jod35/fastapi-beyond-crud-docs).
