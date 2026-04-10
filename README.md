# Peer: Student Engagement Monitoring System

Peer is a teacher-facing dashboard for reviewing student engagement signals from online class sessions. The current repo combines a Supabase-authenticated Next.js dashboard, a FastAPI backend that serves engagement data and Recall.ai webhooks, a standalone Recall bot utility, and a local facial-emotion analysis pipeline.

## What The Product Actually Does Today

- Shows aggregate engagement metrics, recent sessions, and at-risk students in the dashboard
- Lists students with average engagement and session counts from Supabase-backed data
- Supports Supabase Auth for dashboard login/signup, including Google OAuth
- Accepts Recall.ai webhook events, fetches bot details, and downloads meeting recordings
- Processes exported emotion-analysis JSON into `students`, `class_sessions`, and `class_attendances`
- Runs an automated email workflow that identifies students with engagement issues and sends/logs follow-up emails

## Current Scope And Limitations

- The dashboard home page and student tracking page are wired to live backend data
- The email center and meeting-scheduling UI are currently static prototype views, not fully connected workflows
- There is no FastAPI `POST /create-bot` endpoint in the current backend
- Recall bot creation happens through scripts in [`GoogleMeetAPI`](./GoogleMeetAPI), especially `create_and_monitor.py`
- SMTP credentials are currently hardcoded in [`api/mastra_agent.py`](./api/mastra_agent.py), so email configuration is not fully environment-driven yet

## Repo Structure

- `api/`: FastAPI app, Supabase/Postgres access, Recall webhook handling, and email automation
- `student-engagement-dashboard/`: Next.js dashboard with Supabase Auth and UI for analytics, students, and email prototypes
- `GoogleMeetAPI/`: standalone scripts for creating and inspecting Recall.ai bots
- `Facial-Emotion-Recognition-using-OpenCV-and-Deepface-main/`: local emotion analysis code
- `test_data.json`: sample processed emotion-analysis output for ingestion testing

## Architecture

### Frontend

The dashboard is a Next.js app that:

- requires Supabase authentication for protected routes
- proxies `/api/*` requests to `http://localhost:8000/*`
- fetches live data for:
  - `GET /metrics`
  - `GET /class-sessions`
  - `GET /students`
  - `GET /students/at-risk`

### Backend

The FastAPI app in [`api/api.py`](./api/api.py) currently exposes:

- `GET /`
- `GET /health`
- `GET /students`
- `GET /students/at-risk`
- `GET /class-sessions`
- `GET /metrics`
- `POST /webhook/recall/`
- `POST /mastra/process-engagement`
- `GET /mastra/students-with-issues`
- `POST /mastra/send-test-email`

### Data Layer

This project uses both:

- Supabase client access for inserts, email logging, and some student lookups
- direct Postgres access through `DATABASE_URL` for dashboard queries via SQLAlchemy

## Prerequisites

- Python 3.8+
- Node.js 18+
- A Supabase project
- A Recall.ai API key if you want webhook/bot functionality

## Environment Setup

### Backend (`api/.env`)

```bash
SUPABASE_URL=your_supabase_project_url
SUPABASE_KEY=your_supabase_service_or_api_key
DATABASE_URL=postgresql://user:password@host:port/postgres
RECALLAI_API_KEY=your_recall_ai_api_key
RECALLAI_REGION=us
```

Notes:

- `DATABASE_URL` is required because the API queries Postgres directly through SQLAlchemy
- the current email sender does not read SMTP credentials from env; those values are hardcoded in `api/mastra_agent.py`

### Frontend (`student-engagement-dashboard/.env.local`)

```bash
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
NEXT_PUBLIC_DEV_SUPABASE_REDIRECT_URL=http://localhost:3000
```

## Installation

### Backend

```bash
cd api
pip install -r requirements.txt
```

### Frontend

```bash
cd student-engagement-dashboard
npm install
```

`pnpm` also works because the repo includes a `pnpm-lock.yaml`.

## Running The Project

### 1. Start the API

```bash
cd api
uvicorn api:app --reload --host 0.0.0.0 --port 8000
```

### 2. Start the dashboard

```bash
cd student-engagement-dashboard
npm run dev
```

The dashboard runs on `http://localhost:3000` and forwards frontend API calls to the FastAPI server on `http://localhost:8000`.

## Database Expectations

The code expects these tables to exist in Supabase/Postgres:

- `students`
  - used fields: `id`, `first_name`, `last_name`, `email`, `face_id`
- `class_sessions`
  - used fields: `id`, `name`, `start_time`, `duration`
- `class_attendances`
  - used fields: `student_id`, `session_id`, `engaged_percentage`, `disengaged_percentage`, `confused_percentage`, `created_at`
- `emails`
  - used fields: `id`, `student_id`, `session_id`, `email`

`process_json_and_store()` can create placeholder students automatically when it sees new `face_id` values.

## Main Workflows

### Dashboard Analytics

After signing in, the main dashboard displays:

- average engagement
- active student count
- average confusion rate
- average session duration
- recent sessions
- at-risk students

### Student Tracking

The students page shows:

- student name and email
- average engagement
- session count
- aggregated class names

### Recall.ai Recording Flow

Bot creation is currently script-based:

```bash
cd GoogleMeetAPI
python create_and_monitor.py "https://meet.google.com/xxx-xxxx-xxx"
```

Once Recall.ai sends a webhook to `POST /webhook/recall/`, the backend:

1. extracts the Recall bot ID from the webhook payload
2. fetches bot details from Recall.ai
3. finds the mixed-video download URL
4. downloads the recording locally

### JSON Ingestion Flow

To ingest sample processed data:

```bash
cd api
python test_api.py
```

This calls `process_json_and_store("../test_data.json")`, which:

- creates a class session
- creates placeholder students for unseen face IDs
- writes per-student attendance percentages

### Automated Email Processing

To run the engagement email workflow:

```bash
curl -X POST "http://localhost:8000/mastra/process-engagement"
```

Helpful related endpoints:

- `GET /mastra/students-with-issues`
- `POST /mastra/send-test-email?student_id=...&session_id=...`

The current logic flags students when their latest attendance record crosses these thresholds:

- engagement below `50%`
- disengagement above `70%`
- confusion above `60%`

## Testing

### API ingestion test

```bash
cd api
python test_api.py
```

### Mastra/email workflow tests

```bash
cd api
python test_mastra_agent.py
python test_mastra_direct.py
```

### Emotion analysis

```bash
cd Facial-Emotion-Recognition-using-OpenCV-and-Deepface-main
python simplified_analyzer.py
```

## Known Gaps

- README setup previously referenced endpoints that are not implemented; this version reflects the current codebase
- The email center and meeting scheduler in the dashboard are presentational, not end-to-end features yet
- `api/video_downloader.py` writes downloads to a directory named `Facial-Emotion-Recognition-using-OpenCV-and-Deepface`, while the checked-in analyzer folder is `Facial-Emotion-Recognition-using-OpenCV-and-Deepface-main`
- `api/mastra_agent.py` should be moved to environment-based SMTP configuration before production use

## Acknowledgments

- [DeepFace](https://github.com/serengil/deepface) for facial emotion recognition
- [Recall.ai](https://recall.ai) for meeting bot and recording infrastructure
- [Supabase](https://supabase.com) for auth and database services
- [Next.js](https://nextjs.org) for the dashboard frontend
