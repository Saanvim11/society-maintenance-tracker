# System Design — Society Maintenance Tracker

The application follows a simple three-layer architecture: a React/Vite client, an Express REST API, and a relational SQLite database. Authentication uses JWTs and bcrypt, with two roles: resident and admin. This directly supports the required role-based workflow.

## Complaint history model
A complaint stores only its current state (`status`, `priority`, timestamps and resolution time) in `complaints`. Every status transition is additionally inserted into `history` with the complaint ID, new status, optional note, actor ID and timestamp. This gives the resident a chronological audit trail while keeping the current complaint query fast. When a complaint is resolved, `resolved_at` is set and the lifecycle is closed by the UI/API validation.

## Overdue detection
The overdue threshold is configurable through `OVERDUE_DAYS` (7 by default). A complaint is overdue when it is not resolved and its creation timestamp is older than the configured number of days. The admin complaint query sorts overdue records first, while the dashboard exposes the total overdue count. This avoids storing a derived boolean that could become stale.

## Photo handling
Complaint photos are accepted as multipart form data using Multer. Files are limited to 5 MB, renamed to avoid unsafe characters/collisions, and served from the API's `/uploads` route. The complaint stores only the relative photo path, keeping the database small. For production, the same interface can be moved to object storage such as Cloudinary or S3 without changing the complaint model.

## Notification flow
When an admin changes a complaint status, the API creates a history record and attempts to send an email to the resident. Important notices similarly trigger emails to all resident accounts. Nodemailer is used so any SMTP/free-tier provider can be configured through environment variables. If SMTP is not configured, the core application still works and the email attempt is safely skipped.

## Security and deployment
Passwords are hashed with bcrypt and sessions are represented by signed JWTs. Admin-only endpoints check the role from the verified token. Environment variables hold secrets and SMTP credentials. GitHub is used as the source repository; the Node API can be deployed on Render/Railway and the Vite client on Vercel/Netlify. The frontend receives the deployed API URL through `VITE_API_URL`.
