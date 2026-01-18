# Rekomendacje Technologiczne - Thaliana Mentoring Platform

## 1. Executive Summary

### 1.1 Rekomendowany Stack (MVP)

| Warstwa | Technologia | Wersja | Uzasadnienie |
|---------|-------------|--------|--------------|
| **Frontend** | React + TypeScript | 18.3+ | Industry standard, mature ecosystem, type safety |
| **Build Tool** | Vite | 5+ | Szybki dev experience, optimized production builds |
| **UI Framework** | Tailwind CSS + Shadcn/ui | 3.4+ | Rapid development, customizable, accessibility built-in |
| **State Management** | Zustand | 4+ | Lightweight, simple API, sufficient dla MVP |
| **Data Fetching** | TanStack Query (React Query) | 5+ | Caching, optimistic updates, server state management |
| **Backend Framework** | NestJS (Node.js) | 10+ | TypeScript end-to-end, modular, scalable architecture |
| **ORM** | Prisma | 5+ | Type-safe queries, excellent migrations, auto-generated client |
| **Database** | PostgreSQL | 14+ | Robust, JSON support, full-text search, mature |
| **Cache/Sessions** | Redis | 7+ | In-memory performance, pub/sub dla real-time |
| **File Storage** | AWS S3 / MinIO | - | Scalable object storage, S3-compatible |
| **Authentication** | NextAuth.js / Passport.js | - | Proven solutions, social auth support, JWT |
| **API Type** | REST + WebSockets | - | RESTful dla CRUD, WS dla real-time updates |
| **Hosting** | DigitalOcean / AWS | - | Cost-effective, scalable, managed services available |
| **CI/CD** | GitHub Actions | - | Integrated with GitHub, free dla public repos, flexible |

### 1.2 Platform Recommendation: **Web-First + Progressive Web App (PWA)**

**Uzasadnienie**:
- **MVP Speed**: Web app fastest to market (jedna codebase dla desktop/mobile)
- **Accessibility**: No app store approvals, instant updates
- **Cost**: Rozwój jednej aplikacji vs native apps (iOS + Android)
- **PWA Benefits**: Install on mobile, offline mode, push notifications
- **Future**: Native apps (React Native) w Phase 3 jeśli business case

---

## 2. Frontend Stack - Szczegóły

### 2.1 Core Framework: React 18 + TypeScript

**Dlaczego React?**
- ✅ Największy ecosystem (biblioteki, komponenty, talent pool)
- ✅ Excellent developer experience z TypeScript
- ✅ Server Components (future-proofing dla performance)
- ✅ Mature, stable, backed by Meta
- ✅ Świetne narzędzia devtools, testing libraries

**Dlaczego TypeScript?**
- ✅ Type safety = mniej bugs w production
- ✅ Better IDE support (autocomplete, refactoring)
- ✅ Easier onboarding dla nowych developerów
- ✅ Enforces schema contracts między frontend/backend

**Alternatywy Rozważone**:
| Framework | Pros | Cons | Verdict |
|-----------|------|------|---------|
| Vue.js | Łatwiejsza krzywa nauki, excellent docs | Mniejsze community niż React | ❌ React preferred dla talent availability |
| Svelte | Fastest runtime, minimalna boilerplate | Mniejszy ecosystem, mniej mature | ❌ Too risky dla production app |
| Angular | Full framework, enterprise-ready | Heavyweight, steeper learning curve | ❌ Overkill dla MVP |

---

### 2.2 Build Tool: Vite

**Dlaczego Vite?**
- ✅ Lightning-fast HMR (Hot Module Replacement)
- ✅ Modern ESM-based development
- ✅ Optimized production builds (Rollup under hood)
- ✅ Plugin ecosystem (PWA, compression, etc.)
- ✅ Much faster niż Create React App

**Configuration Example**:
```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { VitePWA } from 'vite-plugin-pwa';

export default defineConfig({
  plugins: [
    react(),
    VitePWA({
      registerType: 'autoUpdate',
      manifest: {
        name: 'Thaliana Mentoring',
        short_name: 'Mentoring',
        theme_color: '#0A2463',
        icons: [/* ... */],
      },
    }),
  ],
  build: {
    target: 'es2015',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'react-router-dom'],
          ui: ['@radix-ui/react-dialog', '@radix-ui/react-dropdown-menu'],
        },
      },
    },
  },
});
```

---

### 2.3 UI Framework: Tailwind CSS + Shadcn/ui

**Dlaczego Tailwind?**
- ✅ Utility-first = rapid prototyping
- ✅ Consistent design system przez constraints
- ✅ Tree-shakeable = small bundle sizes
- ✅ Excellent DX z JIT compiler
- ✅ Easy responsive design

**Dlaczego Shadcn/ui?**
- ✅ Copy-paste components (own your code, no lock-in)
- ✅ Accessibility built-in (Radix UI primitives)
- ✅ Highly customizable
- ✅ No runtime overhead (unlike component libraries)
- ✅ TypeScript native

**Example Component Usage**:
```tsx
import { Button } from '@/components/ui/button';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';

function MeetingCard({ meeting }) {
  return (
    <Card>
      <CardHeader>
        <CardTitle>{meeting.title}</CardTitle>
      </CardHeader>
      <CardContent>
        <p>{meeting.scheduledAt}</p>
        <Button onClick={() => joinMeeting(meeting.id)}>
          Join Meeting
        </Button>
      </CardContent>
    </Card>
  );
}
```

**Alternatywy**:
- **Material UI**: Too opinionated design, harder to customize
- **Ant Design**: Great dla admin panels, ale za dużo dla mentoring app
- **Chakra UI**: Good alternative, ale Shadcn ma lepszą flexibility

---

### 2.4 State Management: Zustand

**Dlaczego Zustand?**
- ✅ Minimalna boilerplate (vs Redux)
- ✅ Simple API, easy to learn
- ✅ Good performance (hook-based subscriptions)
- ✅ No Context Provider hell
- ✅ Sufficient dla majority of apps

**Example Store**:
```typescript
// stores/authStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface AuthState {
  user: User | null;
  token: string | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      token: null,
      login: async (email, password) => {
        const response = await authAPI.login(email, password);
        set({ user: response.user, token: response.token });
      },
      logout: () => set({ user: null, token: null }),
    }),
    {
      name: 'auth-storage',
      partialize: (state) => ({ token: state.token }), // tylko token w localStorage
    }
  )
);
```

**Kiedy rozważyć alternatywy**:
- **Redux Toolkit**: Jeśli app rośnie do >50 stores i potrzeba middleware ecosystem
- **Jotai**: Atomic state approach, good dla complex derived state
- **Current Verdict**: Zustand sufficient dla MVP, może evaluate w Phase 2

---

### 2.5 Data Fetching: TanStack Query (React Query)

**Dlaczego React Query?**
- ✅ Automatic caching i refetching
- ✅ Optimistic updates out of box
- ✅ Loading/error states standardized
- ✅ Excellent DevTools
- ✅ Separation of server state from client state

**Example Usage**:
```typescript
// hooks/useMeetings.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

export function useMeetings(relationshipId: string) {
  return useQuery({
    queryKey: ['meetings', relationshipId],
    queryFn: () => meetingsAPI.getByRelationship(relationshipId),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
}

export function useCreateMeeting() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: meetingsAPI.create,
    onSuccess: (newMeeting) => {
      // Invalidate i refetch meetings
      queryClient.invalidateQueries({ queryKey: ['meetings'] });

      // Optimistically update cache
      queryClient.setQueryData(
        ['meetings', newMeeting.relationshipId],
        (old: Meeting[]) => [...old, newMeeting]
      );
    },
  });
}

// Component usage
function MeetingsList({ relationshipId }) {
  const { data: meetings, isLoading, error } = useMeetings(relationshipId);
  const createMeeting = useCreateMeeting();

  if (isLoading) return <Skeleton />;
  if (error) return <ErrorState />;

  return (
    <>
      {meetings.map(meeting => <MeetingCard key={meeting.id} meeting={meeting} />)}
      <Button onClick={() => createMeeting.mutate({ /* ... */ })}>
        Create Meeting
      </Button>
    </>
  );
}
```

---

### 2.6 Routing: React Router v6

**Features Used**:
- Nested routes
- Protected routes (authentication guards)
- Lazy loading dla code splitting

**Example Router Setup**:
```typescript
// router.tsx
import { createBrowserRouter } from 'react-router-dom';
import { lazy, Suspense } from 'react';

const Dashboard = lazy(() => import('./pages/Dashboard'));
const Meetings = lazy(() => import('./pages/Meetings'));
const Goals = lazy(() => import('./pages/Goals'));

export const router = createBrowserRouter([
  {
    path: '/',
    element: <RootLayout />,
    children: [
      {
        index: true,
        element: <Navigate to="/dashboard" replace />,
      },
      {
        path: 'dashboard',
        element: (
          <Suspense fallback={<PageLoader />}>
            <ProtectedRoute>
              <Dashboard />
            </ProtectedRoute>
          </Suspense>
        ),
      },
      {
        path: 'meetings',
        element: (
          <Suspense fallback={<PageLoader />}>
            <ProtectedRoute>
              <Meetings />
            </ProtectedRoute>
          </Suspense>
        ),
      },
      // ...
    ],
  },
]);
```

---

### 2.7 Forms: React Hook Form + Zod

**Dlaczego React Hook Form?**
- ✅ Minimal re-renders (uncontrolled inputs)
- ✅ Built-in validation
- ✅ Easy integration z UI libraries
- ✅ TypeScript support

**Dlaczego Zod?**
- ✅ Schema validation (frontend + backend reuse)
- ✅ Type inference (automatic TypeScript types)
- ✅ Runtime safety

**Example**:
```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const meetingSchema = z.object({
  title: z.string().min(3, 'Title must be at least 3 characters'),
  scheduledAt: z.date(),
  duration: z.number().min(15).max(180),
  location: z.string().url().optional(),
});

type MeetingFormData = z.infer<typeof meetingSchema>;

function MeetingForm() {
  const { register, handleSubmit, formState: { errors } } = useForm<MeetingFormData>({
    resolver: zodResolver(meetingSchema),
  });

  const onSubmit = (data: MeetingFormData) => {
    createMeeting.mutate(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input
        {...register('title')}
        error={errors.title?.message}
      />
      {/* ... */}
      <Button type="submit">Create</Button>
    </form>
  );
}
```

---

### 2.8 Real-time Updates: WebSockets (Socket.io-client)

**Use Cases**:
- Notification alerts
- Live collaboration (jeśli mentor i mentee pracują jednocześnie)
- Online status indicators
- Real-time progress updates

**Example**:
```typescript
// hooks/useRealtimeNotifications.ts
import { useEffect } from 'react';
import { io } from 'socket.io-client';
import { useNotificationsStore } from '@/stores/notificationsStore';

export function useRealtimeNotifications() {
  const addNotification = useNotificationsStore(state => state.add);

  useEffect(() => {
    const socket = io(import.meta.env.VITE_WS_URL, {
      auth: {
        token: localStorage.getItem('auth-token'),
      },
    });

    socket.on('notification', (notification) => {
      addNotification(notification);
      // Show toast
      toast.info(notification.message);
    });

    return () => {
      socket.disconnect();
    };
  }, []);
}
```

---

## 3. Backend Stack - Szczegóły

### 3.1 Framework: NestJS

**Dlaczego NestJS?**
- ✅ TypeScript native (share types z frontendem)
- ✅ Modular architecture (similar to Angular, but better)
- ✅ Dependency injection built-in
- ✅ Excellent dla microservices (jeśli scale w przyszłości)
- ✅ OpenAPI/Swagger integration
- ✅ Built-in WebSockets, GraphQL support

**Project Structure**:
```
src/
├── auth/
│   ├── auth.module.ts
│   ├── auth.service.ts
│   ├── auth.controller.ts
│   ├── guards/
│   └── strategies/
├── users/
│   ├── users.module.ts
│   ├── users.service.ts
│   ├── users.controller.ts
│   └── dto/
├── meetings/
│   ├── meetings.module.ts
│   ├── meetings.service.ts
│   ├── meetings.controller.ts
│   └── dto/
├── goals/
│   ├── goals.module.ts
│   ├── goals.service.ts
│   ├── goals.controller.ts
│   └── dto/
├── common/
│   ├── decorators/
│   ├── filters/
│   ├── guards/
│   ├── interceptors/
│   └── pipes/
├── database/
│   └── prisma.service.ts
├── app.module.ts
└── main.ts
```

**Example Module**:
```typescript
// meetings/meetings.module.ts
import { Module } from '@nestjs/common';
import { MeetingsController } from './meetings.controller';
import { MeetingsService } from './meetings.service';
import { PrismaService } from '../database/prisma.service';

@Module({
  controllers: [MeetingsController],
  providers: [MeetingsService, PrismaService],
  exports: [MeetingsService],
})
export class MeetingsModule {}

// meetings/meetings.controller.ts
import { Controller, Get, Post, Body, Param, UseGuards } from '@nestjs/common';
import { MeetingsService } from './meetings.service';
import { JwtAuthGuard } from '../auth/guards/jwt-auth.guard';
import { CreateMeetingDto } from './dto/create-meeting.dto';

@Controller('meetings')
@UseGuards(JwtAuthGuard)
export class MeetingsController {
  constructor(private readonly meetingsService: MeetingsService) {}

  @Post()
  async create(@Body() createMeetingDto: CreateMeetingDto) {
    return this.meetingsService.create(createMeetingDto);
  }

  @Get(':id')
  async findOne(@Param('id') id: string) {
    return this.meetingsService.findOne(id);
  }

  // ...
}
```

**Alternatywy**:
- **Express.js**: Too minimalistic, brak structure
- **Fastify**: Faster performance, ale mniej mature ecosystem
- **Verdict**: NestJS best dla structured, scalable app

---

### 3.2 ORM: Prisma

**Dlaczego Prisma?**
- ✅ Type-safe queries (auto-generated TypeScript client)
- ✅ Excellent migrations system
- ✅ Intuitive schema definition
- ✅ Built-in connection pooling
- ✅ Great DevX (Prisma Studio dla browsing data)

**Example Service**:
```typescript
// meetings/meetings.service.ts
import { Injectable } from '@nestjs/common';
import { PrismaService } from '../database/prisma.service';
import { CreateMeetingDto } from './dto/create-meeting.dto';

@Injectable()
export class MeetingsService {
  constructor(private prisma: PrismaService) {}

  async create(data: CreateMeetingDto) {
    return this.prisma.meeting.create({
      data: {
        ...data,
        createdBy: data.userId,
      },
      include: {
        relationship: {
          include: {
            mentor: true,
            mentee: true,
          },
        },
      },
    });
  }

  async findByRelationship(relationshipId: string) {
    return this.prisma.meeting.findMany({
      where: { relationshipId },
      orderBy: { scheduledAt: 'desc' },
      include: {
        actionItems: {
          where: { status: { not: 'DONE' } },
        },
      },
    });
  }

  // Type-safe, auto-completed, compiled-time checked!
}
```

**Alternatywy**:
- **TypeORM**: More mature, ale mniej intuitive API
- **Sequelize**: Legacy, nie TypeScript-first
- **Verdict**: Prisma clear winner dla TypeScript projects

---

### 3.3 Database: PostgreSQL

**Dlaczego PostgreSQL?**
- ✅ Most advanced open-source RDBMS
- ✅ JSON/JSONB support (flexibility)
- ✅ Full-text search (pg_trgm, tsvector)
- ✅ Robust transactions, ACID compliance
- ✅ Excellent performance dla medium scale
- ✅ Mature ecosystem (PgBouncer, PostGIS, extensions)

**PostgreSQL-Specific Features Used**:
```sql
-- Full-text search
CREATE INDEX idx_meetings_notes_fts
ON meetings USING GIN (to_tsvector('english', notes));

-- Query
SELECT * FROM meetings
WHERE to_tsvector('english', notes) @@ to_tsquery('fundraising & investor');

-- JSONB dla flexible data
ALTER TABLE meetings
ADD COLUMN metadata JSONB;

-- Query JSONB
SELECT * FROM meetings
WHERE metadata->>'category' = 'Fundraising';

-- Array operations
SELECT * FROM objectives
WHERE 'Fundraising' = ANY(linked_meetings);
```

**Alternatywy**:
- **MySQL**: Mniej features niż PostgreSQL
- **MongoDB**: NoSQL - not suitable dla relational data (meetings, goals)
- **Verdict**: PostgreSQL ideal dla structured data + flexibility

---

### 3.4 Caching: Redis

**Use Cases**:
- Session storage (JWT refresh tokens)
- API response caching
- Real-time features (pub/sub)
- Rate limiting counters
- Temporary data (OTP codes, password reset tokens)

**Example Implementation**:
```typescript
// common/cache/cache.service.ts
import { Injectable } from '@nestjs/common';
import { Redis } from 'ioredis';

@Injectable()
export class CacheService {
  private redis: Redis;

  constructor() {
    this.redis = new Redis({
      host: process.env.REDIS_HOST,
      port: Number(process.env.REDIS_PORT),
      password: process.env.REDIS_PASSWORD,
    });
  }

  async get<T>(key: string): Promise<T | null> {
    const value = await this.redis.get(key);
    return value ? JSON.parse(value) : null;
  }

  async set(key: string, value: any, ttlSeconds?: number): Promise<void> {
    const serialized = JSON.stringify(value);
    if (ttlSeconds) {
      await this.redis.setex(key, ttlSeconds, serialized);
    } else {
      await this.redis.set(key, serialized);
    }
  }

  async del(key: string): Promise<void> {
    await this.redis.del(key);
  }

  // Rate limiting
  async incrementRateLimit(key: string, windowSeconds: number): Promise<number> {
    const current = await this.redis.incr(key);
    if (current === 1) {
      await this.redis.expire(key, windowSeconds);
    }
    return current;
  }
}

// Usage w controller
@Controller('meetings')
export class MeetingsController {
  @Get()
  async findAll(@Query('relationshipId') relationshipId: string) {
    const cacheKey = `meetings:${relationshipId}`;
    const cached = await this.cacheService.get(cacheKey);

    if (cached) {
      return cached;
    }

    const meetings = await this.meetingsService.findByRelationship(relationshipId);
    await this.cacheService.set(cacheKey, meetings, 300); // 5 min TTL

    return meetings;
  }
}
```

---

### 3.5 Authentication: JWT + Passport.js

**Strategy**:
- JWT access tokens (short-lived: 15 min)
- JWT refresh tokens (long-lived: 7 days, stored w Redis)
- Refresh token rotation dla security

**Implementation**:
```typescript
// auth/strategies/jwt.strategy.ts
import { Injectable } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      secretOrKey: process.env.JWT_SECRET,
    });
  }

  async validate(payload: any) {
    return { userId: payload.sub, email: payload.email, role: payload.role };
  }
}

// auth/auth.service.ts
@Injectable()
export class AuthService {
  async login(email: string, password: string) {
    const user = await this.validateUser(email, password);

    const accessToken = this.jwtService.sign(
      { sub: user.id, email: user.email, role: user.role },
      { expiresIn: '15m' }
    );

    const refreshToken = this.jwtService.sign(
      { sub: user.id, type: 'refresh' },
      { expiresIn: '7d' }
    );

    // Store refresh token w Redis
    await this.cacheService.set(
      `refresh:${user.id}`,
      refreshToken,
      7 * 24 * 60 * 60 // 7 days
    );

    return { accessToken, refreshToken, user };
  }

  async refresh(refreshToken: string) {
    const payload = this.jwtService.verify(refreshToken);
    const storedToken = await this.cacheService.get(`refresh:${payload.sub}`);

    if (storedToken !== refreshToken) {
      throw new UnauthorizedException('Invalid refresh token');
    }

    // Rotate refresh token
    await this.cacheService.del(`refresh:${payload.sub}`);

    return this.login(/* generate new tokens */);
  }
}
```

---

### 3.6 File Storage: AWS S3 / MinIO

**Dlaczego S3?**
- ✅ Industry standard
- ✅ Unlimited scalability
- ✅ Built-in CDN (CloudFront)
- ✅ Lifecycle policies (archive old files)
- ✅ Cheap dla storage

**Dlaczego MinIO (alternatywa)?**
- ✅ S3-compatible API (drop-in replacement)
- ✅ Self-hosted option (data sovereignty)
- ✅ No vendor lock-in
- ✅ Good dla development/staging

**Implementation**:
```typescript
// files/files.service.ts
import { Injectable } from '@nestjs/common';
import { S3 } from 'aws-sdk';

@Injectable()
export class FilesService {
  private s3: S3;

  constructor() {
    this.s3 = new S3({
      accessKeyId: process.env.AWS_ACCESS_KEY_ID,
      secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
      region: process.env.AWS_REGION,
    });
  }

  async uploadFile(file: Express.Multer.File, folder: string): Promise<string> {
    const key = `${folder}/${Date.now()}-${file.originalname}`;

    await this.s3.upload({
      Bucket: process.env.S3_BUCKET,
      Key: key,
      Body: file.buffer,
      ContentType: file.mimetype,
      ACL: 'private',
    }).promise();

    return this.getSignedUrl(key);
  }

  async getSignedUrl(key: string, expiresIn: number = 3600): Promise<string> {
    return this.s3.getSignedUrlPromise('getObject', {
      Bucket: process.env.S3_BUCKET,
      Key: key,
      Expires: expiresIn,
    });
  }

  async deleteFile(key: string): Promise<void> {
    await this.s3.deleteObject({
      Bucket: process.env.S3_BUCKET,
      Key: key,
    }).promise();
  }
}
```

---

## 4. Infrastructure & DevOps

### 4.1 Hosting: DigitalOcean App Platform

**Dlaczego DigitalOcean?**
- ✅ Cost-effective ($12-50/month dla MVP)
- ✅ Simple deployment (git push to deploy)
- ✅ Managed databases (PostgreSQL, Redis)
- ✅ Automatic SSL, CDN
- ✅ Easy scaling (vertical + horizontal)
- ✅ European data centers (GDPR compliance)

**Architecture na DigitalOcean**:
```
┌─────────────────────────────────────────┐
│  App Platform                            │
│  ┌───────────────┐  ┌──────────────┐   │
│  │  Frontend     │  │  Backend     │   │
│  │  (React/Vite) │  │  (NestJS)    │   │
│  │  Static Site  │  │  App Service │   │
│  └───────────────┘  └──────────────┘   │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │  Managed PostgreSQL Database       │ │
│  └────────────────────────────────────┘ │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │  Managed Redis                     │ │
│  └────────────────────────────────────┘ │
└─────────────────────────────────────────┘

External:
  - Spaces (S3-compatible object storage)
  - CDN for static assets
```

**Cost Estimate (MVP)**:
- App Platform (Basic): $12/month
- PostgreSQL (1GB RAM): $15/month
- Redis (1GB RAM): $15/month
- Spaces (250GB): $5/month
- **Total**: ~$47/month

**Alternatywy**:
- **AWS**: More features, ale droższe i complex setup
- **Vercel/Netlify**: Great dla frontend, ale limited backend options
- **Verdict**: DigitalOcean sweet spot dla MVP (cost vs features)

---

### 4.2 CI/CD: GitHub Actions

**Workflow Example**:
```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run test
      - run: npm run lint

  build-frontend:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-artifact@v3
        with:
          name: frontend-build
          path: dist/

  deploy-backend:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: digitalocean/action-doctl@v2
        with:
          token: ${{ secrets.DIGITALOCEAN_TOKEN }}
      - run: doctl apps create-deployment ${{ secrets.APP_ID }}

  deploy-frontend:
    needs: build-frontend
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v3
        with:
          name: frontend-build
      - run: |
          # Deploy to DigitalOcean Spaces / CDN
          aws s3 sync dist/ s3://thaliana-frontend --delete
```

---

### 4.3 Monitoring & Logging

**Error Tracking: Sentry**
```typescript
// main.ts
import * as Sentry from '@sentry/node';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 0.1, // 10% of transactions
});

app.use(Sentry.Handlers.requestHandler());
app.use(Sentry.Handlers.errorHandler());
```

**Application Performance Monitoring**:
- **NewRelic** (free tier available)
- **DataDog** (comprehensive, ale pricey)
- **Self-hosted**: Prometheus + Grafana

**Logging Strategy**:
```typescript
// common/logger.ts
import { Logger } from '@nestjs/common';
import winston from 'winston';

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
  ],
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple(),
  }));
}

export default logger;
```

---

## 5. Development Tools & Workflow

### 5.1 Essential Tools

| Tool | Purpose | Link |
|------|---------|------|
| **VS Code** | IDE | https://code.visualstudio.com/ |
| **ESLint** | Linting | Configured w project |
| **Prettier** | Code formatting | Auto-format on save |
| **Husky** | Git hooks | Pre-commit linting/testing |
| **lint-staged** | Run linters on staged files | Performance optimization |
| **Prisma Studio** | Database GUI | `npx prisma studio` |
| **Postman** | API testing | Collections dla all endpoints |
| **Storybook** | Component library | UI development isolation |

### 5.2 VS Code Extensions

```json
// .vscode/extensions.json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "prisma.prisma",
    "bradlc.vscode-tailwindcss",
    "formulahendry.auto-rename-tag",
    "christian-kohler.path-intellisense",
    "ms-azuretools.vscode-docker"
  ]
}
```

### 5.3 Package.json Scripts

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "lint:fix": "eslint . --ext ts,tsx --fix",
    "format": "prettier --write \"src/**/*.{ts,tsx,css,md}\"",
    "type-check": "tsc --noEmit",
    "storybook": "storybook dev -p 6006",
    "build-storybook": "storybook build"
  }
}
```

---

## 6. Testing Strategy

### 6.1 Testing Pyramid

```
        ╱╲
       ╱  ╲       E2E Tests (5-10%)
      ╱────╲      Playwright, Cypress
     ╱      ╲
    ╱────────╲    Integration Tests (20-30%)
   ╱          ╲   API tests, component integration
  ╱────────────╲
 ╱              ╲ Unit Tests (60-70%)
╱────────────────╲ Vitest, Jest
```

### 6.2 Frontend Testing

**Unit Tests: Vitest**
```typescript
// components/MeetingCard.test.tsx
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import { MeetingCard } from './MeetingCard';

describe('MeetingCard', () => {
  it('renders meeting title', () => {
    const meeting = {
      id: '1',
      title: 'Kickoff Session',
      scheduledAt: new Date('2026-01-25T10:00:00Z'),
    };

    render(<MeetingCard meeting={meeting} />);

    expect(screen.getByText('Kickoff Session')).toBeInTheDocument();
  });

  it('shows join button dla upcoming meetings', () => {
    // ...test logic
  });
});
```

**Component Tests: React Testing Library**
```typescript
import { render, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

it('creates a new meeting when form is submitted', async () => {
  render(<MeetingForm />);

  await userEvent.type(screen.getByLabelText('Title'), 'New Meeting');
  await userEvent.click(screen.getByRole('button', { name: /create/i }));

  await waitFor(() => {
    expect(screen.getByText('Meeting created successfully')).toBeInTheDocument();
  });
});
```

**E2E Tests: Playwright**
```typescript
// e2e/meetings.spec.ts
import { test, expect } from '@playwright/test';

test('create and view meeting', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="password"]', 'password');
  await page.click('button:has-text("Login")');

  await page.goto('/meetings');
  await page.click('button:has-text("Schedule Meeting")');

  await page.fill('[name="title"]', 'Test Meeting');
  await page.fill('[name="scheduledAt"]', '2026-02-01T10:00');
  await page.click('button:has-text("Create")');

  await expect(page.locator('text=Test Meeting')).toBeVisible();
});
```

### 6.3 Backend Testing

**Unit Tests**:
```typescript
// meetings/meetings.service.spec.ts
import { Test } from '@nestjs/testing';
import { MeetingsService } from './meetings.service';
import { PrismaService } from '../database/prisma.service';

describe('MeetingsService', () => {
  let service: MeetingsService;
  let prisma: PrismaService;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [MeetingsService, PrismaService],
    }).compile();

    service = module.get(MeetingsService);
    prisma = module.get(PrismaService);
  });

  it('creates a meeting', async () => {
    const mockMeeting = { id: '1', title: 'Test' };
    jest.spyOn(prisma.meeting, 'create').mockResolvedValue(mockMeeting as any);

    const result = await service.create({ title: 'Test', /* ... */ });

    expect(result).toEqual(mockMeeting);
  });
});
```

**Integration Tests (E2E)**:
```typescript
// test/meetings.e2e-spec.ts
import { Test } from '@nestjs/testing';
import { INestApplication } from '@nestjs/common';
import * as request from 'supertest';
import { AppModule } from '../src/app.module';

describe('Meetings (e2e)', () => {
  let app: INestApplication;
  let authToken: string;

  beforeAll(async () => {
    const moduleFixture = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    await app.init();

    // Login to get auth token
    const loginRes = await request(app.getHttpServer())
      .post('/auth/login')
      .send({ email: 'test@example.com', password: 'password' });

    authToken = loginRes.body.accessToken;
  });

  it('/meetings (POST)', () => {
    return request(app.getHttpServer())
      .post('/meetings')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        title: 'Test Meeting',
        scheduledAt: new Date(),
        duration: 60,
      })
      .expect(201)
      .expect((res) => {
        expect(res.body.id).toBeDefined();
        expect(res.body.title).toBe('Test Meeting');
      });
  });
});
```

---

## 7. Security Considerations

### 7.1 OWASP Top 10 Mitigations

| Vulnerability | Mitigation |
|---------------|------------|
| **Injection** | Parameterized queries (Prisma), input validation (Zod) |
| **Broken Auth** | JWT rotation, 2FA, bcrypt hashing, rate limiting |
| **Sensitive Data** | HTTPS everywhere, encryption at rest, field-level encryption |
| **XXE** | Disable XML parsing unless needed, validate uploads |
| **Broken Access Control** | RBAC, ownership checks, audit logs |
| **Security Misconfiguration** | Environment variables, security headers, CSP |
| **XSS** | React auto-escaping, DOMPurify dla rich text, CSP headers |
| **Insecure Deserialization** | Avoid eval(), validate JSON schema |
| **Using Components with Known Vulnerabilities** | Dependabot, npm audit, Snyk |
| **Insufficient Logging** | Winston logging, Sentry monitoring, audit trail |

### 7.2 Security Headers

```typescript
// main.ts
import helmet from 'helmet';

app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'", 'https://fonts.googleapis.com'],
      fontSrc: ["'self'", 'https://fonts.gstatic.com'],
      imgSrc: ["'self'", 'data:', 'https:'],
      scriptSrc: ["'self'"],
      connectSrc: ["'self'", 'https://api.thaliana.app'],
    },
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true,
  },
}));

app.enableCors({
  origin: process.env.FRONTEND_URL,
  credentials: true,
});
```

---

## 8. Performance Optimization

### 8.1 Frontend Optimizations

- **Code splitting**: Route-based + component lazy loading
- **Bundle analysis**: `vite-plugin-visualizer`
- **Image optimization**: WebP format, lazy loading, responsive images
- **Caching**: Service Worker (PWA), HTTP caching headers
- **CDN**: Static assets served from CDN
- **Minification**: Vite handles automatically

### 8.2 Backend Optimizations

- **Database indexes**: Critical queries indexed
- **Query optimization**: EXPLAIN ANALYZE, N+1 problem avoided
- **Caching**: Redis dla frequently accessed data
- **Connection pooling**: Prisma connection pool configured
- **Compression**: gzip/brotli responses
- **Rate limiting**: Prevent abuse

---

**Wersja**: 1.0
**Status**: Draft
**Ostatnia aktualizacja**: 2026-01-18
