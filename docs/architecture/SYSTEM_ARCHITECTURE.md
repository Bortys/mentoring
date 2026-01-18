# Architektura Systemu - Thaliana Mentoring Platform

## 1. Przegląd Architektury

### 1.1 Architektura Wysokopoziomowa

```
┌─────────────────────────────────────────────────────────────┐
│                    CLIENT LAYER                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Web App    │  │  Mobile App  │  │  Admin Panel │      │
│  │   (React)    │  │(React Native)│  │   (React)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ HTTPS/WSS
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    API GATEWAY                               │
│              (Authentication, Rate Limiting)                 │
└─────────────────────────────────────────────────────────────┘
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
┌──────────────────┐ ┌──────────────┐ ┌──────────────┐
│  Application     │ │  Real-time   │ │  Analytics   │
│  Services        │ │  Service     │ │  Service     │
│  (Node.js/NestJS)│ │ (WebSockets) │ │  (Python)    │
└──────────────────┘ └──────────────┘ └──────────────┘
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    DATA LAYER                                │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  PostgreSQL  │  │    Redis     │  │  S3/MinIO    │      │
│  │ (Primary DB) │  │   (Cache)    │  │  (Storage)   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              EXTERNAL SERVICES                               │
│  [Google Calendar] [Email/SendGrid] [Analytics] [AI/LLM]    │
└─────────────────────────────────────────────────────────────┘
```

### 1.2 Architektura Aplikacji (3-Tier)

**Presentation Layer (Frontend)**
- React 18+ z TypeScript
- State management: Zustand lub Jotai (lightweight)
- UI Framework: Tailwind CSS + Shadcn/ui
- Real-time updates: WebSocket client

**Business Logic Layer (Backend)**
- RESTful API + GraphQL (opcjonalnie dla complex queries)
- Microservices architecture (modular monolith w MVP, microservices w fazie scale)
- Domain-Driven Design patterns
- Event-driven communication między serwisami

**Data Layer**
- PostgreSQL: Primary data store
- Redis: Session storage, caching, real-time features
- Object Storage: Dokumenty, zdjęcia profilowe
- Full-text search: PostgreSQL pg_trgm lub ElasticSearch (faza 2)

---

## 2. Moduły Funkcjonalne - Szczegóły

### 2.1 Authentication & Authorization Module

**Funkcjonalności**:
- Rejestracja i login (email/password)
- Social auth: Google, Microsoft (opcjonalnie)
- 2FA (TOTP)
- Password recovery
- Session management
- JWT tokens (access + refresh)

**Tech Stack**:
- Passport.js / NextAuth
- bcrypt dla hashowania
- speakeasy dla 2FA
- Redis dla session storage

**API Endpoints**:
```
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/logout
POST   /api/auth/refresh
POST   /api/auth/forgot-password
POST   /api/auth/reset-password
POST   /api/auth/enable-2fa
POST   /api/auth/verify-2fa
```

---

### 2.2 User Management Module

**Funkcjonalności**:
- CRUD operacje na profilach użytkowników
- Role management (Mentor, Mentee, Admin)
- Profile customization
- Notification preferences
- Privacy settings

**Typy użytkowników**:
```typescript
enum UserRole {
  MENTOR = 'MENTOR',
  MENTEE = 'MENTEE',
  ADMIN = 'ADMIN'
}

interface UserProfile {
  id: string;
  email: string;
  role: UserRole;
  firstName: string;
  lastName: string;
  avatar?: string;
  bio?: string;
  expertise?: string[];      // dla mentorów
  industry?: string[];
  company?: string;
  position?: string;
  linkedIn?: string;
  timezone: string;
  notificationSettings: NotificationSettings;
  createdAt: Date;
  updatedAt: Date;
}
```

**API Endpoints**:
```
GET    /api/users/me
PUT    /api/users/me
GET    /api/users/:id
PUT    /api/users/:id
DELETE /api/users/:id
GET    /api/users/:id/mentoring-relationships
```

---

### 2.3 Mentoring Relationship Module

**Funkcjonalności**:
- Tworzenie par mentor-mentee
- Status relacji (Active, Paused, Completed)
- Metadane relacji (data rozpoczęcia, cele ogólne)
- Historia współpracy
- Archiwizacja zakończonych relacji

**Data Model**:
```typescript
interface MentoringRelationship {
  id: string;
  mentorId: string;
  menteeId: string;
  status: 'ACTIVE' | 'PAUSED' | 'COMPLETED';
  startDate: Date;
  endDate?: Date;
  focusAreas: string[];      // np. ["Fundraising", "Product Strategy"]
  meetingFrequency: string;  // np. "Bi-weekly"
  relationshipGoals?: string;
  privateNotes?: {
    mentorNotes?: string;
    menteeNotes?: string;
  };
  createdAt: Date;
  updatedAt: Date;
}
```

**API Endpoints**:
```
POST   /api/relationships
GET    /api/relationships
GET    /api/relationships/:id
PUT    /api/relationships/:id
DELETE /api/relationships/:id
PUT    /api/relationships/:id/status
```

---

### 2.4 Meetings Management Module

**Funkcjonalności**:
- Tworzenie spotkań z datą i czasem
- Recurring meetings support
- Agenda (przed spotkaniem)
- Notatki (podczas/po spotkaniu)
- Action items z przypisaniem
- Tagowanie tematów
- Przypomnienia automatyczne
- Integracja z Google Calendar

**Data Model**:
```typescript
interface Meeting {
  id: string;
  relationshipId: string;
  title: string;
  scheduledAt: Date;
  duration: number;          // w minutach
  location?: string;         // link Zoom, miejsce fizyczne
  status: 'SCHEDULED' | 'COMPLETED' | 'CANCELLED';
  agenda?: AgendaItem[];
  notes?: string;            // rich text
  tags?: string[];           // ["Product", "Fundraising", "Team"]
  attendees: {
    mentorId: string;
    menteeId: string;
    additionalAttendees?: string[];
  };
  isRecurring?: boolean;
  recurrenceRule?: string;   // RRULE format
  actionItems?: ActionItem[];
  createdBy: string;
  createdAt: Date;
  updatedAt: Date;
}

interface AgendaItem {
  id: string;
  title: string;
  description?: string;
  estimatedTime?: number;    // minuty
  order: number;
}

interface ActionItem {
  id: string;
  meetingId: string;
  title: string;
  description?: string;
  assignedTo: string;        // userId
  dueDate?: Date;
  status: 'TODO' | 'IN_PROGRESS' | 'DONE' | 'BLOCKED';
  priority: 'LOW' | 'MEDIUM' | 'HIGH';
  linkedGoalId?: string;
  createdAt: Date;
  completedAt?: Date;
}
```

**Funkcjonalności Zaawansowane**:
- Automatyczne generowanie follow-up tasks z notatek (AI)
- Email summary po spotkaniu
- Template agend dla różnych typów spotkań
- Wyszukiwanie full-text po notatkach

**API Endpoints**:
```
POST   /api/meetings
GET    /api/meetings?relationshipId=xxx
GET    /api/meetings/:id
PUT    /api/meetings/:id
DELETE /api/meetings/:id
POST   /api/meetings/:id/agenda
PUT    /api/meetings/:id/notes
POST   /api/meetings/:id/action-items
PUT    /api/action-items/:id
GET    /api/action-items?assignedTo=xxx&status=TODO
```

---

### 2.5 Goals & OKR Module

**Funkcjonalności**:
- Tworzenie Objectives z Key Results
- Kategoryzacja celów (Business, Technology, Team, Funding)
- Timeline i milestone tracking
- Progress updates (manualny + automatyczny z action items)
- Powiązanie z meetings i notes
- Archiwum osiągniętych celów
- Reflection notes po zakończeniu celu

**Data Model**:
```typescript
interface Objective {
  id: string;
  relationshipId: string;
  title: string;
  description?: string;
  category: 'BUSINESS' | 'TECHNOLOGY' | 'TEAM' | 'FUNDING' | 'PRODUCT' | 'OTHER';
  status: 'DRAFT' | 'ACTIVE' | 'ACHIEVED' | 'ABANDONED' | 'ARCHIVED';
  startDate: Date;
  targetDate: Date;
  achievedDate?: Date;
  priority: 'LOW' | 'MEDIUM' | 'HIGH' | 'CRITICAL';
  keyResults: KeyResult[];
  linkedMeetings?: string[];  // meetingIds
  reflectionNotes?: string;   // post-completion
  createdBy: string;
  createdAt: Date;
  updatedAt: Date;
}

interface KeyResult {
  id: string;
  objectiveId: string;
  title: string;
  description?: string;
  measureType: 'NUMBER' | 'PERCENTAGE' | 'BOOLEAN' | 'CURRENCY';
  targetValue: number | boolean;
  currentValue: number | boolean;
  unit?: string;             // np. "users", "EUR", "%"
  status: 'NOT_STARTED' | 'ON_TRACK' | 'AT_RISK' | 'ACHIEVED';
  progressUpdates: ProgressUpdate[];
  createdAt: Date;
  updatedAt: Date;
}

interface ProgressUpdate {
  id: string;
  keyResultId: string;
  value: number | boolean;
  note?: string;
  updatedBy: string;
  timestamp: Date;
}
```

**Business Logic**:
- Auto-calculate progress percentage dla Objective na podstawie Key Results
- Notifications gdy cel jest "At Risk" (brak postępu w X dni)
- Quarterly review prompts

**API Endpoints**:
```
POST   /api/objectives
GET    /api/objectives?relationshipId=xxx&status=ACTIVE
GET    /api/objectives/:id
PUT    /api/objectives/:id
DELETE /api/objectives/:id
POST   /api/objectives/:id/key-results
PUT    /api/key-results/:id
POST   /api/key-results/:id/progress
GET    /api/objectives/analytics?relationshipId=xxx
```

---

### 2.6 Knowledge Base Module

**Funkcjonalności**:
- Dodawanie zasobów (linki, dokumenty, notatki)
- Kategoryzacja i tagowanie
- Full-text search
- Wersjonowanie dokumentów
- Sharing permissions (prywatne mentor/mentee, wspólne)
- Rekomendacje (książki, osoby, eventy)

**Data Model**:
```typescript
interface Resource {
  id: string;
  relationshipId: string;
  type: 'LINK' | 'DOCUMENT' | 'NOTE' | 'BOOK' | 'CONTACT' | 'EVENT';
  title: string;
  description?: string;
  content?: string;          // dla notatek
  url?: string;              // dla linków
  fileUrl?: string;          // dla dokumentów
  fileSize?: number;
  mimeType?: string;
  category?: string;         // "Product Development", "Fundraising", etc.
  tags?: string[];
  visibility: 'PRIVATE_MENTOR' | 'PRIVATE_MENTEE' | 'SHARED';
  linkedToMeeting?: string;  // meetingId
  linkedToGoal?: string;     // objectiveId
  addedBy: string;
  createdAt: Date;
  updatedAt: Date;
}

interface Insight {
  id: string;
  relationshipId: string;
  title: string;
  content: string;           // key learning or breakthrough
  source?: string;           // "Meeting 2024-01-15" lub "Book: Zero to One"
  tags?: string[];
  importance: 'LOW' | 'MEDIUM' | 'HIGH';
  dateRecorded: Date;
  createdBy: string;
}
```

**API Endpoints**:
```
POST   /api/knowledge-base/resources
GET    /api/knowledge-base/resources?relationshipId=xxx&category=xxx
GET    /api/knowledge-base/resources/:id
PUT    /api/knowledge-base/resources/:id
DELETE /api/knowledge-base/resources/:id
POST   /api/knowledge-base/insights
GET    /api/knowledge-base/insights?relationshipId=xxx
POST   /api/knowledge-base/search
```

---

### 2.7 Dashboard & Analytics Module

**Funkcjonalności**:
- Personalized dashboard (różny dla mentora i mentee)
- Upcoming meetings i deadlines
- Goal progress visualization
- Activity timeline
- Key metrics i KPIs
- Custom reports (eksport PDF)

**Dashboard Components**:

**Dla Mentora**:
- Lista wszystkich aktywnych mentees
- Nadchodzące spotkania (najbliższe 7 dni)
- Action items wymagające uwagi
- Cele "At Risk"
- Recent activity feed

**Dla Mentee**:
- Progress overview (% osiągniętych celów)
- Nadchodzące spotkania z agendami
- Moje action items (grouped by status)
- Recent resources dodane przez mentora
- Timeline rozwoju firmy

**Analytics Metrics**:
```typescript
interface RelationshipAnalytics {
  relationshipId: string;
  timeRange: { start: Date; end: Date };

  meetings: {
    totalCount: number;
    completedCount: number;
    avgDuration: number;
    frequency: number;        // meetings per month
  };

  goals: {
    totalObjectives: number;
    achievedObjectives: number;
    achievementRate: number;  // %
    avgTimeToComplete: number; // days
    byCategory: Record<string, number>;
  };

  actionItems: {
    totalCreated: number;
    completed: number;
    completionRate: number;   // %
    avgCompletionTime: number; // days
  };

  knowledgeSharing: {
    resourcesAdded: number;
    insightsRecorded: number;
  };

  engagement: {
    lastActivity: Date;
    daysActive: number;
    interactionScore: number; // custom metric
  };
}
```

**API Endpoints**:
```
GET    /api/dashboard/mentor
GET    /api/dashboard/mentee
GET    /api/analytics/relationship/:id
GET    /api/analytics/relationship/:id/export?format=pdf
GET    /api/analytics/overview  // dla admina
```

---

### 2.8 Communication Module

**Funkcjonalności MVP (Phase 1)**:
- Email notifications (meeting reminders, action item due)
- In-app notifications
- Activity feed

**Funkcjonalności Extended (Phase 2)**:
- In-app messaging (mentor <-> mentee)
- File sharing w messages
- Read receipts
- Notification preferences (email vs in-app vs push)

**Data Model**:
```typescript
interface Notification {
  id: string;
  userId: string;
  type: 'MEETING_REMINDER' | 'ACTION_ITEM_DUE' | 'GOAL_AT_RISK' |
        'NEW_RESOURCE' | 'MESSAGE' | 'SYSTEM';
  title: string;
  message: string;
  link?: string;             // deep link do odpowiedniej sekcji
  isRead: boolean;
  createdAt: Date;
}

interface Message {
  id: string;
  relationshipId: string;
  senderId: string;
  recipientId: string;
  content: string;
  attachments?: string[];    // file URLs
  isRead: boolean;
  sentAt: Date;
}
```

**API Endpoints**:
```
GET    /api/notifications?userId=xxx
PUT    /api/notifications/:id/read
PUT    /api/notifications/mark-all-read
POST   /api/messages
GET    /api/messages?relationshipId=xxx
```

---

## 3. Integracje Zewnętrzne

### 3.1 Google Calendar Integration
- OAuth 2.0 flow
- Sync spotkań dwukierunkowy
- Auto-create calendar events przy tworzeniu meetings
- Import wydarzeń z kalendarza

### 3.2 Email Service (SendGrid / AWS SES)
- Transactional emails (welcome, password reset)
- Meeting reminders (24h przed, 1h przed)
- Weekly/monthly summary reports
- Action item due notifications

### 3.3 Cloud Storage (AWS S3 / MinIO)
- Upload dokumentów i attachments
- Profile avatars
- Pre-signed URLs dla secure access
- CDN dla static assets

### 3.4 AI/LLM Integration (Phase 2)
- Auto-summarization notatek ze spotkań
- Extraction action items z notatek
- Sugestie tematów do omówienia na podstawie historii
- Trend analysis w celach i postępach

### 3.5 Analytics & Monitoring
- Sentry dla error tracking
- Plausible/Umami dla privacy-friendly web analytics
- Custom event tracking (feature usage)
- Performance monitoring (Lighthouse CI)

---

## 4. Bezpieczeństwo

### 4.1 Authentication & Authorization
- JWT-based auth z refresh tokens
- Refresh token rotation
- RBAC (Role-Based Access Control)
- Row-level security w PostgreSQL

### 4.2 Data Protection
- Encryption at rest (PostgreSQL transparent encryption)
- Encryption in transit (TLS 1.3)
- End-to-end encryption dla szczególnie wrażliwych notatek (optional, Phase 2)
- Regular automated backups (daily)
- Backup retention: 30 days

### 4.3 Privacy & Compliance
- RODO compliance:
  - Data export functionality
  - Right to be forgotten (account deletion)
  - Consent management
  - Data processing agreements
- Audit logging wszystkich operacji CRUD
- IP logging dla authentication events

### 4.4 Security Best Practices
- Input validation i sanitization
- Parameterized queries (SQL injection prevention)
- CORS properly configured
- Rate limiting na API endpoints
- CSP headers
- Regular security audits
- Dependency scanning (Snyk, Dependabot)

---

## 5. Skalowalność i Performance

### 5.1 Caching Strategy
- Redis dla:
  - Session storage
  - API response caching (short TTL)
  - Real-time data (np. online status)
  - Rate limiting counters

### 5.2 Database Optimization
- Indexing strategy:
  - Primary keys, foreign keys
  - Composite indexes dla common queries
  - Full-text search indexes
- Query optimization i EXPLAIN ANALYZE
- Connection pooling
- Read replicas dla analytics queries (Phase 3)

### 5.3 Horizontal Scaling
- Stateless backend services
- Load balancer (Nginx/HAProxy)
- Container orchestration (Docker + Kubernetes dla production)
- Auto-scaling based on load

### 5.4 Performance Targets
- API response time: p95 < 200ms
- Page load: FCP < 1.5s, TTI < 3.5s
- Real-time updates latency: < 100ms
- Database query time: p95 < 50ms

---

## 6. Deployment Architecture

### 6.1 Environments
- **Development**: Lokalne dev environments + shared dev server
- **Staging**: Production-like environment dla testing
- **Production**: Multi-region (opcjonalnie Phase 3)

### 6.2 CI/CD Pipeline
```
[Git Push]
    ↓
[GitHub Actions]
    ↓
[Run Tests - Unit, Integration, E2E]
    ↓
[Build Docker Images]
    ↓
[Push to Container Registry]
    ↓
[Deploy to Staging]
    ↓
[Smoke Tests]
    ↓
[Manual Approval]
    ↓
[Deploy to Production]
    ↓
[Health Checks]
```

### 6.3 Infrastructure (IaC)
- Terraform dla infrastructure provisioning
- Docker Compose dla local development
- Kubernetes manifests dla production (Phase 2)
- Managed services preference (RDS, ElastiCache, S3)

### 6.4 Monitoring & Observability
- Health check endpoints
- Prometheus + Grafana dla metrics
- Centralized logging (ELK stack lub Loki)
- Distributed tracing (Jaeger)
- Alerting (PagerDuty/Opsgenie dla critical issues)

---

## 7. Tech Stack Summary

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| **Frontend** | React 18 + TypeScript | Industry standard, strong ecosystem |
| | Vite | Fast dev experience, optimized builds |
| | Tailwind CSS + Shadcn/ui | Rapid UI development, consistent design |
| | Zustand | Lightweight state management |
| | React Query | Server state management, caching |
| **Backend** | Node.js + NestJS | TypeScript end-to-end, modular architecture |
| | PostgreSQL 14+ | Robust relational DB, JSON support |
| | Redis | Caching, real-time, sessions |
| | Prisma | Type-safe ORM, migrations |
| **Infrastructure** | Docker | Containerization |
| | AWS/DigitalOcean | Cloud hosting |
| | Nginx | Reverse proxy, static files |
| **DevOps** | GitHub Actions | CI/CD automation |
| | Terraform | Infrastructure as Code |
| **Monitoring** | Sentry | Error tracking |
| | Plausible | Privacy-friendly analytics |

---

**Wersja**: 1.0
**Status**: Draft
**Ostatnia aktualizacja**: 2026-01-18
