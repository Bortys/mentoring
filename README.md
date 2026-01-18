# Thaliana Space - Mentoring Management Platform

> Kompleksowa aplikacja do zarządzania procesem mentoringu dla firm deep-tech, łącząca zarządzanie spotkaniami, system celów OKR oraz podstawowe funkcjonalności CRM.

## 📋 O Projekcie

Thaliana Space to firma działająca w innowacyjnej branży łączącej technologie kosmiczne (space-tech) z rozwiązaniami ekologicznymi (cleantech). Ta aplikacja wspiera długoterminowe relacje mentor-mentee poprzez:

- 📅 **Zarządzanie spotkaniami** - planowanie, agendy, notatki, follow-up
- 🎯 **System celów OKR** - śledzenie celów biznesowych i technicznych
- 📚 **Baza wiedzy** - dokumentowanie insights i zasobów
- ✅ **Action items** - tracking zadań i zobowiązań
- 📊 **Analytics** - wizualizacja postępów i kluczowych wskaźników
- 👥 **CRM relacji** - zarządzanie historią współpracy

## 🏗️ Architektura

### Tech Stack

**Frontend**:
- React 18 + TypeScript
- Vite (build tool)
- Tailwind CSS + Shadcn/ui
- Zustand (state management)
- TanStack Query (data fetching)
- React Router v6

**Backend**:
- NestJS (Node.js framework)
- PostgreSQL (database)
- Prisma (ORM)
- Redis (caching & sessions)
- JWT (authentication)

**Infrastructure**:
- DigitalOcean App Platform
- AWS S3 / MinIO (file storage)
- GitHub Actions (CI/CD)

## 📁 Struktura Projektu

```
.
├── docs/                          # Dokumentacja projektowa
│   ├── PROJECT_OVERVIEW.md        # Przegląd projektu i wizja
│   ├── architecture/
│   │   └── SYSTEM_ARCHITECTURE.md # Architektura systemu
│   ├── data-model/
│   │   └── DATABASE_SCHEMA.md     # Model danych i schema
│   ├── ux-ui/
│   │   └── UI_UX_DESIGN.md        # Design system i wireframes
│   ├── tech-stack/
│   │   └── TECHNOLOGY_RECOMMENDATIONS.md # Rekomendacje technologiczne
│   └── implementation/
│       └── IMPLEMENTATION_PLAN.md  # Plan wdrożenia i timeline
├── src/
│   ├── backend/                   # Kod backend (NestJS)
│   ├── frontend/                  # Kod frontend (React)
│   └── shared/                    # Współdzielone typy i utilities
└── README.md                      # Ten plik
```

## 📚 Dokumentacja

### Kluczowe Dokumenty

1. **[PROJECT_OVERVIEW.md](docs/PROJECT_OVERVIEW.md)**
   - Wizja produktu i problem biznesowy
   - Główne moduły funkcjonalne
   - User personas i user journeys
   - Wymagania funkcjonalne i niefunkcjonalne

2. **[SYSTEM_ARCHITECTURE.md](docs/architecture/SYSTEM_ARCHITECTURE.md)**
   - Architektura 3-tier
   - Szczegóły modułów (Auth, Meetings, Goals, etc.)
   - Integracje zewnętrzne
   - Bezpieczeństwo i skalowalność

3. **[DATABASE_SCHEMA.md](docs/data-model/DATABASE_SCHEMA.md)**
   - ERD (Entity Relationship Diagram)
   - Pełny Prisma schema
   - Indeksy i optymalizacje
   - Strategia backupu

4. **[UI_UX_DESIGN.md](docs/ux-ui/UI_UX_DESIGN.md)**
   - Design philosophy
   - Wireframes (ASCII art)
   - Component library
   - Mobile UI patterns

5. **[TECHNOLOGY_RECOMMENDATIONS.md](docs/tech-stack/TECHNOLOGY_RECOMMENDATIONS.md)**
   - Uzasadnienie wyborów technologicznych
   - Alternatywy rozważone
   - Przykłady implementacji
   - Security best practices

6. **[IMPLEMENTATION_PLAN.md](docs/implementation/IMPLEMENTATION_PLAN.md)**
   - Fazy projektu (Phase 0-3)
   - Sprint planning (MVP)
   - Budget estimation
   - Risk management

## 🚀 Quick Start

### Wymagania

- Node.js 20+
- PostgreSQL 14+
- Redis 7+
- npm lub yarn

### Setup (będzie zaktualizowane po implementacji)

```bash
# Clone repository
git clone https://github.com/thaliana-space/mentoring.git
cd mentoring

# Install dependencies
npm install

# Setup environment variables
cp .env.example .env
# Edit .env with your configuration

# Database setup
npx prisma migrate dev

# Start development servers
npm run dev:backend  # Backend on http://localhost:3000
npm run dev:frontend # Frontend on http://localhost:5173
```

## 🎯 Roadmap

### Phase 0: Discovery & Design ✅ (Completed)
- [x] User research
- [x] Prototyping
- [x] High-fidelity designs
- [x] Technical architecture

### Phase 1: MVP Development 🚧 (In Progress)
- [ ] Sprint 1-2: Authentication & Profiles
- [ ] Sprint 3: Meetings Management
- [ ] Sprint 4: Goals & OKR
- [ ] Sprint 5: Knowledge Base & Action Items
- [ ] Beta Launch

### Phase 2: Enhancement 📋 (Planned)
- [ ] Advanced Analytics
- [ ] Google Calendar Integration
- [ ] In-app Messaging
- [ ] Mobile PWA Features

### Phase 3: Scale 🔮 (Future)
- [ ] Mobile Native App (React Native)
- [ ] AI Features (meeting summaries, insights)
- [ ] Multi-tenant Architecture
- [ ] Advanced ML Analytics

## 👥 Team

**Product Owner**: Thaliana Space Team
**Development**: [Developer Name]
**Design**: [Designer Name]

## 📄 License

Proprietary - © 2026 Thaliana Space. All rights reserved.

## 📞 Contact

- **Email**: mentoring@thaliana.space
- **Website**: https://thaliana.space
- **GitHub Issues**: [Report bugs or request features](https://github.com/thaliana-space/mentoring/issues)

---

**Wersja**: 1.0.0
**Ostatnia aktualizacja**: 2026-01-18
**Status**: Phase 0 Complete, Phase 1 Planning
