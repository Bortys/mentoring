# Model Danych - Thaliana Mentoring Platform

## 1. Diagram ERD (Entity Relationship Diagram)

```
┌─────────────────────┐
│       User          │
├─────────────────────┤
│ PK id              │◄───┐
│    email           │    │
│    passwordHash    │    │
│    role            │    │
│    firstName       │    │
│    lastName        │    │
│    avatar          │    │
│    bio             │    │
│    expertise[]     │    │
│    timezone        │    │
│    createdAt       │    │
└─────────────────────┘    │
         │                 │
         │ 1               │ 1
         │                 │
         │ N               │ N
┌────────┴─────────────────┴────────────┐
│    MentoringRelationship              │
├───────────────────────────────────────┤
│ PK id                                 │
│ FK mentorId        ──────────────────►│
│ FK menteeId        ──────────────────►│
│    status                             │
│    startDate                          │
│    endDate                            │
│    focusAreas[]                       │
│    meetingFrequency                   │
│    relationshipGoals                  │
│    createdAt                          │
└───────────────────────────────────────┘
         │                 △
         │ 1               │
         │                 │
         │ N               │
         │                 │
┌────────┴─────────┐       │
│     Meeting      │       │
├──────────────────┤       │
│ PK id           │       │
│ FK relationshipId├───────┘
│    title         │
│    scheduledAt   │
│    duration      │
│    location      │
│    status        │
│    notes         │
│    tags[]        │
│    createdAt     │
└──────────────────┘
         │ 1
         │
         │ N
┌────────┴──────────┐
│   ActionItem      │
├───────────────────┤
│ PK id            │
│ FK meetingId     │◄──┐
│ FK assignedTo    │   │
│ FK linkedGoalId  │   │
│    title         │   │
│    description   │   │
│    dueDate       │   │
│    status        │   │
│    priority      │   │
│    createdAt     │   │
│    completedAt   │   │
└───────────────────┘   │
                        │
┌───────────────────────┴────┐
│       Objective            │
├────────────────────────────┤
│ PK id                     │
│ FK relationshipId         │
│    title                  │
│    description            │
│    category               │
│    status                 │
│    startDate              │
│    targetDate             │
│    achievedDate           │
│    priority               │
│    reflectionNotes        │
│    createdAt              │
└────────────────────────────┘
         │ 1
         │
         │ N
┌────────┴─────────────┐
│     KeyResult        │
├──────────────────────┤
│ PK id               │
│ FK objectiveId      │
│    title            │
│    measureType      │
│    targetValue      │
│    currentValue     │
│    unit             │
│    status           │
│    createdAt        │
└──────────────────────┘
         │ 1
         │
         │ N
┌────────┴───────────────┐
│   ProgressUpdate       │
├────────────────────────┤
│ PK id                 │
│ FK keyResultId        │
│    value              │
│    note               │
│    updatedBy          │
│    timestamp          │
└────────────────────────┘

┌─────────────────────────┐
│      Resource           │
├─────────────────────────┤
│ PK id                  │
│ FK relationshipId      │
│ FK linkedToMeeting     │
│ FK linkedToGoal        │
│    type                │
│    title               │
│    description         │
│    content             │
│    url                 │
│    fileUrl             │
│    category            │
│    tags[]              │
│    visibility          │
│    addedBy             │
│    createdAt           │
└─────────────────────────┘

┌──────────────────────────┐
│     Notification         │
├──────────────────────────┤
│ PK id                   │
│ FK userId               │
│    type                 │
│    title                │
│    message              │
│    link                 │
│    isRead               │
│    createdAt            │
└──────────────────────────┘
```

---

## 2. Prisma Schema Definition

```prisma
// schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// ============================================
// USER & AUTHENTICATION
// ============================================

enum UserRole {
  MENTOR
  MENTEE
  ADMIN
}

model User {
  id                String    @id @default(uuid())
  email             String    @unique
  passwordHash      String
  role              UserRole

  // Profile
  firstName         String
  lastName          String
  avatar            String?
  bio               String?   @db.Text
  expertise         String[]  // Array of expertise areas
  industry          String[]
  company           String?
  position          String?
  linkedIn          String?
  website           String?

  // Settings
  timezone          String    @default("Europe/Warsaw")
  language          String    @default("pl")
  emailNotifications Boolean  @default(true)

  // 2FA
  twoFactorEnabled  Boolean   @default(false)
  twoFactorSecret   String?

  // Timestamps
  createdAt         DateTime  @default(now())
  updatedAt         DateTime  @updatedAt
  lastLoginAt       DateTime?

  // Relations
  mentorRelationships   MentoringRelationship[] @relation("MentorRelationships")
  menteeRelationships   MentoringRelationship[] @relation("MenteeRelationships")
  assignedActionItems   ActionItem[]            @relation("AssignedActionItems")
  createdObjectives     Objective[]             @relation("CreatedObjectives")
  notifications         Notification[]
  sentMessages          Message[]               @relation("SentMessages")
  receivedMessages      Message[]               @relation("ReceivedMessages")
  auditLogs             AuditLog[]

  @@index([email])
  @@index([role])
  @@map("users")
}

// ============================================
// MENTORING RELATIONSHIP
// ============================================

enum RelationshipStatus {
  ACTIVE
  PAUSED
  COMPLETED
  ARCHIVED
}

model MentoringRelationship {
  id                  String              @id @default(uuid())

  // Participants
  mentorId            String
  mentor              User                @relation("MentorRelationships", fields: [mentorId], references: [id], onDelete: Cascade)

  menteeId            String
  mentee              User                @relation("MenteeRelationships", fields: [menteeId], references: [id], onDelete: Cascade)

  // Relationship metadata
  status              RelationshipStatus  @default(ACTIVE)
  startDate           DateTime            @default(now())
  endDate             DateTime?

  // Configuration
  focusAreas          String[]            // ["Fundraising", "Product Strategy"]
  meetingFrequency    String?             // "Bi-weekly", "Monthly"
  relationshipGoals   String?             @db.Text

  // Private notes
  mentorPrivateNotes  String?             @db.Text
  menteePrivateNotes  String?             @db.Text

  // Timestamps
  createdAt           DateTime            @default(now())
  updatedAt           DateTime            @updatedAt

  // Relations
  meetings            Meeting[]
  objectives          Objective[]
  resources           Resource[]
  messages            Message[]

  @@unique([mentorId, menteeId])
  @@index([status])
  @@index([mentorId])
  @@index([menteeId])
  @@map("mentoring_relationships")
}

// ============================================
// MEETINGS
// ============================================

enum MeetingStatus {
  SCHEDULED
  COMPLETED
  CANCELLED
  RESCHEDULED
}

model Meeting {
  id              String              @id @default(uuid())
  relationshipId  String
  relationship    MentoringRelationship @relation(fields: [relationshipId], references: [id], onDelete: Cascade)

  // Meeting details
  title           String
  description     String?             @db.Text
  scheduledAt     DateTime
  duration        Int                 // in minutes
  location        String?             // Zoom link, physical address
  status          MeetingStatus       @default(SCHEDULED)

  // Meeting content
  agenda          Json?               // AgendaItem[]
  notes           String?             @db.Text
  summary         String?             @db.Text  // AI-generated summary
  tags            String[]

  // Recurrence
  isRecurring     Boolean             @default(false)
  recurrenceRule  String?             // RRULE format
  parentMeetingId String?             // for recurring meetings

  // Metadata
  createdBy       String
  cancelReason    String?

  // Timestamps
  createdAt       DateTime            @default(now())
  updatedAt       DateTime            @updatedAt
  completedAt     DateTime?

  // Relations
  actionItems     ActionItem[]

  @@index([relationshipId])
  @@index([scheduledAt])
  @@index([status])
  @@map("meetings")
}

// ============================================
// ACTION ITEMS
// ============================================

enum ActionItemStatus {
  TODO
  IN_PROGRESS
  DONE
  BLOCKED
  CANCELLED
}

enum ActionItemPriority {
  LOW
  MEDIUM
  HIGH
  CRITICAL
}

model ActionItem {
  id              String              @id @default(uuid())

  // Relations
  meetingId       String?
  meeting         Meeting?            @relation(fields: [meetingId], references: [id], onDelete: SetNull)

  assignedTo      String
  assignee        User                @relation("AssignedActionItems", fields: [assignedTo], references: [id], onDelete: Cascade)

  linkedGoalId    String?
  linkedGoal      Objective?          @relation(fields: [linkedGoalId], references: [id], onDelete: SetNull)

  // Content
  title           String
  description     String?             @db.Text

  // Status & Priority
  status          ActionItemStatus    @default(TODO)
  priority        ActionItemPriority  @default(MEDIUM)

  // Dates
  dueDate         DateTime?
  createdAt       DateTime            @default(now())
  updatedAt       DateTime            @updatedAt
  completedAt     DateTime?

  @@index([assignedTo, status])
  @@index([meetingId])
  @@index([dueDate])
  @@map("action_items")
}

// ============================================
// GOALS & OKR
// ============================================

enum ObjectiveCategory {
  BUSINESS
  TECHNOLOGY
  TEAM
  FUNDING
  PRODUCT
  MARKETING
  OPERATIONS
  OTHER
}

enum ObjectiveStatus {
  DRAFT
  ACTIVE
  ACHIEVED
  ABANDONED
  ARCHIVED
}

enum ObjectivePriority {
  LOW
  MEDIUM
  HIGH
  CRITICAL
}

model Objective {
  id                String              @id @default(uuid())
  relationshipId    String
  relationship      MentoringRelationship @relation(fields: [relationshipId], references: [id], onDelete: Cascade)

  // Content
  title             String
  description       String?             @db.Text
  category          ObjectiveCategory

  // Status & Priority
  status            ObjectiveStatus     @default(DRAFT)
  priority          ObjectivePriority   @default(MEDIUM)

  // Timeline
  startDate         DateTime
  targetDate        DateTime
  achievedDate      DateTime?

  // Metadata
  linkedMeetings    String[]            // Array of meetingIds
  reflectionNotes   String?             @db.Text

  // Created by
  createdBy         String
  creator           User                @relation("CreatedObjectives", fields: [createdBy], references: [id], onDelete: Cascade)

  // Timestamps
  createdAt         DateTime            @default(now())
  updatedAt         DateTime            @updatedAt

  // Relations
  keyResults        KeyResult[]
  actionItems       ActionItem[]

  @@index([relationshipId, status])
  @@index([category])
  @@index([targetDate])
  @@map("objectives")
}

enum MeasureType {
  NUMBER
  PERCENTAGE
  BOOLEAN
  CURRENCY
}

enum KeyResultStatus {
  NOT_STARTED
  ON_TRACK
  AT_RISK
  ACHIEVED
  MISSED
}

model KeyResult {
  id              String              @id @default(uuid())
  objectiveId     String
  objective       Objective           @relation(fields: [objectiveId], references: [id], onDelete: Cascade)

  // Content
  title           String
  description     String?             @db.Text

  // Measurement
  measureType     MeasureType
  targetValue     Float
  currentValue    Float               @default(0)
  unit            String?             // "users", "EUR", "%"

  // Status
  status          KeyResultStatus     @default(NOT_STARTED)

  // Timestamps
  createdAt       DateTime            @default(now())
  updatedAt       DateTime            @updatedAt

  // Relations
  progressUpdates ProgressUpdate[]

  @@index([objectiveId])
  @@map("key_results")
}

model ProgressUpdate {
  id              String      @id @default(uuid())
  keyResultId     String
  keyResult       KeyResult   @relation(fields: [keyResultId], references: [id], onDelete: Cascade)

  // Update data
  value           Float
  note            String?     @db.Text

  // Metadata
  updatedBy       String
  timestamp       DateTime    @default(now())

  @@index([keyResultId])
  @@index([timestamp])
  @@map("progress_updates")
}

// ============================================
// KNOWLEDGE BASE
// ============================================

enum ResourceType {
  LINK
  DOCUMENT
  NOTE
  BOOK
  CONTACT
  EVENT
  VIDEO
}

enum ResourceVisibility {
  PRIVATE_MENTOR
  PRIVATE_MENTEE
  SHARED
}

model Resource {
  id                String              @id @default(uuid())
  relationshipId    String
  relationship      MentoringRelationship @relation(fields: [relationshipId], references: [id], onDelete: Cascade)

  // Type & Content
  type              ResourceType
  title             String
  description       String?             @db.Text
  content           String?             @db.Text  // For notes
  url               String?
  fileUrl           String?
  fileSize          Int?
  mimeType          String?

  // Organization
  category          String?
  tags              String[]
  visibility        ResourceVisibility  @default(SHARED)

  // Links
  linkedToMeeting   String?
  linkedToGoal      String?

  // Metadata
  addedBy           String
  createdAt         DateTime            @default(now())
  updatedAt         DateTime            @updatedAt

  @@index([relationshipId])
  @@index([type])
  @@index([category])
  @@index([addedBy])
  @@map("resources")
}

model Insight {
  id                String              @id @default(uuid())
  relationshipId    String

  // Content
  title             String
  content           String              @db.Text
  source            String?             // "Meeting 2024-01-15"
  tags              String[]
  importance        String              @default("MEDIUM") // LOW, MEDIUM, HIGH

  // Metadata
  dateRecorded      DateTime            @default(now())
  createdBy         String
  createdAt         DateTime            @default(now())
  updatedAt         DateTime            @updatedAt

  @@index([relationshipId])
  @@map("insights")
}

// ============================================
// COMMUNICATION
// ============================================

enum NotificationType {
  MEETING_REMINDER
  ACTION_ITEM_DUE
  GOAL_AT_RISK
  NEW_RESOURCE
  MESSAGE
  SYSTEM
  GOAL_ACHIEVED
  MEETING_CANCELLED
}

model Notification {
  id          String            @id @default(uuid())
  userId      String
  user        User              @relation(fields: [userId], references: [id], onDelete: Cascade)

  // Content
  type        NotificationType
  title       String
  message     String            @db.Text
  link        String?           // Deep link

  // Status
  isRead      Boolean           @default(false)
  readAt      DateTime?

  // Metadata
  createdAt   DateTime          @default(now())

  @@index([userId, isRead])
  @@index([createdAt])
  @@map("notifications")
}

model Message {
  id              String              @id @default(uuid())
  relationshipId  String
  relationship    MentoringRelationship @relation(fields: [relationshipId], references: [id], onDelete: Cascade)

  senderId        String
  sender          User                @relation("SentMessages", fields: [senderId], references: [id], onDelete: Cascade)

  recipientId     String
  recipient       User                @relation("ReceivedMessages", fields: [recipientId], references: [id], onDelete: Cascade)

  // Content
  content         String              @db.Text
  attachments     String[]            // URLs to files

  // Status
  isRead          Boolean             @default(false)
  readAt          DateTime?

  // Timestamps
  sentAt          DateTime            @default(now())

  @@index([relationshipId])
  @@index([senderId])
  @@index([recipientId])
  @@map("messages")
}

// ============================================
// AUDIT & LOGGING
// ============================================

enum AuditAction {
  CREATE
  UPDATE
  DELETE
  LOGIN
  LOGOUT
  EXPORT
}

model AuditLog {
  id              String      @id @default(uuid())
  userId          String?
  user            User?       @relation(fields: [userId], references: [id], onDelete: SetNull)

  // Action details
  action          AuditAction
  entityType      String      // "Meeting", "Objective", etc.
  entityId        String?
  changes         Json?       // Before/after snapshot

  // Context
  ipAddress       String?
  userAgent       String?

  // Timestamp
  timestamp       DateTime    @default(now())

  @@index([userId])
  @@index([entityType, entityId])
  @@index([timestamp])
  @@map("audit_logs")
}
```

---

## 3. Relacje i Kardynalności

### 3.1 Kluczowe Relacje

**User ↔ MentoringRelationship**
- 1 User (Mentor) : N MentoringRelationships
- 1 User (Mentee) : N MentoringRelationships
- Cascade delete: Usunięcie użytkownika usuwa wszystkie relacje

**MentoringRelationship ↔ Meeting**
- 1 Relationship : N Meetings
- Cascade delete: Usunięcie relacji usuwa wszystkie spotkania

**Meeting ↔ ActionItem**
- 1 Meeting : N ActionItems
- Set null on delete: Usunięcie spotkania nie usuwa action items (zachowujemy historię)

**Objective ↔ KeyResult**
- 1 Objective : N KeyResults (minimum 1, maksymalnie ~5 recommended)
- Cascade delete: Usunięcie celu usuwa wszystkie KR

**KeyResult ↔ ProgressUpdate**
- 1 KeyResult : N ProgressUpdates
- Cascade delete: Zachowujemy całą historię zmian

### 3.2 Constrainty i Walidacja na Poziomie DB

```sql
-- Unique constraint: Każda para mentor-mentee jest unikalna
ALTER TABLE mentoring_relationships
ADD CONSTRAINT unique_mentor_mentee UNIQUE (mentor_id, mentee_id);

-- Check constraint: Data zakończenia > data rozpoczęcia
ALTER TABLE mentoring_relationships
ADD CONSTRAINT valid_date_range
CHECK (end_date IS NULL OR end_date > start_date);

-- Check constraint: Czas trwania spotkania > 0
ALTER TABLE meetings
ADD CONSTRAINT positive_duration
CHECK (duration > 0);

-- Check constraint: Target date > start date
ALTER TABLE objectives
ADD CONSTRAINT valid_objective_dates
CHECK (target_date > start_date);

-- Check constraint: Current value <= target value (dla większości cases)
-- Implementacja w application logic, ponieważ zależy od typu metryki
```

---

## 4. Indeksy dla Optymalizacji Query

### 4.1 Często Wykonywane Queries i Indeksy

**Query 1: Pobierz aktywne spotkania dla użytkownika**
```sql
-- Index już zdefiniowany w schema
CREATE INDEX idx_meetings_relationship_scheduled
ON meetings(relationship_id, scheduled_at)
WHERE status = 'SCHEDULED';
```

**Query 2: Pobierz action items dla użytkownika z filtrem po statusie**
```sql
-- Composite index
CREATE INDEX idx_action_items_assignee_status_due
ON action_items(assigned_to, status, due_date);
```

**Query 3: Full-text search w notatkach ze spotkań**
```sql
-- PostgreSQL GIN index dla full-text search
CREATE INDEX idx_meetings_notes_fulltext
ON meetings USING GIN (to_tsvector('polish', notes));

CREATE INDEX idx_resources_content_fulltext
ON resources USING GIN (to_tsvector('polish', content));
```

**Query 4: Pobranie celów z progress > X%**
```sql
-- Computed column + index (Postgres 12+)
-- Implementacja w application layer przez agregację KeyResults
```

### 4.2 Partycjonowanie (Phase 3 - jeśli skala > 100k spotkań)

```sql
-- Partycjonowanie tabeli meetings po dacie (yearly)
CREATE TABLE meetings_2024 PARTITION OF meetings
FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');

CREATE TABLE meetings_2025 PARTITION OF meetings
FOR VALUES FROM ('2025-01-01') TO ('2026-01-01');
```

---

## 5. Migracje i Seed Data

### 5.1 Przykładowa Migracja (Prisma)

```typescript
// migrations/20260118000000_init/migration.sql
-- CreateEnum
CREATE TYPE "UserRole" AS ENUM ('MENTOR', 'MENTEE', 'ADMIN');
-- (...)

-- CreateTable
CREATE TABLE "users" (
    "id" TEXT NOT NULL,
    "email" TEXT NOT NULL,
    -- (...)
    CONSTRAINT "users_pkey" PRIMARY KEY ("id")
);

-- CreateIndex
CREATE UNIQUE INDEX "users_email_key" ON "users"("email");
```

### 5.2 Seed Data dla Development

```typescript
// prisma/seed.ts
import { PrismaClient } from '@prisma/client';
import { hash } from 'bcrypt';

const prisma = new PrismaClient();

async function main() {
  // Create mentor
  const mentor = await prisma.user.create({
    data: {
      email: 'anna.kowalska@example.com',
      passwordHash: await hash('demo123', 10),
      role: 'MENTOR',
      firstName: 'Anna',
      lastName: 'Kowalska',
      bio: 'Ekspertka w technologiach satelitarnych z 20-letnim doświadczeniem',
      expertise: ['Space Tech', 'Satellite Technology', 'Fundraising'],
      timezone: 'Europe/Warsaw',
    },
  });

  // Create mentee
  const mentee = await prisma.user.create({
    data: {
      email: 'tomasz.nowak@thaliana.space',
      passwordHash: await hash('demo123', 10),
      role: 'MENTEE',
      firstName: 'Tomasz',
      lastName: 'Nowak',
      company: 'Thaliana Space',
      position: 'CEO & Co-Founder',
      timezone: 'Europe/Warsaw',
    },
  });

  // Create mentoring relationship
  const relationship = await prisma.mentoringRelationship.create({
    data: {
      mentorId: mentor.id,
      menteeId: mentee.id,
      status: 'ACTIVE',
      focusAreas: ['Fundraising', 'Product Strategy', 'Space Industry'],
      meetingFrequency: 'Bi-weekly',
      relationshipGoals: 'Pomoc w przygotowaniu do rundy Seed i rozwoju produktu',
    },
  });

  // Create sample meeting
  await prisma.meeting.create({
    data: {
      relationshipId: relationship.id,
      title: 'Kickoff Session - Poznanie celów i oczekiwań',
      scheduledAt: new Date('2026-01-25T10:00:00Z'),
      duration: 60,
      location: 'https://zoom.us/j/xxx',
      status: 'SCHEDULED',
      createdBy: mentor.id,
      agenda: {
        items: [
          { title: 'Wprowadzenie i poznanie', estimatedTime: 15 },
          { title: 'Omówienie celów Thaliana Space', estimatedTime: 20 },
          { title: 'Ustalenie priorytetów i planu działania', estimatedTime: 25 },
        ],
      },
      tags: ['Kickoff', 'Planning'],
    },
  });

  // Create sample objective
  const objective = await prisma.objective.create({
    data: {
      relationshipId: relationship.id,
      createdBy: mentee.id,
      title: 'Przygotowanie i zamknięcie rundy Seed (500k EUR)',
      description: 'Pozyskanie finansowania seed w wysokości 500k EUR do Q2 2026',
      category: 'FUNDING',
      status: 'ACTIVE',
      priority: 'CRITICAL',
      startDate: new Date('2026-01-15'),
      targetDate: new Date('2026-06-30'),
      keyResults: {
        create: [
          {
            title: 'Przygotowanie pitch deck i financial model',
            measureType: 'BOOLEAN',
            targetValue: 1,
            currentValue: 0,
            status: 'IN_PROGRESS',
          },
          {
            title: 'Przeprowadzenie 20 spotkań z inwestorami',
            measureType: 'NUMBER',
            targetValue: 20,
            currentValue: 3,
            unit: 'meetings',
            status: 'ON_TRACK',
          },
          {
            title: 'Otrzymanie minimum 3 term sheets',
            measureType: 'NUMBER',
            targetValue: 3,
            currentValue: 0,
            unit: 'term sheets',
            status: 'NOT_STARTED',
          },
        ],
      },
    },
  });

  console.log('✅ Seed data created successfully');
  console.log({ mentor, mentee, relationship, objective });
}

main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

---

## 6. Data Privacy & Security

### 6.1 Wrażliwe Dane

**Dane wymagające szczególnej ochrony**:
- `mentorPrivateNotes`, `menteePrivateNotes` w MentoringRelationship
- `notes` w Meeting (mogą zawierać poufne informacje biznesowe)
- `content` w Resource (dokumenty firmowe)
- `passwordHash` i `twoFactorSecret` w User

**Implementacja**:
```typescript
// Encryption helper dla sensitive fields
import { createCipheriv, createDecipheriv } from 'crypto';

class EncryptionService {
  private algorithm = 'aes-256-gcm';
  private key = Buffer.from(process.env.ENCRYPTION_KEY!, 'hex');

  encrypt(text: string): { encrypted: string; iv: string; tag: string } {
    const iv = randomBytes(16);
    const cipher = createCipheriv(this.algorithm, this.key, iv);
    let encrypted = cipher.update(text, 'utf8', 'hex');
    encrypted += cipher.final('hex');
    const tag = cipher.getAuthTag();

    return {
      encrypted,
      iv: iv.toString('hex'),
      tag: tag.toString('hex'),
    };
  }

  decrypt(encrypted: string, iv: string, tag: string): string {
    const decipher = createDecipheriv(
      this.algorithm,
      this.key,
      Buffer.from(iv, 'hex')
    );
    decipher.setAuthTag(Buffer.from(tag, 'hex'));
    let decrypted = decipher.update(encrypted, 'hex', 'utf8');
    decrypted += decipher.final('utf8');
    return decrypted;
  }
}
```

### 6.2 Row-Level Security (RLS)

```sql
-- Enable RLS na tabelach
ALTER TABLE mentoring_relationships ENABLE ROW LEVEL SECURITY;
ALTER TABLE meetings ENABLE ROW LEVEL SECURITY;
ALTER TABLE objectives ENABLE ROW LEVEL SECURITY;

-- Policy: Użytkownik widzi tylko swoje relacje
CREATE POLICY user_own_relationships ON mentoring_relationships
FOR SELECT
USING (mentor_id = current_user_id() OR mentee_id = current_user_id());

-- Policy: Użytkownik może edytować tylko swoje prywatne notatki
CREATE POLICY mentor_edit_own_notes ON mentoring_relationships
FOR UPDATE
USING (mentor_id = current_user_id())
WITH CHECK (mentor_id = current_user_id());
```

### 6.3 RODO Compliance

**Right to Access**: Export wszystkich danych użytkownika w JSON/PDF
```typescript
async function exportUserData(userId: string) {
  const user = await prisma.user.findUnique({
    where: { id: userId },
    include: {
      mentorRelationships: {
        include: { meetings: true, objectives: true },
      },
      menteeRelationships: {
        include: { meetings: true, objectives: true },
      },
      assignedActionItems: true,
      notifications: true,
    },
  });
  return user;
}
```

**Right to be Forgotten**: Anonimizacja lub usunięcie danych
```typescript
async function deleteUserData(userId: string) {
  await prisma.$transaction([
    // Anonymize instead of delete (preserve data integrity)
    prisma.user.update({
      where: { id: userId },
      data: {
        email: `deleted_${userId}@anonymous.com`,
        firstName: 'Deleted',
        lastName: 'User',
        avatar: null,
        bio: null,
        passwordHash: '',
        // Keep relationships but mark as archived
      },
    }),
    prisma.auditLog.create({
      data: {
        userId,
        action: 'DELETE',
        entityType: 'User',
        entityId: userId,
      },
    }),
  ]);
}
```

---

## 7. Backup & Recovery Strategy

### 7.1 Backup Policy
- **Frequency**: Daily automated backups at 2 AM UTC
- **Retention**:
  - Daily backups: 30 days
  - Weekly backups: 3 months
  - Monthly backups: 1 year
- **Type**: Full database dumps + incremental WAL archiving

### 7.2 Backup Script
```bash
#!/bin/bash
# backup.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/postgres"
DB_NAME="thaliana_mentoring"

# Create backup
pg_dump -U postgres -F c -b -v -f "$BACKUP_DIR/backup_$DATE.dump" $DB_NAME

# Compress
gzip "$BACKUP_DIR/backup_$DATE.dump"

# Upload to S3
aws s3 cp "$BACKUP_DIR/backup_$DATE.dump.gz" s3://thaliana-backups/db/

# Clean old local backups (keep last 7 days locally)
find $BACKUP_DIR -name "backup_*.dump.gz" -mtime +7 -delete

echo "✅ Backup completed: backup_$DATE.dump.gz"
```

### 7.3 Recovery Procedure
```bash
# Download backup from S3
aws s3 cp s3://thaliana-backups/db/backup_20260118.dump.gz .

# Decompress
gunzip backup_20260118.dump.gz

# Restore
pg_restore -U postgres -d thaliana_mentoring -c backup_20260118.dump

# Verify
psql -U postgres -d thaliana_mentoring -c "SELECT COUNT(*) FROM users;"
```

---

**Wersja**: 1.0
**Status**: Draft
**Ostatnia aktualizacja**: 2026-01-18
