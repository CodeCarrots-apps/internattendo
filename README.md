# Intern Attendance Management System (MERN + Supabase)

Full-stack attendance system with:

- React + Tailwind frontend
- Express + Node backend
- Supabase Auth + PostgreSQL

## Features Implemented

- Role-based login (`Admin`, `Intern`) using Supabase Auth
- Check-in rules:
  - Before/at 9:30 -> `Present`
  - After 9:30 -> `Late`
- Check-out rule:
  - Before 5:00 PM -> `Early Leave`
- GPS geofence validation:
  - Outside office radius -> `Leave`
- Work description capture + update
- One attendance record per intern per day (prevents multiple login)
- Admin dashboard cards + sticky table + filters/search
- Export attendance as CSV or Excel (`xlsx`)
- Weekly graph
- Bonus: auto-mark absent (`Leave`) via scheduled backend job

## Project Structure

`backend/` - Express API and attendance logic  
`frontend/` - React web app  
`supabase-schema.sql` - PostgreSQL schema + policies

## Setup

### 1) Supabase

1. Create a Supabase project.
1. Run `supabase-schema.sql` in SQL Editor.
1. Enable Email/Password in Auth.
1. Create users in Auth and insert corresponding rows in `profiles`:

- role must be `Admin` or `Intern`

Example insert:

```sql
insert into public.profiles (id, email, full_name, role)
values ('<auth_user_uuid>', 'admin@company.com', 'Admin User', 'Admin');
```

### 2) Backend

```bash
cd backend
cp .env.example .env
npm install
npm run dev
```

Set values in `backend/.env`:

- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`
- `OFFICE_LAT`
- `OFFICE_LON`
- `OFFICE_RADIUS_METERS`
- `COMPANY_TIMEZONE`

### 3) Frontend

```bash
cd frontend
cp .env.example .env
npm install
npm run dev
```

Set values in `frontend/.env`:

- `VITE_SUPABASE_URL`
- `VITE_SUPABASE_ANON_KEY`
- `VITE_API_BASE_URL` (default `http://localhost:5000/api`)

## Main API Endpoints

- `GET /health`
- `POST /api/public/register`
- `GET /api/auth/me`
- `GET /api/attendance/today`
- `POST /api/attendance/check-in`
- `POST /api/attendance/check-out`
- `PATCH /api/attendance/work`
- `GET /api/admin/stats`
- `GET /api/admin/attendance`
- `GET /api/admin/export?format=csv|xlsx`
- `GET /api/admin/weekly-summary`

## Notes

- Server determines attendance status and time rules.
- Geolocation is captured from browser on check-in.
- Use HTTPS in production for secure geolocation and auth.
- Self registration creates `Intern` role accounts; create `Admin` users from Supabase dashboard/SQL.

## Deploy On Vercel

Deploy this monorepo as two Vercel projects:

- `backend/` as a Node.js serverless API
- `frontend/` as a static Vite app

### 1) Deploy backend project

1. In Vercel, click Add New Project and import this repository.
1. Set Root Directory to `backend`.
1. Keep install/build defaults (`npm install`).
1. Add backend environment variables:

- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`
- `OFFICE_LAT`
- `OFFICE_LON`
- `OFFICE_RADIUS_METERS`
- `FRONTEND_URL` (set this after frontend deploy, then redeploy backend)

1. Deploy and copy the backend URL, for example `https://your-api.vercel.app`.

### 2) Deploy frontend project

1. Add another Vercel project from the same repository.
1. Set Root Directory to `frontend`.
1. Build settings:

- Build Command: `npm run build`
- Output Directory: `dist`

1. Add frontend environment variables:

- `VITE_SUPABASE_URL`
- `VITE_SUPABASE_ANON_KEY`
- `VITE_API_BASE_URL` = `https://your-api.vercel.app/api`

1. Deploy and copy the frontend URL, for example `https://your-app.vercel.app`.

### 3) Final URL wiring

1. Go back to backend project environment variables.
1. Set `FRONTEND_URL` to your deployed frontend URL.
1. Redeploy backend.

### Important

- The backend includes a local `setInterval` absence marker job; this style is not reliable in serverless environments.
- For production on Vercel, move absence marking to a scheduled Vercel Cron endpoint or Supabase scheduled job.
