# Peer: Student Engagement Monitoring System

Peer is an AI-assisted classroom analytics platform built to help educators identify disengagement early in online learning environments. It combines meeting capture, facial-emotion analysis, engagement scoring, and a teacher dashboard so instructors can quickly spot students who may need support.

## Overview

Peer was designed around a simple goal: make it easier for teachers to understand how students are responding during virtual class sessions. The platform brings together meeting data, engagement signals, and automated follow-up workflows in one place.

At a high level, Peer enables educators to:

- review class-level engagement trends
- identify students with declining participation
- monitor confusion and disengagement signals
- trigger follow-up outreach for students who may be struggling

## Product Highlights

- **Teacher Dashboard**: a modern web interface for reviewing engagement metrics, recent sessions, and at-risk students
- **Student-Level Insights**: per-student engagement summaries with session counts and class history
- **Automated Monitoring Pipeline**: processes meeting artifacts into structured attendance and engagement records
- **Recall.ai Integration**: supports meeting-bot workflows and webhook-driven recording retrieval
- **AI-Driven Follow-Up**: flags students based on engagement thresholds and supports automated outreach workflows
- **Secure Authentication**: uses Supabase Auth for teacher login and protected dashboard access

## Tech Stack

### Frontend

- Next.js
- React
- TypeScript
- Tailwind CSS
- Supabase Auth

### Backend

- FastAPI
- Python
- SQLAlchemy
- Supabase
- PostgreSQL

### AI / Data Processing

- DeepFace
- OpenCV
- Recall.ai

## System Architecture

Peer is organized as a multi-component application:

- `student-engagement-dashboard/`: Next.js dashboard used by educators
- `api/`: FastAPI backend serving analytics, webhook processing, and automation workflows
- `GoogleMeetAPI/`: Recall.ai bot utilities for meeting capture workflows
- `Facial-Emotion-Recognition-using-OpenCV-and-Deepface-main/`: video analysis and engagement signal extraction

## How It Works

1. A meeting is captured through the Recall.ai workflow.
2. Meeting recordings or processed outputs are analyzed for engagement-related signals.
3. Engagement data is written into the application database.
4. The dashboard surfaces class metrics, student summaries, and at-risk learners.
5. Follow-up workflows can identify students who may benefit from instructor outreach.

## Dashboard Experience

The teacher dashboard includes:

- class-wide engagement metrics
- recent session summaries
- at-risk student visibility
- student engagement tracking views
- authenticated access for teacher accounts

## API Surface

The backend currently provides endpoints for:

- health checks
- student and class-session analytics
- aggregate metrics
- Recall.ai webhook handling
- engagement-processing and outreach workflows

## Local Development

### Prerequisites

- Python 3.8+
- Node.js 18+
- a Supabase project
- a Recall.ai API key for meeting-bot functionality

### Backend Environment

Create `api/.env`:

```bash
SUPABASE_URL=your_supabase_project_url
SUPABASE_KEY=your_supabase_key
DATABASE_URL=postgresql://user:password@host:port/postgres
RECALLAI_API_KEY=your_recall_ai_api_key
RECALLAI_REGION=us
```

### Frontend Environment

Create `student-engagement-dashboard/.env.local`:

```bash
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
NEXT_PUBLIC_DEV_SUPABASE_REDIRECT_URL=http://localhost:3000
```

### Install Dependencies

Backend:

```bash
cd api
pip install -r requirements.txt
```

Frontend:

```bash
cd student-engagement-dashboard
npm install
```

### Run The App

Start the API:

```bash
cd api
uvicorn api:app --reload --host 0.0.0.0 --port 8000
```

Start the dashboard:

```bash
cd student-engagement-dashboard
npm run dev
```

The dashboard runs on `http://localhost:3000` and connects to the local FastAPI backend on `http://localhost:8000`.

## Example Workflows

### Process Engagement Data

```bash
cd api
python test_api.py
```

### Run Engagement Follow-Up Workflow

```bash
curl -X POST "http://localhost:8000/mastra/process-engagement"
```

### Create A Meeting Bot

```bash
cd GoogleMeetAPI
python create_and_monitor.py "https://meet.google.com/xxx-xxxx-xxx"
```

## Acknowledgments

- [DeepFace](https://github.com/serengil/deepface)
- [Recall.ai](https://recall.ai)
- [Supabase](https://supabase.com)
- [Next.js](https://nextjs.org)
