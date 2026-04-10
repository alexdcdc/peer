# Peer: Student Engagement Monitoring System

Peer is an innovative application designed for teachers to monitor student reactions during online Google Meet lessons. By leveraging AI-powered facial emotion recognition and real-time analytics, Peer helps educators preemptively identify and address academic issues before they become severe.

## Overview

Peer integrates with Google Meet through automated bots that record classroom sessions, analyze student facial expressions, and provide actionable insights through a comprehensive dashboard. The system tracks three key engagement states:

- **Engaged**: Students showing positive attention and participation
- **Disengaged**: Students appearing distracted or disinterested
- **Confused**: Students displaying signs of confusion or difficulty

## Architecture

The application consists of several interconnected components:

### Backend Components

- **API Server** (`api/`): FastAPI-based REST API handling webhooks, data processing, and bot management
- **Emotion Analyzer** (`Facial-Emotion-Recognition-using-OpenCV-and-Deepface-main/`): Real-time facial emotion detection using DeepFace and OpenCV
- **Google Meet Integration** (`GoogleMeetAPI/`): Bot creation and monitoring using Recall.ai API
- **Mastra Agent** (`api/mastra_agent.py`): Intelligent agent for automated engagement monitoring and email notifications

### Frontend Components

- **Dashboard** (`student-engagement-dashboard/`): Next.js-based web interface for teachers to view analytics and manage students

### Data Storage

- **Supabase**: PostgreSQL database for storing student data, session recordings, and engagement metrics

## Quick Start

### Prerequisites

- Python 3.8+
- Node.js 18+
- Supabase account
- Recall.ai API key
- Gmail account (for email notifications)

### 1. Clone the Repository

```bash
git clone <repository-url>
cd hackgt-12
```

### 2. Set Up Environment Variables

Create `.env` files in the appropriate directories:

**For the API (`api/.env`):**

```bash
SUPABASE_URL=your_supabase_project_url
SUPABASE_KEY=your_supabase_anon_key
RECALLAI_API_KEY=your_recall_ai_api_key
RECALLAI_REGION=us  # or eu, ap, etc.
GMAIL_USER=your_gmail@gmail.com
GMAIL_APP_PASSWORD=your_gmail_app_password
```

**For the Dashboard (`student-engagement-dashboard/.env.local`):**

```bash
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
```

### 3. Install Backend Dependencies

```bash
cd api
pip install -r requirements.txt
```

### 4. Install Frontend Dependencies

```bash
cd ../student-engagement-dashboard
pnpm install
# or npm install
```

### 5. Set Up Supabase Database

Create the following tables in your Supabase database:

```sql
-- Students table
CREATE TABLE students (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL
);

-- Class sessions table
CREATE TABLE class_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL,
    start_time TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    duration INTEGER,
    meeting_url TEXT
);

-- Class attendances table
CREATE TABLE class_attendances (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    student_id UUID REFERENCES students(id),
    session_id UUID REFERENCES class_sessions(id),
    engaged_percentage DECIMAL(5,2),
    disengaged_percentage DECIMAL(5,2),
    confused_percentage DECIMAL(5,2),
    recorded_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Emails table
CREATE TABLE emails (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    student_id UUID REFERENCES students(id),
    session_id UUID REFERENCES class_sessions(id),
    email TEXT NOT NULL,
    sent_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

### 6. Run the Backend API

```bash
cd api
uvicorn api:app --reload --host 0.0.0.0 --port 8000
```

### 7. Run the Frontend Dashboard

```bash
cd student-engagement-dashboard
pnpm dev
# or npm run dev
```

The dashboard will be available at `http://localhost:3000` and the API at `http://localhost:8000`.

## Usage

### Creating a Monitoring Session

1. **Schedule a Google Meet session** with your students
2. **Create a bot** using the API endpoint:
   ```bash
   curl -X POST "http://localhost:8000/create-bot" \
     -H "Content-Type: application/json" \
     -d '{"meeting_url": "https://meet.google.com/xxx-xxxx-xxx"}'
   ```
3. **Monitor in real-time** through the dashboard as the bot records and analyzes the session

### Viewing Analytics

- Access the dashboard at `http://localhost:3000`
- View overall engagement metrics and trends
- Identify at-risk students requiring intervention
- Review detailed session analytics

### Automated Alerts

The Mastra agent automatically:

- Monitors engagement thresholds
- Sends personalized email notifications to teachers when students show signs of disengagement or confusion
- Prevents duplicate notifications

To manually trigger engagement processing:

```bash
curl -X POST "http://localhost:8000/mastra/process-engagement"
```

## API Endpoints

### Core Endpoints

- `GET /` - API health check
- `GET /health` - Health status
- `GET /students` - List all students with engagement data
- `GET /students/at-risk` - Get students with low engagement
- `GET /class-sessions` - List all class sessions

### Bot Management

- `POST /create-bot` - Create a new monitoring bot for a Google Meet session
- `GET /bots/{bot_id}` - Get bot status and data

### Mastra Agent

- `POST /mastra/process-engagement` - Trigger engagement analysis and email notifications
- `GET /mastra/students-with-issues` - Get students requiring attention

## Development

### Running Tests

```bash
cd api
python test_api.py
python test_mastra_agent.py
```

### Testing Emotion Analysis

```bash
cd Facial-Emotion-Recognition-using-OpenCV-and-Deepface-main
python simplified_analyzer.py
```

## Data Flow

1. **Bot Creation**: Teacher initiates monitoring for a Google Meet URL
2. **Recording**: Recall.ai bot joins the meeting and records video/audio
3. **Processing**: Video is downloaded and processed through emotion analysis
4. **Storage**: Engagement metrics are stored in Supabase database
5. **Analysis**: Mastra agent analyzes patterns and sends alerts
6. **Visualization**: Dashboard displays real-time and historical data

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- [DeepFace](https://github.com/serengil/deepface) for facial emotion recognition
- [Recall.ai](https://recall.ai) for meeting recording capabilities
- [Supabase](https://supabase.com) for backend infrastructure
- [Next.js](https://nextjs.org) for the frontend framework</content>
  <parameter name="filePath">c:\Users\alexd\Documents\GitHub\hackgt-12\README.md
