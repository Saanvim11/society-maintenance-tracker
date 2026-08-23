# Society Maintenance Tracker

A full-stack apartment society maintenance portal built for the assignment requirements: resident registration/login, complaints with photos, status history, priority, overdue detection, admin dashboard, notice board, and email notifications.

## Stack
- Frontend: React + Vite
- Backend: Node.js + Express
- Database: SQLite (better-sqlite3)
- Auth: JWT + bcrypt
- Uploads: Multer
- Email: Nodemailer (SMTP / any free SMTP provider)

## Run locally
```bash
npm install
npm run install-all
npm run dev
```
Frontend: http://localhost:5173
API: http://localhost:5000

Admin demo: `admin@society.local` / `Admin@123`

## Environment
Copy `server/.env.example` to `server/.env` and change `JWT_SECRET`. Set SMTP values if you want real emails. `OVERDUE_DAYS=7` controls the overdue threshold.

For a hosted frontend, set `VITE_API_URL` to your deployed API URL before `npm run build`.

## Main API
- POST `/api/auth/register`
- POST `/api/auth/login`
- GET `/api/complaints`
- POST `/api/complaints` (multipart/form-data)
- PATCH `/api/complaints/:id` (admin)
- GET `/api/notices`
- POST `/api/notices` (admin)
- GET `/api/dashboard` (admin)

## Database schema
`users` → residents/admins.
`complaints` → current complaint state and metadata.
`history` → immutable status-change records with timestamp, actor and note.
`notices` → community announcements, including important/pinned notices.

## Deployment
GitHub stores the source code; it does not run the Node API. Deploy the `server` folder to Render/Railway and the `client` folder to Vercel/Netlify. Configure `VITE_API_URL` on the frontend and the SMTP/JWT variables on the backend.
