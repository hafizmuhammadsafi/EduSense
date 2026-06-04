<div align="center">

# 🎓 EduSense AI

### An AI-Powered Classroom Platform for the Modern Era

**Real-Time Attention Monitoring · AI-Generated Insights · Gamification · Deep Analytics**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/Node.js-20.x-green.svg)](https://nodejs.org/)
[![React](https://img.shields.io/badge/React-18-61DAFB.svg?logo=react)](https://reactjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.6-3178C6.svg?logo=typescript)](https://www.typescriptlang.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791.svg?logo=postgresql)](https://www.postgresql.org/)
[![Socket.io](https://img.shields.io/badge/Socket.io-4.x-010101.svg?logo=socket.io)](https://socket.io/)

</div>

---

## 📖 Overview

**EduSense AI** is a full-stack, teacher-first educational platform that bridges the gap between passive online learning and truly interactive classrooms. It leverages cutting-edge AI, real-time WebSocket streaming, and computer vision to give instructors live feedback on student engagement — and empowers students with gamified, AI-assisted learning tools.

Originally designed for universities, K-12 schools, and corporate training environments, EduSense integrates:
- 🎥 **Live video broadcasting** via WebRTC (PeerJS)
- 🧠 **AI-powered transcription & lecture summaries** via OpenAI Whisper + GPT-4o-mini
- 👁️ **Real-time attention & emotion analytics** via client-side Face-API
- 🏆 **Gamification** with XP, levels, badges, and streaks
- 🔒 **Plagiarism detection** for assignments and quizzes
- 📊 **Deep analytics & PDF reporting** for teachers and admins

---

## ✨ Feature Showcase

### 🔐 1. Role-Based Authentication & Access Control
- Three distinct user roles: **Teacher**, **Student**, and **Admin** — each with their own dedicated dashboard and navigation.
- Session-based authentication using `express-session` + `connect-pg-simple` for persistent, secure logins.
- `passport-local` strategy for credential validation.
- Middleware-enforced route protection — students cannot access teacher routes and vice versa.

---

### 👨‍💼 2. Admin Control Center
- **Platform-wide analytics**: total users, active sessions, average attention scores, and top-performing students on a live leaderboard.
- **User Management**: Create, edit, and delete student and teacher accounts from a centralized panel.
- **Class Oversight**: View all classes created across every teacher, with enrollment details and student lists.
- **Broadcast Announcements**: Send real-time notifications to all users, students only, teachers only, or a specific class.
- **Activity Feed**: A live log of recent platform-wide events.

---

### 🎬 3. Live Class Broadcasting (WebRTC + Socket.io)
- **Camera & Screen Sharing**: Teachers broadcast high-quality video or share their screen directly in the browser using **PeerJS (WebRTC)**.
- **Live Server-Side Audio Streaming**: Microphone audio is sliced into binary chunks and streamed continuously to a high-performance backend WebSocket gateway.
- **Real-Time Student Attention Grid**: The teacher dashboard shows a live grid of every connected student with:
  - 🔵 Color-coded **attention rings** (green → yellow → red based on score)
  - 😊 **Emotion badges** (happy, focused, neutral, bored, distracted)
  - ⚠️ **Boredom alerts** for students falling below the threshold
  - 📈 **Class attention wave chart** updated every 3 seconds
- **Session End Flow**: When a teacher ends the session, all connected students instantly see a custom "Session Ended" summary screen.

**WebSocket Events (Socket.io):**
| Event | Description |
|---|---|
| `teacher:join-session` | Teacher joins a session room |
| `student:join-session` | Student joins and starts streaming |
| `student:attention` | Student sends `{sessionId, score, emotion}` every 3s |
| `attention:update` | Server relays data to the teacher dashboard |
| `student:joined` / `student:left` | Connection/disconnection events |
| `session:ended` | Teacher broadcasts end signal to all students |
| `session:current-students` | Teacher receives current student list on join |

---

### 🤖 4. Polyglot AI Pipeline (Transcription & Summaries)
A multi-language microservice architecture powers the AI features:
- **Live Whisper AI Transcriptions**: Audio chunks are streamed in real-time from the Rust WebSocket gateway to a **Python FastAPI** microservice running **OpenAI Whisper**, capturing everything the teacher says.
- **Auto-Generated Lecture Summaries**: At session end, the full transcript is sent to **GPT-4o-mini** to generate a clean, concise summary of the entire lecture.
- **Class-Wide Summary Broadcasting**: The AI-generated summary is immediately displayed on students' ending screen and also pushed as a persistent notification to all enrolled students.

---

### 👁️ 5. Real-Time Attention & Emotion Analytics (AI Face Monitor)
- **Client-Side Face Monitoring**: The reusable `FaceMonitor` component activates the student's webcam via `getUserMedia` and runs **Face-API** (facial sentiment analysis) entirely on-device for privacy.
- **Picture-in-Picture Preview**: A minimizable PiP window shows a live video preview with an "AI Active" badge, face-detection overlay, and real-time emotion/score readout.
- **Camera Consent Dialog**: Users explicitly opt-in via a consent gate before the camera activates.
- **Graceful Fallback**: If camera access is denied or unavailable, the system automatically switches to simulated scoring — sessions continue uninterrupted.
- **Live Engagement Scoring**: Calculates a real-time `Attention Score` (0–100%) and streams it to the teacher's dashboard via Socket.io every 3 seconds.
- **Smart Automated Attendance**: Algorithmically calculates each student's `averageFocus` across the session. Students maintaining **≥60% focus** are automatically marked **Present**.
- **Used In**: Live sessions **and** the book reader.

---

### 🏆 6. Gamification Engine
Designed to build consistent learning habits through positive reinforcement:
- **XP & Leveling System**: Students earn Experience Points (XP) for:
  - Maintaining high focus during live sessions
  - Joining classes
  - Completing assignments and quizzes
- **Dynamic Streaks**: Daily activity streaks are tracked and displayed prominently to encourage consistent participation.
- **Achievement Badges**: The backend automatically awards badges (e.g., *"7-Day Streak"*, *"Top Performer"*) and sends pop-up system notifications upon unlock.
- **Leaderboard**: Per-class leaderboard showing XP rankings among enrolled students.
- **Student Profile**: A dedicated profile page showcasing earned badges, XP history, activity charts, stats, and learning style.

---

### 📝 7. Assessment System & Anti-Cheat Quizzes
- **AI Quiz Generation**: Teachers can automatically generate custom quizzes by providing a topic — powered by **OpenAI**.
- **Manual Quiz Builder**: Fully manual multiple-choice question builder with optional per-question timers.
- **Anti-Cheat Mechanisms**:
  - ⏱️ Enforced time limits per quiz and per question
  - 🚫 **Tab-switch detection** — flags attempts when the student leaves the quiz window
  - 🖥️ **Fullscreen enforcement** — quiz exits on fullscreen exit
  - 🔒 **Copy/paste blocking** to prevent answer sharing
- **XP Rewards**: Students earn XP and potentially badges upon quiz completion.
- **Results Dashboard**: Teachers view per-student quiz results, scores, and attempt history.

---

### 📚 8. Assignment Management & Submissions
- **Assignment Creation**: Teachers create assignments linked to specific classes with due dates and descriptions.
- **Student Submission Portal**: Students upload **PDF or Word (.docx)** documents as assignment submissions directly from their dashboard using a drag-and-drop upload zone.
- **Automatic Text Extraction**: Text is automatically extracted from uploaded files (using `pdf-parse` and `mammoth`) for plagiarism analysis.
- **Teacher Grading Interface**: Teachers view, download, and grade submissions at `/teacher/submissions` using a grading dialog (0–100 score + written feedback).
- **Inline Feedback**: Students see their grade and teacher feedback displayed inline on their assignment card.

---

### 🔍 9. Plagiarism Detection Engine
A two-tier system for academic integrity:
1. **Quiz Plagiarism** (`/teacher/plagiarism`): Compares answer patterns across students — flags matching wrong answers as suspicious.
2. **Document Plagiarism** (`/teacher/submissions`): Uses a multi-algorithm approach:
   - **4-gram Jaccard Similarity** on extracted text
   - **Exact sentence matching**
   - **12-word sliding window** fallback algorithm
   - **Side-by-side highlighted comparison**: Copied passages are highlighted in context for each student pair
3. **Notification System**: Teachers can "Flag & Notify All Students" or notify individual pairs. Plagiarism alert notifications include: similarity %, copied passages, risk level, and who they matched with.
4. **Security**: Server validates recipient IDs against actual assignment submissions before sending any notification.

---

### 📖 10. Book Library & Smart Reader
- **Centralized PDF Library**: Teachers upload and share reference books, materials, and PDFs (up to **50MB**, drag-and-drop supported).
- **Class Assignments**: Books are assigned to specific classes with optional timers and due dates.
- **In-Browser PDF Reader**: Students read real PDFs rendered via `react-pdf` with zoom controls, page navigation, and completion tracking.
- **Smart Reading Sessions**: The system tracks `time_spent_seconds` and `bored_count` per reading session, integrated with the Face Monitor.
- **Fallback Reader**: Books without PDFs fall back to a sample text reader.
- **AI Doubt Solver**: A context-aware chat interface embedded in the reader where students ask questions about what they're reading. Uses **OpenAI with streaming SSE responses**. Sends the book title, subject, and current page text as context for relevant answers. Supports chat history and Zod-validated input.

---

### 🧑‍🏫 11. AI Teaching Advisor
- A full-page AI chat interface for teachers at `/teacher/ai-advisor`.
- The AI has access to **real class data**: student XP, attention scores, session counts, boredom events, and learning styles.
- Provides **data-driven advice** on improving teaching effectiveness, identifying at-risk students, and boosting engagement.
- Features **quick-prompt cards**, streaming SSE responses, and graceful disconnect handling.
- Student data uses first names only for privacy.

---

### 📊 12. Analytics & Reporting
- **Teacher Insights Dashboard**: Visualizes peaks and valleys in classroom attention alongside session transcripts using **Recharts**.
- **Weekly Attention Trends**: Line/area charts showing attention patterns over time.
- **Learning Style Radar Charts**: Visualizes the distribution of learning styles across a class.
- **Dropout Risk Flags**: Highlights at-risk students based on attention and engagement metrics.
- **Downloadable PDF Reports**: The backend dynamically builds professionally formatted PDF reports of classroom attention, attendance metrics, and student progress using **PDFKit**.
- **Parent Reports**: Teachers generate per-student per-class progress reports including attendance, attention scores, XP/level/streak, quiz performance, badges, and reading completion. Available at `/teacher/reports`.

---

### 💬 13. Communication & Notifications
- **Global Real-Time Notification System**: A notification bell with type-coded alerts for:
  - Class starts
  - Badge unlocks
  - Lecture summaries
  - Plagiarism alerts
  - Admin announcements
- **Direct Messaging**: Built-in 1-to-1 direct messaging between classmates within the same class (**safety-enforced** — backend verifies classmate relationship).
  - Two-panel chat UI at `/student/messages`
  - Conversation list + chat bubbles
  - New message dialog with classmate search
  - Unread message badges in the sidebar
  - 10-second polling for new messages
- **Join Codes**: Secure alphanumeric class join codes for simple student onboarding.

---

## 🏗️ Architecture

EduSense uses a **polyglot microservice-inspired architecture** for performance and scalability:

```
┌──────────────────────────────────────────────────────────┐
│                     Browser (Client)                      │
│  React 18 + TypeScript + Vite + TailwindCSS + shadcn/ui  │
│  Face-API (CV) · PeerJS (WebRTC) · Socket.io-client      │
└────────────────────────┬─────────────────────────────────┘
                         │ HTTP / WebSocket / WebRTC
┌────────────────────────▼─────────────────────────────────┐
│               Node.js / Express.js Backend                │
│    TypeScript · Drizzle ORM · Socket.io · Passport.js    │
│    Session Auth · REST API · SSE Streaming               │
└──────────┬───────────────────────┬───────────────────────┘
           │                       │
┌──────────▼──────────┐   ┌───────▼──────────────────────┐
│  PostgreSQL (pg 16) │   │    Python FastAPI Service     │
│  Drizzle ORM        │   │  OpenAI Whisper (ASR)         │
│  All relational data│   │  Transcription & Summaries    │
└─────────────────────┘   └──────────────────────────────┘
```

### Tech Stack Summary

| Layer | Technology |
|---|---|
| **Frontend** | React 18, TypeScript, Vite, TailwindCSS, shadcn/ui |
| **State Management** | TanStack Query (React Query v5) |
| **Charts & Visualization** | Recharts, Framer Motion |
| **Backend** | Express.js 5, TypeScript, Node.js 20 |
| **Real-Time** | Socket.io 4.x (WebSockets) |
| **Video/Audio** | PeerJS (WebRTC), Web Audio API |
| **AI / ML** | OpenAI GPT-4o-mini, OpenAI Whisper, Face-API |
| **Computer Vision** | Face-API (client-side), MediaPipe |
| **Database** | PostgreSQL 16, Drizzle ORM |
| **Auth** | Passport.js (Local Strategy), express-session |
| **PDF** | react-pdf, pdf-parse, PDFKit, pdfjs-dist |
| **File Uploads** | Multer (50MB max) |
| **Containerization** | Docker (multi-stage build with Rust/WASM) |
| **Security** | Helmet.js, express-rate-limit |

---

## 🗄️ Database Schema

| Table | Purpose |
|---|---|
| `users` | All users with roles, XP, level, streak, learning style |
| `classes` | Teacher classes with join codes |
| `class_enrollments` | Student-class relationships |
| `sessions_log` | Live session records with attention summaries |
| `attention_scores` | Per-student per-session attention + emotion data |
| `books` | Book library (PDF metadata & references) |
| `assignments` | Book assignments to classes with due dates |
| `reading_sessions` | Student reading progress & boredom counts |
| `notifications` | User notifications (all types) |
| `badges` | Earned achievement badges |
| `quizzes` | Quiz definitions with questions |
| `quiz_attempts` | Student quiz attempt results |
| `direct_messages` | Classmate-to-classmate messages (safety scoped) |

---

## 🗂️ Project Structure

```
EduSense-main/
├── client/src/
│   ├── pages/
│   │   ├── login.tsx                  # Auth with demo shortcuts
│   │   ├── teacher/
│   │   │   ├── dashboard.tsx          # Teacher home (stats + charts)
│   │   │   ├── classes.tsx            # Class management (CRUD)
│   │   │   ├── class-detail.tsx       # Tabs: students, sessions, assignments, quizzes, leaderboard
│   │   │   ├── session.tsx            # Live session with real-time attention grid
│   │   │   ├── analytics.tsx          # Analytics dashboard (Recharts)
│   │   │   ├── quizzes.tsx            # Quiz management & results
│   │   │   ├── books.tsx              # Book library management
│   │   │   ├── submissions.tsx        # Assignment submissions & grading
│   │   │   ├── plagiarism.tsx         # Plagiarism detection dashboard
│   │   │   ├── reports.tsx            # Parent/student progress reports
│   │   │   └── ai-advisor.tsx         # AI Teaching Advisor chat
│   │   ├── student/
│   │   │   ├── dashboard.tsx          # Student home (XP + assignments + quizzes + sessions)
│   │   │   ├── session.tsx            # Student live session (streams attention via WebSocket)
│   │   │   ├── profile.tsx            # Profile with badges, stats, activity chart
│   │   │   ├── quiz.tsx               # Quiz taking (timer + anti-cheat)
│   │   │   ├── reader.tsx             # Book reader (enrichment + AI Doubt Solver)
│   │   │   └── messages.tsx           # Direct messaging
│   │   └── admin/
│   │       ├── dashboard.tsx          # Platform analytics + announcements
│   │       ├── users.tsx              # User management (CRUD)
│   │       └── classes.tsx            # Class oversight (all teachers)
│   └── components/
│       ├── app-sidebar.tsx            # Role-aware navigation sidebar
│       ├── face-monitor.tsx           # Webcam AI monitor (PiP + consent dialog)
│       ├── pdf-reader.tsx             # PDF renderer (react-pdf)
│       ├── notification-bell.tsx      # Real-time notification popover
│       └── stat-card.tsx              # Reusable statistics card
├── server/
│   ├── index.ts                       # App entry point
│   ├── routes.ts                      # All API endpoints (~78k — fully documented)
│   ├── storage.ts                     # Database storage layer + seed data
│   ├── socket.ts                      # Socket.io server (session auth sharing)
│   └── agent.ts                       # AI agent utilities
├── shared/
│   └── schema.ts                      # All Drizzle ORM schemas + TypeScript types
├── migrations/                        # Drizzle DB migrations
├── uploads/                           # Uploaded PDF files (multer)
├── Dockerfile                         # Multi-stage Docker build
├── start-all.sh                       # Local stack startup script
└── drizzle.config.ts                  # Drizzle ORM configuration
```

---

## 🚀 Getting Started

### Prerequisites

- **Node.js** v20.x or higher
- **npm** v10.x or higher
- **PostgreSQL** 16 (via Docker or local install)
- **OpenAI API Key** (for AI features)

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/EduSense.git
cd EduSense
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Set Up PostgreSQL (via Docker)

```bash
# First-time setup — create the container:
docker run -d \
  --name edusense-postgres \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=edusense \
  -p 5432:5432 \
  postgres:16

# On subsequent runs, just start the existing container:
docker start edusense-postgres
```

### 4. Configure Environment Variables

Create a `.env` file in the project root:

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/edusense
SESSION_SECRET=your-secure-session-secret
OPENAI_API_KEY=your-openai-api-key
NODE_ENV=development
PORT=5001
```

### 5. Push Database Schema

```bash
npm run db:push
```

### 6. Start the Application

**Option A — Quick Start (Recommended):**
```bash
bash start-all.sh
```

**Option B — Manual:**
```bash
npm run dev
```

The app will be available at **http://localhost:5001**

---

## 🐳 Docker Deployment

The project includes a multi-stage Dockerfile that handles Node.js, Rust (for WASM compilation), and production serving:

```bash
# Build the Docker image
docker build -t edusense-ai .

# Run the container (provide your environment variables)
docker run -d \
  -p 5000:5000 \
  -e DATABASE_URL=your-database-url \
  -e SESSION_SECRET=your-secret \
  -e OPENAI_API_KEY=your-openai-key \
  edusense-ai
```

---

## 🧪 Demo Accounts

Use these pre-seeded accounts to explore the platform:

| Role | Username | Password | Access |
|------|----------|----------|--------|
| 👩‍🏫 Teacher | `teacher1` | `password123` | Teacher dashboard, live sessions, analytics |
| 🧑‍🎓 Student | `student1` | `password123` | Student dashboard, live sessions, book reader |
| 🛡️ Admin | `admin` | `password123` | Full platform control, user management |

---

## 🔌 Key API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/login` | Authenticate user |
| `POST` | `/api/logout` | Logout current session |
| `GET` | `/api/user` | Get current authenticated user |
| `GET` | `/api/classes` | List classes for current user |
| `POST` | `/api/classes` | Create a new class |
| `POST` | `/api/classes/:id/join` | Join a class via join code |
| `POST` | `/api/sessions` | Start a live session |
| `PATCH` | `/api/sessions/:id/end` | End a live session |
| `GET` | `/api/sessions/:id/attention` | Get attention data for a session |
| `POST` | `/api/books` | Upload a book to the library |
| `GET` | `/api/books/:id/pdf` | Serve a book's PDF file |
| `POST` | `/api/quizzes` | Create a quiz |
| `POST` | `/api/quizzes/:id/attempt` | Submit a quiz attempt |
| `POST` | `/api/assignments/:id/submit` | Upload an assignment submission |
| `POST` | `/api/admin/broadcast` | Broadcast notification to users |
| `GET` | `/api/teacher/reports/:classId/:studentId` | Generate student PDF report |
| `POST` | `/api/ai/doubt-solver` | Stream AI answer for student question |
| `POST` | `/api/ai/advisor` | Stream AI teaching advice |
| `GET` | `/api/notifications` | Get user notifications |
| `GET` | `/api/messages/:userId` | Get conversation with a user |

---

## 🎨 Design System

| Token | Value | Usage |
|---|---|---|
| Primary Brand | `#1E3A5F` | Navigation, headers |
| Primary Action | `#2563EB` | Buttons, CTAs |
| Secondary | `#0EA5E9` | Accents, highlights |
| Surface Light | `#EFF6FF` | Card backgrounds |
| Success | `#16A34A` | Attendance, positive states |
| Warning | `#D97706` | Alerts, medium attention |
| Danger | `#DC2626` | Errors, low attention, plagiarism |
| Purple | `#7C3AED` | Gamification, badges, XP |

---

## 🔒 Security Features

- **Helmet.js**: Sets security-related HTTP headers automatically
- **express-rate-limit**: API rate limiting to prevent abuse
- **Session-based auth**: Secure, server-side sessions with PostgreSQL persistence
- **Role-based middleware**: Route-level access control for all protected endpoints
- **Classmate-only messaging**: Backend enforces social safety by verifying enrollment before allowing messages
- **Plagiarism notification security**: Server validates recipient IDs against actual submissions before any notification is sent
- **Input validation**: Zod schemas validate all API inputs server-side

---

## 📋 Available Scripts

| Script | Description |
|---|---|
| `npm run dev` | Start development server with hot reload |
| `npm run build` | Build production bundle (includes WASM compilation) |
| `npm start` | Run production build |
| `npm run db:push` | Push Drizzle schema to the database |
| `npm run check` | TypeScript type checking |
| `bash start-all.sh` | Start full local stack |

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -m 'feat: add your feature'`
4. Push to your branch: `git push origin feature/your-feature-name`
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 👥 Team

Built as a semester project exploring the intersection of **AI, Computer Vision, EdTech, and Distributed Systems**.

> *"EduSense AI isn't just a classroom tool — it's a window into the learning experience."*

---

<div align="center">

Made with ❤️ for the future of education

**[⭐ Star this repo](https://github.com/your-username/EduSense)** · **[🐛 Report a Bug](https://github.com/your-username/EduSense/issues)** · **[💡 Request a Feature](https://github.com/your-username/EduSense/issues)**

</div>
