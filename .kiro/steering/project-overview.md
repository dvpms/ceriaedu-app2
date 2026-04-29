---
inclusion: always
---

# CeriaEdu — Project Overview

CeriaEdu is an LMS (Learning Management System) platform for learning English and Mathematics, targeting elementary school students in grades 4–6. Built as a thesis project.

## Tech Stack

- **Framework**: Next.js (App Router) with JavaScript
- **Styling**: Tailwind CSS using `@theme` design system
- **ORM**: Prisma v7 with Neon (serverless PostgreSQL)
- **Auth**: NextAuth.js (Credentials provider + Prisma adapter, JWT strategy)
- **Icons**: Lucide-react
- **State Management**: Redux Toolkit + RTK Query
- **Storage**: Cloudinary (S3 method)

## Folder Structure

```
src/
  app/
    (admin)/          # Admin route group
    (auth)/           # Auth route group
    (student)/        # Student route group
  components/
    ui/               # Reusable UI components
    layouts/
      AdminSidebar.jsx
      StudentNavigation.jsx  # Hybrid: Bottombar (mobile) / Topbar (desktop)
  lib/
    redux/
      store.js
      slices/         # Global state slices
      api/            # RTK Query slices
    prisma.js         # Prisma client instance
```

## Core Features

1. **Auth System** — Student & Admin roles
2. **Card-Based Learning** — Materials rendered as slides/cards from JSON
3. **Quizzes** — Multiple choice quizzes per material/subject
4. **Games** — 3 interactive games: Word Arrangement, Word Puzzle, Multiplication & Division Puzzle
5. **Leaderboard** — Ranking based on accumulated points
6. **Gamification** — Points earned per quiz/game via `postActivity` RTK Query

## Material System

- No PDF. Materials use **Card-Based Learning**.
- Each material stored as JSON: `{ title, image, contentArray[] }`
- `contentArray` is rendered as interactive Slide/Card components
- Admin creates/edits card content via JSON editor or structured form

## Game System

- 3 games implemented as pure React components:
  1. **Word Arrangement** (Susun Kata) — drag-and-drop using `@dnd-kit`
  2. **Word Puzzle** (Teka-Teki Kata) — letter grid puzzle
  3. **Multiplication & Division Puzzle** (Puzzle Perkalian & Pembagian) — drag-and-drop using `@dnd-kit`
- Animations via **Framer Motion** for transitions and feedback
- On game completion → call `postActivity` via RTK Query to award points

## Database Schema (Draft)

| Model | Fields |
|---|---|
| User | id, name, email, password, role (STUDENT/ADMIN), points, level |
| Subject | id, title (Math/English), activitiesDescription, icon |
| Material | id, subjectId, title, cards (JSON), order |
| Quiz | id, materialId/subjectId, title, type (MULTIPLE_CHOICE) |
| Question | id, quizId, text, image, points |
| Option | id, questionId, text, isCorrect |
| Submission | id, userId, quizId, score, completedAt |
| ActivityLog | id, userId, type (QUIZ/GAME), activityId, points, createdAt |

## Target Users

- **Students**: SD grade 4–6 (ages ~9–12)
- **Admins**: Teachers / content managers
