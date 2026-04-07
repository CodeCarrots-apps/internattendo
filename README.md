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
2. Run `supabase-schema.sql` in SQL Editor.
3. Enable Email/Password in Auth.
4. Create users in Auth and insert corresponding rows in `profiles`:
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
- `OFFICE_LAT`, `OFFICE_LON`, `OFFICE_RADIUS_METERS`
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
"# internattendo" 
