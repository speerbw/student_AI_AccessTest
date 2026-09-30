# Project Handoff: Local AI Access Portal for Students

## 1. Purpose of This Document

This document is a complete cold-start handoff for the project.

It is written so that another AI assistant, developer, or technical operator can pick up the project with no prior context and continue implementation, debugging, deployment, or extension.

The project is a local-network web application that allows students on separate machines to log in and use a local AI model running on one host machine. It also includes an administrator monitoring dashboard for tracking student usage, activity, errors, and model performance.

---

# 2. Project Summary

The goal is to build a simple, secure, local-area-network web app where:

1. One machine hosts:
   - A local AI model, preferably via Ollama.
   - A web server that proxies chat requests to the local model.
   - User accounts for students and administrators.
   - A monitoring dashboard for administrators.

2. Other student machines on the same local network can:
   - Open a browser.
   - Navigate to the host machine’s local IP address.
   - Log in with a student account.
   - Chat with the local AI model.

3. Administrators can:
   - Log in with an admin account.
   - View a dashboard showing:
     - Total students
     - Active users
     - Message counts
     - Token usage
     - Average response time
     - Errors
     - Per-user usage
     - Recent activity events

The system is intended for classroom or local network use, not public internet deployment.

---

# 3. Target Users

## 3.1 Students

Students are end users who:

- Have their own username and password.
- Log in from their own machine.
- Use the AI chat interface.
- Have their usage tracked for monitoring.

## 3.2 Teachers / Administrators

Administrators are users who:

- Have elevated permissions.
- Can access the monitoring dashboard.
- Can review student usage, activity, and errors.
- Do not necessarily need a different chat experience, but should be able to access the same AI if desired.

---

# 4. Primary Goals

## 4.1 Functional Goals

- Allow local model access over HTTP.
- Provide browser-based chat.
- Support multiple student accounts.
- Support login and logout.
- Keep AI API/model configuration on the server.
- Store per-user conversation history.
- Track usage metrics.
- Provide an admin dashboard.
- Run on a local network without internet dependence for model inference.

## 4.2 Operational Goals

- Be easy to start and stop.
- Be easy to audit.
- Be easy to extend.
- Avoid unnecessary infrastructure.
- Avoid requiring students to install software.
- Keep configuration simple.
- Make local model selection configurable.

---

# 5. Non-Goals / Out of Scope for MVP

The following are not required for the initial version unless explicitly requested later:

- Public internet exposure
- Cloud deployment
- Mobile native apps
- Single sign-on / school identity provider
- Fine-grained permission system beyond student/admin
- Billing or metered pricing
- Complex role-based access control
- Real-time collaborative features
- File upload or image generation
- Plugin system
- Multi-tenant classroom separation
- Advanced analytics dashboards with charts, unless added later

---

# 6. Assumptions

Unless changed by the user, assume the following:

1. The AI model runs locally on the host machine.
2. The host machine and student machines are on the same LAN.
3. Students access the app through a normal web browser.
4. The preferred local model runtime is Ollama.
5. The app should work without requiring a paid API key.
6. The programming language can be Python unless the user requests otherwise.
7. The database can be SQLite for simplicity.
8. The app should not expose the local model endpoint directly to students.
9. The host machine acts as the only authorized proxy to the model.
10. The system should remain LAN-only by default.

---

# 7. Recommended Stack

## 7.1 Language

**Python 3.11+**

Rationale:

- Fast to implement.
- Good HTTP server ecosystem.
- Easy integration with OpenAI-compatible clients.
- Works well with Ollama.
- Low operational complexity.

---

## 7.2 Web Framework

**FastAPI**

Rationale:

- Modern async-friendly Python web framework.
- Built-in validation with Pydantic.
- Easy JSON APIs.
- Good for serving both HTML pages and API endpoints.
- Simple dependency injection for authentication.
- Lightweight enough for local deployment.

---

## 7.3 ASGI Server

**Uvicorn**

Rationale:

- Standard development and simple production server for FastAPI.
- Easy to bind to `0.0.0.0` for LAN access.
- Simple command-line startup.

---

## 7.4 Database

**SQLite**

Rationale:

- No separate database server required.
- Simple file-based persistence.
- Enough for classroom-scale usage.
- Supports multiple concurrent reads reasonably well.
- Avoids operational overhead of PostgreSQL/MySQL.

Use WAL mode for better concurrency:

```sql
PRAGMA journal_mode=WAL;



