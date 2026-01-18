# UX/UI Design - Thaliana Mentoring Platform

## 1. Design Philosophy & Principles

### 1.1 Core Design Values

**Clarity over Cleverness**
- Intuicyjne interfejsy bez potrzeby szkoleń
- Jasna hierarchia informacji
- Przewidywalne interakcje

**Productivity-Focused**
- Minimalizacja kliknięć do wykonania zadań
- Keyboard shortcuts dla power users
- Bulk actions gdzie sensowne
- Quick actions (floating buttons, context menus)

**Human-Centered**
- Ciepłe, przyjaźne UI (nie zimne korporacyjne)
- Mikro-interakcje dodające "życia"
- Helpful empty states
- Encouraging feedback messages

**Data Visualization**
- Wizualizacje wspierające decision-making
- Progress indicators motywujące do działania
- Timeline views pokazujące journey

### 1.2 Design System Foundation

**Color Palette**
```
Primary (Brand):
  - Space Blue #0A2463 (dark, space-themed)
  - Clean Green #10B981 (cleantech, growth)
  - Accent Orange #F59E0B (energy, action)

Neutrals:
  - Gray 50-900 (Tailwind scale)
  - White #FFFFFF
  - Black #0F172A

Semantic Colors:
  - Success: #10B981 (Green)
  - Warning: #F59E0B (Amber)
  - Error: #EF4444 (Red)
  - Info: #3B82F6 (Blue)
```

**Typography**
```
Headings: Inter (Sans-serif, modern, readable)
  - H1: 32px / 600 weight
  - H2: 24px / 600 weight
  - H3: 20px / 600 weight
  - H4: 18px / 500 weight

Body: Inter
  - Regular: 16px / 400 weight / 1.6 line-height
  - Small: 14px / 400 weight
  - Tiny: 12px / 400 weight

Monospace: JetBrains Mono (dla dat, kodów, technical info)
```

**Spacing Scale (Tailwind)**
- Base unit: 4px
- Scale: 4, 8, 12, 16, 24, 32, 48, 64, 96

**Border Radius**
- Small: 4px (buttons, inputs)
- Medium: 8px (cards, modals)
- Large: 12px (hero elements)
- Round: 9999px (avatars, badges)

**Shadows**
```
sm:  0 1px 2px 0 rgb(0 0 0 / 0.05)
md:  0 4px 6px -1px rgb(0 0 0 / 0.1)
lg:  0 10px 15px -3px rgb(0 0 0 / 0.1)
xl:  0 20px 25px -5px rgb(0 0 0 / 0.1)
```

---

## 2. Information Architecture & Navigation

### 2.1 Navigation Structure

```
┌─────────────────────────────────────────────────────────┐
│  [Logo] Thaliana Mentoring                              │
│                                                          │
│  📊 Dashboard    📅 Meetings    🎯 Goals    📚 Knowledge│
│                                                          │
│                                    [🔔 Notifications]   │
│                                    [👤 Profile Menu]    │
└─────────────────────────────────────────────────────────┘
```

**Sidebar Navigation (Desktop)**
```
╔═══════════════════╗
║ [Logo]            ║
║                   ║
║ 📊 Dashboard      ║
║ 👥 Relationships  ║  ← tylko dla mentorów z >1 mentee
║ 📅 Meetings       ║
║ 🎯 Goals & OKRs   ║
║ ✅ Action Items   ║
║ 📚 Knowledge Base ║
║                   ║
║ ─────────────     ║
║ ⚙️  Settings      ║
║ 👤 Profile        ║
╚═══════════════════╝
```

**Mobile Navigation (Bottom Tab Bar)**
```
┌───────────────────────────────────────┐
│                                       │
│        [Main Content Area]            │
│                                       │
└───────────────────────────────────────┘
┌───────────────────────────────────────┐
│  📊      📅       🎯       📚      ⚙️  │
│  Home   Meetings  Goals   Resources  More│
└───────────────────────────────────────┘
```

### 2.2 User Flows

**Flow 1: Scheduling a Meeting**
```
Dashboard
  ↓ [Click "Schedule Meeting"]
Meeting Form Modal
  ├─ Select Date & Time (Calendar widget)
  ├─ Set Duration (Dropdown: 30/60/90 min)
  ├─ Add Title
  ├─ Add Location (text or Zoom link)
  ├─ (Optional) Pre-fill Agenda
  ↓ [Click "Create Meeting"]
Confirmation
  ├─ Show success message
  ├─ Add to calendar
  └─ Send email to participants
```

**Flow 2: Updating Goal Progress**
```
Dashboard / Goals Page
  ↓ [Click on Objective Card]
Objective Detail View
  ├─ See all Key Results
  ↓ [Click "Update Progress" on KR]
Quick Update Modal
  ├─ Current Value: [slider or number input]
  ├─ Note (optional): [textarea]
  ↓ [Click "Save Update"]
Updated View
  ├─ Chart animates to new value
  ├─ Timeline shows new data point
  └─ Celebration animation if achieved! 🎉
```

**Flow 3: Adding a Resource**
```
Knowledge Base
  ↓ [Click "+ Add Resource" FAB]
Resource Type Selection
  ├─ 📎 Link
  ├─ 📄 Document
  ├─ 📝 Note
  ├─ 📚 Book
  ↓ [Select Type]
Resource Form
  ├─ Title
  ├─ Description
  ├─ Category (dropdown)
  ├─ Tags (multi-select)
  ├─ Visibility (Private/Shared)
  ├─ Link to Meeting/Goal (optional)
  ↓ [Click "Add Resource"]
Success + Return to KB with new item highlighted
```

---

## 3. Screen Designs & Wireframes

### 3.1 Dashboard (Mentee View)

```
┌────────────────────────────────────────────────────────────────┐
│  Thaliana Mentoring                  🔔(3)  [👤 Tomasz Nowak] │
├────────────────────────────────────────────────────────────────┤
│  📊 Dashboard    📅 Meetings    🎯 Goals    📚 Knowledge       │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Welcome back, Tomasz! 👋                                      │
│  You have 1 meeting this week and 3 action items due.          │
│                                                                 │
│  ┌─────────────────────────────┐  ┌────────────────────────┐  │
│  │  📅 Upcoming Meetings       │  │  ✅ Action Items (3)   │  │
│  ├─────────────────────────────┤  ├────────────────────────┤  │
│  │  🗓  Thu, Jan 25 • 10:00 AM │  │  ☐ Prepare pitch deck │  │
│  │  Kickoff Session            │  │     Due: Jan 22        │  │
│  │  with Anna Kowalska         │  │                        │  │
│  │                             │  │  ☐ Contact 3 investors│  │
│  │  📋 View Agenda  [Join Now] │  │     Due: Jan 25        │  │
│  │                             │  │                        │  │
│  │  ─────────────────────      │  │  ☑ Update financial   │  │
│  │  No more meetings this week │  │     model (Done)       │  │
│  └─────────────────────────────┘  └────────────────────────┘  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  🎯 Active Goals Progress                                │  │
│  ├──────────────────────────────────────────────────────────┤  │
│  │  💰 Przygotowanie rundy Seed (500k EUR)                 │  │
│  │  ████████░░░░░░░░░░░░ 40%                    Critical   │  │
│  │  Target: Jun 30, 2026                                    │  │
│  │                                                           │  │
│  │  📱 Product Development Milestone                        │  │
│  │  ████████████████░░░░ 75%                    On Track   │  │
│  │  Target: Mar 31, 2026                                    │  │
│  │                                                           │  │
│  │  [View All Goals →]                                      │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌─────────────────────────────┐  ┌────────────────────────┐  │
│  │  📚 Recent Resources        │  │  📈 Your Journey       │  │
│  ├─────────────────────────────┤  ├────────────────────────┤  │
│  │  📄 Term Sheet Template     │  │  [Timeline Chart]      │  │
│  │     Added by Anna • Jan 18  │  │                        │  │
│  │                             │  │  5 meetings completed  │  │
│  │  📎 Top 10 Space VCs        │  │  2 goals achieved      │  │
│  │     Added by Anna • Jan 15  │  │  12 action items done  │  │
│  │                             │  │                        │  │
│  │  [View All Resources →]    │  │  Since: Jan 1, 2026    │  │
│  └─────────────────────────────┘  └────────────────────────┘  │
│                                                                 │
│                                                  [+ Quick Add] │
└────────────────────────────────────────────────────────────────┘
```

**Key Features**:
- **Personalized greeting** z kontekstem aktualnego stanu
- **Cards-based layout** dla łatwego scanowania
- **Action-oriented** - każda sekcja ma CTA
- **Progress visualization** z color coding (Critical/On Track/At Risk)
- **Quick Add FAB** (Floating Action Button) dla szybkiego dostępu

---

### 3.2 Dashboard (Mentor View)

```
┌────────────────────────────────────────────────────────────────┐
│  Thaliana Mentoring                  🔔(2)  [👤 Anna Kowalska]│
├────────────────────────────────────────────────────────────────┤
│  📊 Dashboard    👥 Mentees    📅 Meetings    📚 Knowledge     │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Hi Anna! 👋 Here's your mentoring overview.                   │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  👥 Active Mentees (2)                                   │  │
│  ├──────────────────────────────────────────────────────────┤  │
│  │  ┌──────────────────────┐  ┌──────────────────────┐     │  │
│  │  │ [👤] Tomasz Nowak    │  │ [👤] Maria Schmidt   │     │  │
│  │  │ Thaliana Space       │  │ EcoSat Solutions     │     │  │
│  │  ├──────────────────────┤  ├──────────────────────┤     │  │
│  │  │ Next: Thu 10:00 AM  │  │ Next: Mon 2:00 PM    │     │  │
│  │  │ 🎯 3 goals active    │  │ 🎯 2 goals active    │     │  │
│  │  │ ⚠️ 1 goal at risk    │  │ ✅ All on track      │     │  │
│  │  │                      │  │                      │     │  │
│  │  │ [View Details]       │  │ [View Details]       │     │  │
│  │  └──────────────────────┘  └──────────────────────┘     │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  📅 Upcoming Meetings                                     │  │
│  ├──────────────────────────────────────────────────────────┤  │
│  │  🗓  Thu, Jan 25 • 10:00-11:00                           │  │
│  │     Kickoff Session - Tomasz Nowak                       │  │
│  │     📋 Agenda ready  💬 3 action items pending           │  │
│  │     [Prepare] [Join]                                     │  │
│  │                                                           │  │
│  │  🗓  Mon, Jan 29 • 14:00-15:00                           │  │
│  │     Q1 Planning - Maria Schmidt                          │  │
│  │     ⚠️ No agenda yet                                     │  │
│  │     [Add Agenda] [View Details]                          │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌────────────────────────────┐  ┌─────────────────────────┐  │
│  │  ✅ Items Needing Attention│  │  📊 Your Impact          │  │
│  ├────────────────────────────┤  ├─────────────────────────┤  │
│  │  ⚠️ Goal "Seed Round" at  │  │  This Month:            │  │
│  │     risk - Tomasz          │  │                         │  │
│  │                            │  │  🎯 2 goals achieved    │  │
│  │  📝 Add notes from last    │  │  📅 6 meetings held     │  │
│  │     meeting - Maria        │  │  📚 8 resources shared  │  │
│  │                            │  │                         │  │
│  │  [View All →]              │  │  Total Mentees: 2       │  │
│  └────────────────────────────┘  └─────────────────────────┘  │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

**Różnice od Mentee View**:
- **Overview wszystkich mentees** z quick status
- **Attention items** - rzeczy wymagające reakcji
- **Impact metrics** - pokazanie wartości swojej pracy
- **Preparation helpers** - przypomnienia o agendach

---

### 3.3 Meeting Detail Page

```
┌────────────────────────────────────────────────────────────────┐
│  ← Back to Meetings                              [⋮ More]      │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Kickoff Session - Poznanie celów i oczekiwań                  │
│  🗓  Thursday, January 25, 2026 • 10:00-11:00 AM               │
│  📍 https://zoom.us/j/xxxxx                     [Copy Link]    │
│  👥 Anna Kowalska (Mentor), Tomasz Nowak (Mentee)              │
│                                                                 │
│  ┌─[ Tabs ]────────────────────────────────────────────────┐   │
│  │  [📋 Agenda]  [📝 Notes]  [✅ Action Items]  [🔗 Links] │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ╔════════════════════════════════════════════════════════╗   │
│  ║  📋 Agenda (45 min planned)                            ║   │
│  ╠════════════════════════════════════════════════════════╣   │
│  ║  ☑ 1. Wprowadzenie i poznanie (15 min)                ║   │
│  ║     Brief intro, background, expectations              ║   │
│  ║                                                         ║   │
│  ║  ☐ 2. Omówienie celów Thaliana Space (20 min)         ║   │
│  ║     Current status, challenges, fundraising goals      ║   │
│  ║                                                         ║   │
│  ║  ☐ 3. Ustalenie priorytetów i planu działania (10 min)║   │
│  ║     Next steps, frequency, communication channels      ║   │
│  ║                                                         ║   │
│  ║  [+ Add Agenda Item]                                   ║   │
│  ╚════════════════════════════════════════════════════════╝   │
│                                                                 │
│  ╔════════════════════════════════════════════════════════╗   │
│  ║  📝 Meeting Notes                                      ║   │
│  ╠════════════════════════════════════════════════════════╣   │
│  ║  [Rich Text Editor]                                    ║   │
│  ║                                                         ║   │
│  ║  Key Takeaways:                                        ║   │
│  ║  • Thaliana needs to close seed round by Q2            ║   │
│  ║  • Main challenge: limited traction data              ║   │
│  ║  • Focus: pitch deck + investor intros                 ║   │
│  ║                                                         ║   │
│  ║  Next Steps:                                           ║   │
│  ║  • Anna to review pitch deck draft                     ║   │
│  ║  • Tomasz to prepare financials                        ║   │
│  ║                                                         ║   │
│  ║  💡 Tip: Use @ to mention, # to tag, [] for tasks     ║   │
│  ╚════════════════════════════════════════════════════════╝   │
│                                                                 │
│  [💾 Auto-saved 2 min ago]                    [Mark Complete] │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

**Key Features**:
- **Tabs dla organizacji** - Agenda, Notes, Action Items, Links
- **Checkboxes w agendzie** - tracking progress przez meeting
- **Rich text editor** z markdown support i helpful shortcuts
- **Auto-save** - nigdy nie tracisz pracy
- **Quick actions** - @ mentions, # tags, [] tasks

---

### 3.4 Goals & OKR Page

```
┌────────────────────────────────────────────────────────────────┐
│  🎯 Goals & OKRs                               [+ New Objective]│
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Filters: [Active ▼] [All Categories ▼] [All Priorities ▼]   │
│  Sort by: [Target Date ▼]                                      │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  💰 Przygotowanie i zamknięcie rundy Seed (500k EUR)    │  │
│  │  ─────────────────────────────────────────────────────── │  │
│  │  Category: Funding    Priority: 🔴 Critical             │  │
│  │  Timeline: Jan 15 - Jun 30, 2026                         │  │
│  │                                                           │  │
│  │  Overall Progress:  ████████░░░░░░░░░░░░ 40%            │  │
│  │                                                           │  │
│  │  Key Results:                                            │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │ ☑ Pitch deck i financial model gotowe              │  │  │
│  │  │   ██████████████████████ 100%            ✅ Done   │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │                                                           │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │ ☐ 20 spotkań z inwestorami                         │  │  │
│  │  │   ██░░░░░░░░░░░░░░░░░░░░ 3/20          📈 On Track│  │  │
│  │  │   [Update Progress]                                │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │                                                           │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │ ☐ Minimum 3 term sheets                            │  │  │
│  │  │   ░░░░░░░░░░░░░░░░░░░░░░ 0/3           ⏸ Not Started│  │  │
│  │  │   [Update Progress]                                │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │                                                           │  │
│  │  📎 Linked: 2 meetings, 5 action items                  │  │
│  │  [View Details]  [Edit]  [Add Reflection]               │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  📱 Product Development Milestone                        │  │
│  │  ─────────────────────────────────────────────────────── │  │
│  │  Category: Technology    Priority: 🟡 High              │  │
│  │  Timeline: Jan 1 - Mar 31, 2026                          │  │
│  │                                                           │  │
│  │  Overall Progress:  ████████████████░░░░ 75%            │  │
│  │  [Expand to see Key Results ▼]                           │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌─────────────────────────────────┐                          │
│  │  📊 Analytics                   │                          │
│  │  ─────────────────────────────  │                          │
│  │  Total Objectives: 5            │                          │
│  │  Active: 3                      │                          │
│  │  Achieved: 2 (40% success rate) │                          │
│  │  At Risk: 1                     │                          │
│  │                                  │                          │
│  │  [View Detailed Analytics →]    │                          │
│  └─────────────────────────────────┘                          │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

**Key Features**:
- **Visual progress bars** z color coding
- **Expandable/collapsible cards** - detail on demand
- **Quick update buttons** - minimize friction
- **Linked resources** - context w jednym miejscu
- **Analytics sidebar** - big picture view
- **Status badges** z emoji dla szybkiego rozpoznania

---

### 3.5 Knowledge Base / Resources

```
┌────────────────────────────────────────────────────────────────┐
│  📚 Knowledge Base                         [🔍 Search] [Filter]│
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Categories:                                                    │
│  [All] [Fundraising] [Product] [Space Industry] [Team] [Other]│
│                                                                 │
│  ┌────────────────────────────────────────────────────────┐    │
│  │  Search resources, documents, notes...                 │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  📄 Term Sheet Template                                  │  │
│  │  ───────────────────────────────────────────────────────  │  │
│  │  Standardowy szablon term sheet dla rund seed w EU.      │  │
│  │  Includes: valuation, vesting, board seats, liquidation   │  │
│  │                                                           │  │
│  │  📁 Fundraising  🏷️ template, legal, seed                │  │
│  │  👤 Added by Anna Kowalska • Jan 18, 2026                │  │
│  │  🔗 Linked to: Goal "Seed Round"                         │  │
│  │                                                           │  │
│  │  [📥 Download]  [🔗 Open Link]  [✏️ Edit]               │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  📎 Top 10 Space Tech VCs in Europe                     │  │
│  │  ───────────────────────────────────────────────────────  │  │
│  │  Curated list with contact info, investment thesis,      │  │
│  │  typical check sizes, and portfolio companies.           │  │
│  │                                                           │  │
│  │  📁 Fundraising  🏷️ investors, contacts                  │  │
│  │  👤 Added by Anna Kowalska • Jan 15, 2026                │  │
│  │  🔗 Linked to: Meeting "Kickoff Session"                 │  │
│  │                                                           │  │
│  │  [📥 Download]  [🔗 Open Link]                           │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  💡 Key Insight: Product-Market Fit Indicators          │  │
│  │  ───────────────────────────────────────────────────────  │  │
│  │  Dyskusja z Anna o tym, jak mierzyć PMF w space-tech:   │  │
│  │  1. LOIs (Letters of Intent) od customers               │  │
│  │  2. Pilot programs z enterprise clients                 │  │
│  │  3. Retention rate > 80% after 6 months                  │  │
│  │                                                           │  │
│  │  📁 Product  🏷️ pmf, strategy, metrics                   │  │
│  │  👤 Created by Tomasz Nowak • Jan 20, 2026               │  │
│  │  ⭐ Importance: High                                      │  │
│  │                                                           │  │
│  │  [✏️ Edit]  [🔗 Share]                                   │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│                                                   [+ Add FAB]  │
└────────────────────────────────────────────────────────────────┘
```

**Key Features**:
- **Rich cards** z preview content
- **Smart categorization** i tagging
- **Linked entities** - pokazanie kontekstu
- **Different resource types** z odpowiednimi ikonami
- **Importance markers** dla insights
- **Quick actions** - download, open, edit

---

## 4. Mobile UI Adaptations

### 4.1 Mobile Dashboard (Mentee)

```
┌─────────────────────────┐
│  ☰  Thaliana      🔔(3) │
├─────────────────────────┤
│                         │
│  Welcome, Tomasz! 👋    │
│                         │
│ ┌─────────────────────┐ │
│ │ 📅 Next Meeting     │ │
│ │                     │ │
│ │ Thu, Jan 25         │ │
│ │ 10:00 AM            │ │
│ │                     │ │
│ │ Kickoff Session     │ │
│ │ with Anna Kowalska  │ │
│ │                     │ │
│ │ [View Agenda]       │ │
│ │ [Join Now]          │ │
│ └─────────────────────┘ │
│                         │
│ ┌─────────────────────┐ │
│ │ ✅ Action Items (3) │ │
│ │                     │ │
│ │ ☐ Prepare pitch deck│ │
│ │   Due: Jan 22       │ │
│ │                     │ │
│ │ ☐ Contact investors│ │
│ │   Due: Jan 25       │ │
│ │                     │ │
│ │ [View All →]        │ │
│ └─────────────────────┘ │
│                         │
│ ┌─────────────────────┐ │
│ │ 🎯 Goals Progress   │ │
│ │                     │ │
│ │ Seed Round          │ │
│ │ ████░░░░░ 40%       │ │
│ │                     │ │
│ │ Product Milestone   │ │
│ │ ███████░░ 75%       │ │
│ │                     │ │
│ │ [View All →]        │ │
│ └─────────────────────┘ │
│                         │
│              [+]        │
│                         │
├─────────────────────────┤
│ 📊   📅   🎯   📚   ⋮  │
│Home  Meet Goals KB  More│
└─────────────────────────┘
```

**Mobile-Specific Patterns**:
- **Single column layout** - vertical scrolling
- **Larger touch targets** (min 44x44 pt)
- **Swipe gestures** - swipe right to mark action item done
- **Collapsible sections** - accordion pattern dla savings space
- **Bottom navigation** - thumb-friendly
- **FAB** dla quick add (right thumb zone)

### 4.2 Mobile Patterns Library

**Pull-to-Refresh**
- Na wszystkich list views (Meetings, Goals, Resources)

**Swipe Actions**
```
Action Item Card
  Swipe Left  → [✓ Complete] [🗑 Delete]
  Swipe Right → [✏️ Edit]

Meeting Card
  Swipe Left  → [📝 Add Notes] [✗ Cancel]
```

**Bottom Sheet Modals**
- Używaj bottom sheets zamiast full-screen modals dla:
  - Quick updates (goal progress)
  - Filters
  - Action menus

**Offline Mode Indicator**
```
┌─────────────────────────┐
│  ⚠️ Offline Mode        │
│  Changes will sync when │
│  you're back online     │
└─────────────────────────┘
```

---

## 5. Component Library

### 5.1 Buttons

```
Primary Button:
┌──────────────┐
│ Create Goal  │  ← bg: primary blue, text: white, rounded-md
└──────────────┘

Secondary Button:
┌──────────────┐
│   Cancel     │  ← bg: gray-100, text: gray-700, border: gray-300
└──────────────┘

Ghost Button:
  View Details    ← no background, text: primary, hover: bg-gray-50

Icon Button:
  [⋮]             ← circular, gray hover state
```

### 5.2 Cards

```
Standard Card:
┌────────────────────────────┐
│  [Icon] Card Title         │
│  ─────────────────────────  │
│  Card content goes here... │
│  Can be multi-line.        │
│                            │
│  [Action Button]           │
└────────────────────────────┘
  ↑ shadow-md, rounded-lg, padding: 16-24px

Elevated Card (hover state):
  shadow-md → shadow-lg
  transform: translateY(-2px)
  transition: 150ms
```

### 5.3 Forms

```
Input Field:
┌────────────────────────────┐
│ Label                      │
│ ┌────────────────────────┐ │
│ │ Placeholder text       │ │
│ └────────────────────────┘ │
│ Helper text / error        │
└────────────────────────────┘

Select Dropdown:
┌────────────────────────────┐
│ Category                   │
│ ┌────────────────────────┐ │
│ │ Select option...     ▼ │ │
│ └────────────────────────┘ │
└────────────────────────────┘

Textarea:
┌────────────────────────────┐
│ Description                │
│ ┌────────────────────────┐ │
│ │                        │ │
│ │                        │ │
│ │                        │ │
│ └────────────────────────┘ │
│ 0/500 characters           │
└────────────────────────────┘

Date Picker:
┌────────────────────────────┐
│ Meeting Date               │
│ ┌────────────────────────┐ │
│ │ Jan 25, 2026       📅  │ │ ← opens calendar modal
│ └────────────────────────┘ │
└────────────────────────────┘
```

### 5.4 Progress Indicators

```
Progress Bar:
████████████░░░░░░░░░░░░ 60%
  ↑ dynamic color: green (>70%), yellow (30-70%), red (<30%)

Circular Progress (dla KPIs w cards):
     ╱─────╲
   │   75%   │
    ╲─────╱
  ↑ ring chart, animated

Step Indicator:
●━━━━○━━━━○━━━━○
1    2    3    4
  ↑ dla onboarding, multi-step forms
```

### 5.5 Badges & Tags

```
Status Badge:
[✓ Active]     - green background
[⏸ Paused]     - yellow background
[✗ Cancelled]  - red background
[✓ Completed]  - blue background

Priority Badge:
[🔴 Critical]
[🟡 High]
[🟢 Medium]
[⚪ Low]

Tag:
[#fundraising] [#product] [#team]
  ↑ small, rounded-full, gray-200 bg, clickable
```

### 5.6 Notifications & Alerts

```
Success Toast:
┌─────────────────────────────────┐
│ ✓ Meeting created successfully! │  ← green bg, auto-dismiss 3s
└─────────────────────────────────┘

Error Toast:
┌─────────────────────────────────┐
│ ✗ Failed to save. Please retry. │  ← red bg, manual dismiss
└─────────────────────────────────┘

Info Banner:
┌──────────────────────────────────────────┐
│ ℹ️ Your next meeting is in 1 hour.       │
│    [Prepare Agenda]               [✕]   │
└──────────────────────────────────────────┘
  ↑ blue bg, sticky at top

Warning Alert:
┌──────────────────────────────────────────┐
│ ⚠️ Goal "Seed Round" is at risk.         │
│    No progress in 14 days.               │
│    [Update Progress]              [✕]   │
└──────────────────────────────────────────┘
  ↑ yellow bg
```

---

## 6. Interactions & Animations

### 6.1 Micro-interactions

**Button Click**
- Scale: 0.98 on press
- Duration: 100ms
- Ease: ease-out

**Card Hover (Desktop)**
- Elevation increase (shadow-md → shadow-lg)
- Slight lift (translateY: -2px)
- Border highlight (optional)
- Duration: 150ms

**Goal Progress Update**
- Animated counter (0 → new value)
- Progress bar fills smoothly
- Confetti animation if 100% achieved! 🎉
- Duration: 500ms, ease-out

**Checkbox/Action Item Toggle**
- Checkmark scales in
- Strikethrough animation for completed text
- Subtle color change
- Duration: 200ms

**Loading States**
- Skeleton screens for cards
- Shimmer effect
- Spinner for buttons (inline, doesn't change layout)

### 6.2 Page Transitions

**Route Changes**
- Fade transition: 150ms
- Preserve scroll position on back navigation

**Modal/Dialog Open**
- Backdrop fade in: 200ms
- Content scale in: 250ms, ease-out
- Spring animation dla modals (opcjonalnie)

**Drawer/Sidebar**
- Slide in from left/right: 300ms, ease-in-out

### 6.3 Empty States

**No Meetings Yet**
```
┌─────────────────────────┐
│                         │
│      📅                 │
│                         │
│  No meetings scheduled  │
│  yet.                   │
│                         │
│  Schedule your first    │
│  mentoring session!     │
│                         │
│  [+ Schedule Meeting]   │
│                         │
└─────────────────────────┘
```

**No Goals Yet**
```
┌─────────────────────────┐
│      🎯                 │
│                         │
│  Set your first goal!   │
│                         │
│  Define objectives and  │
│  track your progress.   │
│                         │
│  [+ Create Goal]        │
└─────────────────────────┘
```

**Empty Search Results**
```
┌─────────────────────────┐
│      🔍                 │
│                         │
│  No results found for   │
│  "your search term"     │
│                         │
│  Try different keywords │
│  or filters.            │
└─────────────────────────┘
```

---

## 7. Accessibility (a11y)

### 7.1 WCAG 2.1 AA Compliance

**Color Contrast**
- Text: Minimum 4.5:1 ratio
- Large text (18pt+): Minimum 3:1 ratio
- Interactive elements: Minimum 3:1 ratio

**Keyboard Navigation**
- All interactive elements keyboard accessible
- Visible focus indicators (2px outline, primary color)
- Logical tab order
- Skip to main content link

**Screen Reader Support**
- Semantic HTML (headings, landmarks, lists)
- ARIA labels gdzie potrzebne
- Alt text dla obrazów
- Live regions dla dynamic content (toasts, updates)

**Example ARIA Implementation**:
```html
<!-- Button with icon -->
<button aria-label="Delete meeting" title="Delete">
  <TrashIcon aria-hidden="true" />
</button>

<!-- Progress bar -->
<div role="progressbar"
     aria-valuenow="60"
     aria-valuemin="0"
     aria-valuemax="100"
     aria-label="Goal progress: 60%">
  <div style="width: 60%"></div>
</div>

<!-- Live region dla notifications -->
<div role="status" aria-live="polite" aria-atomic="true">
  Meeting created successfully!
</div>
```

### 7.2 Responsive Font Sizes

```css
/* Fluid typography */
html {
  font-size: clamp(14px, 1vw, 16px);
}

h1 {
  font-size: clamp(24px, 4vw, 32px);
}
```

### 7.3 Reduced Motion Support

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 8. Dark Mode (Phase 2)

```css
/* Color palette adjustments */
:root[data-theme="dark"] {
  --bg-primary: #0F172A;
  --bg-secondary: #1E293B;
  --bg-tertiary: #334155;

  --text-primary: #F1F5F9;
  --text-secondary: #CBD5E1;

  --border-color: #334155;

  /* Adjust brand colors dla dark mode */
  --space-blue: #3B82F6;  /* lighter than light mode */
  --clean-green: #34D399;
}
```

**Toggle Implementation**:
```
Settings Page:
┌────────────────────────┐
│ Appearance             │
│                        │
│ ○ Light                │
│ ● Dark                 │
│ ○ System (auto)        │
└────────────────────────┘
```

---

## 9. Design Checklist

### Pre-Development
- [ ] User flows validated z stakeholderami
- [ ] Wireframes approved
- [ ] High-fidelity mockups w Figma
- [ ] Interactive prototype dla user testing
- [ ] Accessibility audit on designs
- [ ] Mobile + Desktop views defined

### Development Phase
- [ ] Design system components w Storybook
- [ ] Responsive breakpoints tested
- [ ] Cross-browser compatibility (Chrome, Firefox, Safari, Edge)
- [ ] Dark mode implemented (Phase 2)
- [ ] Performance budget met (Lighthouse score >90)

### Pre-Launch
- [ ] User acceptance testing completed
- [ ] Accessibility testing (axe DevTools, manual testing)
- [ ] Usability testing z real users (5-8 users)
- [ ] Bug bash conducted
- [ ] Analytics events instrumented

---

**Narzędzia Rekomendowane**:
- **Design**: Figma (prototyping, design system)
- **Icons**: Heroicons, Lucide Icons
- **Component Library**: Shadcn/ui (headless, customizable)
- **Animation**: Framer Motion
- **Accessibility Testing**: axe DevTools, WAVE
- **User Testing**: Maze, UserTesting.com

---

**Wersja**: 1.0
**Status**: Draft
**Ostatnia aktualizacja**: 2026-01-18
