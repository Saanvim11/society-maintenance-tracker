# Society Maintenance Tracker

A full-stack apartment society maintenance portal built for the assignment requirements: resident registration/login, complaints with photos, status history, priority management, overdue detection, admin dashboard, notice board, and email notifications.

## Features

### Authentication & RBAC

* Resident registration and login
* JWT-based authentication
* Password hashing with bcrypt
* Role-based access control
* Separate resident and administrator workflows
* Protected API endpoints
* Admin-only operations

### Complaint Management

* Create and track maintenance complaints
* Categories: Plumbing, Electrical, Security, Cleaning, Lift, Parking, Other
* Detailed complaint descriptions
* Optional photo attachments
* Priority levels: Low, Medium, High
* Administrative notes
* Complaint timestamps
* Resolution timestamps
* Complete complaint history

### Complaint Lifecycle

```text
Open
  ↓
In Progress
  ↓
Resolved
```

### Overdue Detection

* Automatically identifies unresolved complaints exceeding the configured threshold
* Configurable using `OVERDUE_DAYS`
* Overdue complaints are highlighted in the administrator complaint queue
* Dashboard displays the total number of overdue complaints

### Admin Dashboard

Administrators can monitor:

* Total complaints
* Open complaints
* Resolved complaints
* Overdue complaints
* Complaints by status
* Complaints by category

### Notice Board

#### Administrators can:

* Create society announcements
* Add detailed notice content
* Mark notices as important
* Publish notices to residents

#### Residents can:

* View community announcements
* Identify important notices

### Email Notifications

SMTP-based email integration supports:

* Complaint status update notifications
* Important notice notifications
* Configurable SMTP credentials through environment variables

### Photo Uploads

* Residents can attach images to complaints
* Multipart form-data support
* 5 MB file-size limit
* Sanitized uploaded filenames
* Static image serving through the backend

## System Architecture

```text
                    ┌──────────────────────────┐
                    │        Residents         │
                    │                          │
                    │  Register / Login        │
                    │  Raise Complaints        │
                    │  Upload Photos           │
                    │  Track Complaints        │
                    │  View Notices            │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │      React Frontend      │
                    │                          │
                    │       React + Vite       │
                    │      Responsive UI       │
                    └────────────┬─────────────┘
                                 │
                              REST API
                                 │
                              JWT Auth
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │     Express Backend      │
                    │                          │
                    │ Authentication           │
                    │ Authorization            │
                    │ Complaint Management     │
                    │ Status History           │
                    │ Priority Management      │
                    │ Notice Management        │
                    │ Dashboard Analytics      │
                    │ Photo Uploads            │
                    │ Overdue Detection        │
                    │ Email Notifications      │
                    └──────────┬───────────┬───┘
                               │           │
                               ▼           ▼
                    ┌────────────────┐ ┌───────────────┐
                    │ SQLite Database│ │ SMTP Server   │
                    │                │ │               │
                    │ Users          │ │ Notifications │
                    │ Complaints     │ └───────────────┘
                    │ History        │
                    │ Notices        │
                    └────────────────┘
```

## System Components

### Frontend

The React frontend provides the interface for residents and administrators.

#### Responsibilities

* Authentication
* Role-based navigation
* Complaint creation
* Complaint tracking
* Photo uploads
* Complaint status display
* Complaint history
* Admin dashboard
* Notice board
* Responsive user interface

#### Technologies

* React
* Vite
* JavaScript
* CSS
* Lucide React

### Backend

The Node.js backend provides the REST API and application business logic.

#### Responsibilities

* User registration and login
* JWT authentication
* Role-based authorization
* Complaint creation and retrieval
* Complaint status updates
* Complaint history
* Priority management
* Overdue detection
* Notice management
* Dashboard statistics
* Image uploads
* Email notifications

#### Technologies

* Node.js
* Express.js
* JWT
* bcrypt
* Multer
* Nodemailer
* better-sqlite3

## Database

SQLite is used as the relational database.

### Database Schema

#### Users

```text
users
├── id
├── name
├── email
├── password
└── role
```

Roles:

* `resident`
* `admin`

#### Complaints

```text
complaints
├── id
├── user_id
├── category
├── description
├── photo
├── priority
├── status
├── created_at
├── updated_at
└── resolved_at
```

#### History

```text
history
├── id
├── complaint_id
├── status
├── note
├── actor_id
└── created_at
```

#### Notices

```text
notices
├── id
├── title
├── body
├── important
├── created_at
└── author_id
```

## API Documentation

All protected endpoints require:

```http
Authorization: Bearer <JWT_TOKEN>
```

### Authentication

#### Register

```http
POST /api/auth/register
```

#### Login

```http
POST /api/auth/login
```

### Complaints

#### Get Complaints

```http
GET /api/complaints
```

* Residents receive their own complaints.
* Administrators receive all complaints.

#### Create Complaint

```http
POST /api/complaints
```

Supports `multipart/form-data`:

* `category`
* `description`
* `photo`

#### Update Complaint

```http
PATCH /api/complaints/:id
```

Administrator-only endpoint.

Request:

```json
{
  "status": "In Progress",
  "priority": "High",
  "note": "Maintenance team assigned."
}
```

Supported statuses:

* `Open`
* `In Progress`
* `Resolved`

Supported priorities:

* `Low`
* `Medium`
* `High`

### Notices

#### Get Notices

```http
GET /api/notices
```

#### Create Notice

```http
POST /api/notices
```

Administrator-only endpoint.

### Dashboard

#### Get Dashboard Statistics

```http
GET /api/dashboard
```

Administrator-only endpoint.

Returns:

* Total complaints
* Complaint status statistics
* Complaint category statistics
* Overdue complaint count

## Security

The application implements:

* JWT authentication
* bcrypt password hashing
* Protected API routes
* Role-based authorization
* Admin-only operations
* Authenticated complaint access
* SQLite foreign-key constraints
* File upload size restrictions
* Sanitized uploaded filenames
* Environment-based configuration
* Sensitive environment files excluded from Git

## Environment Variables

### Backend

Create `server/.env`:

```env
PORT=5000
JWT_SECRET=your-secure-secret
OVERDUE_DAYS=7
CLIENT_URL=http://localhost:5173

SMTP_HOST=
SMTP_PORT=587
SMTP_USER=
SMTP_PASS=
MAIL_FROM=
```

### Frontend

Create the frontend environment configuration with:

```env
VITE_API_URL=http://localhost:5000/api
```

For production deployment, replace the local API URL with the deployed backend URL.

## Project Structure

```text
society-maintenance-tracker/
│
├── client/
│   ├── src/
│   │   ├── main.jsx
│   │   └── style.css
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   └── vercel.json
│
├── server/
│   ├── index.js
│   ├── package.json
│   ├── .env.example
│   └── uploads/
│
├── README.md
├── SYSTEM_DESIGN.md
├── render.yaml
├── package.json
└── .gitignore
```

## Application Workflows

### Resident Workflow

```text
Register
   ↓
Login
   ↓
Resident Workspace
   ↓
Raise Complaint
   ↓
Select Category
   ↓
Add Description
   ↓
Attach Photo
   ↓
Submit Complaint
   ↓
Track Complaint
   ↓
View Status History
```

### Administrator Workflow

```text
Login
   ↓
Admin Dashboard
   ↓
View Complaint Statistics
   ↓
Review Complaints
   ↓
Set Priority
   ↓
Update Status
   ↓
Add Administrative Note
   ↓
Resolve Complaint
```

### Notice Workflow

```text
Admin Login
   ↓
Notice Board
   ↓
Create Notice
   ↓
Add Notice Details
   ↓
Mark Important
   ↓
Publish
   ↓
Residents View Notice
   ↓
Email Notification
```

## Local Setup

### Prerequisites

* Node.js 20+
* npm
* Git

### Clone Repository

```bash
git clone https://github.com/Saanvim11/society-maintenance-tracker.git
cd society-maintenance-tracker
```

### Install Dependencies

```bash
npm install
npm run install-all
```

### Start Application

```bash
npm run dev
```

Frontend:

```text
http://localhost:5173
```

Backend:

```text
http://localhost:5000
```

### Run Backend Separately

```bash
cd server
npm install
npm start
```

### Run Frontend Separately

```bash
cd client
npm install
npm run dev
```

## Admin Credentials

The application automatically creates the default administrator when the database is initialized.

```text
Email: admin@society.local
Password: Admin@123
```

> For production use, configure a strong `JWT_SECRET` and secure administrator credentials. The demo password should not be used in a production environment.

## Deployment

The application can be deployed with the frontend and backend as separate services.

### Backend Deployment

Deploy the `server` folder to a Node.js-compatible hosting provider such as Render or Railway.

Configure the following backend environment variables:

```env
PORT=5000
JWT_SECRET=<strong-production-secret>
OVERDUE_DAYS=7
CLIENT_URL=<deployed-frontend-url>
SMTP_HOST=<smtp-host>
SMTP_PORT=587
SMTP_USER=<smtp-username>
SMTP_PASS=<smtp-password>
MAIL_FROM=<sender-email>
```

### Frontend Deployment

Deploy the `client` folder to a frontend hosting provider such as Vercel or Netlify.

Configure:

```env
VITE_API_URL=<deployed-backend-api-url>/api
```

### Deployment Flow

```text
GitHub Repository
       │
       ├──────────────► Backend Hosting
       │                  │
       │                  └── Express API
       │
       └──────────────► Frontend Hosting
                          │
                          └── React + Vite
```

## Design Principles

The application is designed around:

* Clear separation of resident and administrator responsibilities
* Secure authentication and authorization
* Transparent complaint tracking
* Complete complaint history
* Priority-based maintenance handling
* Automatic overdue identification
* Centralized community communication
* Responsive user experience
* RESTful API architecture
* Modular full-stack design
* Maintainable codebase
* Separation of frontend, backend, and persistence layers

## Summary

Society Maintenance Tracker provides a centralized platform for managing apartment society maintenance requests and community communication. Residents can securely submit and monitor complaints, attach supporting photos, and view notices, while administrators can manage complaint priorities and statuses, maintain a complete history, monitor overdue issues through dashboard analytics, and publish important community announcements with email notifications.
