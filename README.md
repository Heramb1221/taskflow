# TaskFlow — Real-Time Cross-Platform Task Manager

> A cross-platform task management application built to explore reactive backend-as-a-service architecture, real-time data synchronization, and modern React Native development practices.

---

# About The Project

TaskFlow is a cross-platform mobile application that runs on iOS, Android, and Web from a single TypeScript codebase.

It was built as a hands-on exploration of Convex — a reactive backend-as-a-service that replaces the traditional REST polling model with a server-push, subscription-based architecture.

Rather than building a conventional REST API where the client polls for updates, this project uses Convex's reactive query model:

- The client subscribes to a query
- Whenever the underlying data changes, the server re-executes the query
- Updated results are pushed over WebSocket automatically
- The UI re-renders without manual refetching or stale cache management

The frontend is built with Expo using file-based routing via `expo-router` (similar to Next.js App Router, but for mobile), and runs with React Native's New Architecture enabled, replacing the legacy Bridge with JSI for lower-latency native module communication.

This project is intentionally a learning artifact and prototype — it demonstrates real-time data architecture and cross-platform engineering concepts, but intentionally omits production features such as authentication.

---

# Project Type

Cross-Platform Mobile Application · Real-Time Reactive System · BaaS Architecture Prototype

---

# Project Status

**Experimental Prototype — Actively maintained as a learning artifact.**

---

# Why I Built This

Most todo app tutorials use REST APIs with manual fetch-on-mount logic. I wanted to understand what a genuinely reactive architecture looks like in practice — where the server, not the client, is responsible for notifying consumers of state changes.

## Technical Goals

- Understand Convex's OCC (Optimistic Concurrency Control) transaction model
- Explore file-based routing in mobile applications via `expo-router`
- Build a theming system from scratch using React Context and AsyncStorage
- Run React Native's New Architecture with JSI in a real project
- Understand the trade-offs of targeting iOS, Android, and Web from one codebase

## Engineering Questions Explored

- What does a WebSocket-first data layer look like compared to REST polling?
- How does `expo-router` map filesystem routes to navigation primitives?
- What are the rendering implications of gradient-heavy UI inside virtualized lists?

---

# Features

## Core Features

- Create, edit, delete, and complete todos with optimistic real-time sync
- Progress bar tracking completion percentage
- Bulk reset functionality
- Empty state handling

---

## Engineering Features

- Reactive data subscriptions
- File-based routing via `expo-router`
- TypeScript-first schema generation
- Cross-platform support (iOS / Android / Web)
- React Native New Architecture enabled
- React Compiler experiment enabled
- Dark / Light mode persistence via AsyncStorage

---

## Known Prototype Limitations

- No authentication
- Preferences are partially UI-only
- No offline support

---

# Tech Stack

## Frontend

| Technology | Version | Role |
|---|---|---|
| React Native | 0.81.5 | Cross-platform UI framework |
| Expo | 54 | Managed workflow |
| expo-router | ~6.0.21 | File-based routing |
| expo-linear-gradient | ~15.0.8 | Native gradient rendering |
| TypeScript | ~5.9.2 | Static typing |
| React Navigation | ^7.4.0 | Bottom tabs navigation |

---

## Backend / Data

| Technology | Version | Role |
|---|---|---|
| Convex | ^1.31.2 | BaaS, realtime DB, functions |

---

## State & Persistence

| Technology | Role |
|---|---|
| Convex `useQuery` | Reactive server state |
| React `useState` | Local UI state |
| React Context | Theme management |
| AsyncStorage | Preference persistence |

---

# Architecture

## System Overview

```text
┌─────────────────────────────────────────────────────────────┐
│                      Client (Expo App)                     │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ ConvexProvider (WebSocket connection pool)         │   │
│  │                                                     │   │
│  │  ThemeProvider (Context + AsyncStorage)            │   │
│  │                                                     │   │
│  │   ┌───────────────┐   ┌────────────────────────┐   │   │
│  │   │ Todos Screen  │   │ Settings Screen        │   │   │
│  │   │               │   │                        │   │   │
│  │   │ Header        │   │ ProgressStats          │   │   │
│  │   │ TodoInput     │   │ Preferences            │   │   │
│  │   │ FlatList      │   │ DangerZone             │   │   │
│  │   └───────────────┘   └────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────┘   │
└──────────────────────┬─────────────────────────────────────┘
                       │
                       │ WebSocket subscriptions
                       │ HTTP mutations
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                       Convex Cloud                          │
│                                                             │
│  Query Functions                                            │
│  getTodos → reactive query                                  │
│                                                             │
│  Mutation Functions                                         │
│  addTodo · toggleTodo · updateTodo                          │
│  deleteTodo · clearAllTodos                                 │
│                                                             │
│  Database                                                   │
│  todos { _id, text, isCompleted, _creationTime }            │
└─────────────────────────────────────────────────────────────┘
```

---

## Data Flow — Adding a Todo

```text
User types in TodoInput
    ↓
Local useState updates
    ↓
useMutation(api.todos.addTodo)
    ↓
Convex mutation executes
    ↓
Database insert
    ↓
Convex invalidates subscriptions
    ↓
Updated query pushed over WebSocket
    ↓
Subscribed components re-render
```

---

## Reactive Query Model vs REST

| Concern | REST (Polling) | Convex (Reactive) |
|---|---|---|
| Freshness | Stale until refetch | Push on change |
| Network usage | Constant polling | Event-driven |
| Client complexity | Manual invalidation | Automatic subscriptions |
| Multi-device sync | Requires polling | Instant |

---

# Folder Structure

```text
taskflow-rn/
├── app/
│   ├── _layout.tsx
│   └── (tabs)/
│       ├── _layout.tsx
│       ├── index.tsx
│       └── settings.tsx
│
├── assets/
│   ├── images/
│   └── styles/
│
├── components/
│   ├── DangerZone.tsx
│   ├── EmptyState.tsx
│   ├── Header.tsx
│   ├── LoadingSpinner.tsx
│   ├── Preferences.tsx
│   ├── ProgressStats.tsx
│   └── TodoInput.tsx
│
├── convex/
│   ├── _generated/
│   ├── schema.ts
│   ├── todos.ts
│   └── tsconfig.json
│
├── hooks/
│   └── useTheme.tsx
│
├── app.json
├── tsconfig.json
├── eslint.config.js
└── package.json
```

---

# Installation

## Prerequisites

- Node.js >= 20.x
- npm >= 7
- Expo CLI
- Convex account
- Expo Go or simulator

---

## Setup

```bash
# Clone repository
git clonehttps://github.com/Heramb1221/taskflow

cd taskflow

# Install dependencies
npm install

# Initialize Convex
npx convex dev

# Start Expo
npx expo start
```

---

## Web

```bash
npx expo start --web
```

---

# Environment Variables

Create `.env.local`:

```env
EXPO_PUBLIC_CONVEX_URL=https://your-deployment.convex.cloud
```

---

# Usage

## Adding a Todo

Type into the input field and press Return or the `+` button.

---

## Editing a Todo

Tap the pencil icon to switch into inline edit mode.

---

## Completing a Todo

Tap the checkbox icon. Progress statistics update reactively.

---

## Deleting a Todo

Tap the trash icon and confirm deletion.

---

## Bulk Reset

Navigate to:

```text
Settings → Danger Zone → Reset App
```

---

## Dark Mode

Navigate to:

```text
Settings → Preferences → Dark Mode
```

Preference persists using AsyncStorage.

---

# API Documentation

All backend functionality is implemented as Convex functions rather than traditional REST endpoints.

---

## Queries

### `getTodos`

Returns all todos ordered by creation time descending.

```ts
const todos = useQuery(api.todos.getTodos);
```

---

## Example Response

```json
[
  {
    "_id": "abc123",
    "_creationTime": 1700000000000,
    "text": "Buy groceries",
    "isCompleted": false
  }
]
```

---

## Mutations

### `addTodo`

```ts
await addTodo({
  text: "Finish README"
});
```

---

### `toggleTodo`

```ts
await toggleTodo({
  id
});
```

---

### `updateTodo`

```ts
await updateTodo({
  id,
  text
});
```

---

### `deleteTodo`

```ts
await deleteTodo({
  id
});
```

---

### `clearAllTodos`

```ts
const result = await clearAllTodos();
```

Returns:

```json
{
  "deletedCount": 12
}
```

---


## Known Bottlenecks

| Issue | Recommended Fix |
|---|---|
| Style factories created every render | Wrap with `useMemo` |
| Duplicate `useQuery()` subscriptions | Lift query to parent |
| Gradient-heavy FlatList items | Reduce gradients at scale |
| Sequential deletes | Replace with `Promise.all` |

---

# Tradeoffs & Limitations

| Decision | Tradeoff |
|---|---|
| Convex BaaS | Vendor lock-in |
| Expo managed workflow | Limited deep native access |
| No authentication | Prototype simplicity |
| Context-based theme state | Broad re-renders |
| No offline support | Requires connectivity |

---

# Known Issues

| Issue | Severity |
|---|---|
| Theme flash on startup | Low |
| Duplicate subscriptions | Medium |
| Preferences not persisted | Low |
| Whitespace todo validation missing | Medium |
| Wrong icon usage | Low |

---

# Challenges Faced

## Reactive Mental Model Shift

Convex fundamentally changes how data fetching is approached. Instead of thinking about refetch timing, the UI simply reacts to server state changes.

---

## expo-router Layout Hierarchy

Understanding nested `_layout.tsx` composition required learning a different mental model compared to traditional React Navigation.

---

## Theme Hydration Timing

AsyncStorage-based theme persistence introduces a flash-of-wrong-theme problem during startup hydration.

---

## Cross-Platform Styling

iOS and Android shadow systems behave differently and required platform-specific adjustments.

---

# What I Learned

## Architectural Concepts

- Reactive vs request-driven architectures
- OCC transaction models
- Real-time synchronization patterns
- Subscription-driven UI rendering

---

## React Native Concepts

- JSI vs legacy Bridge architecture
- FlatList virtualization
- Safe area handling
- Cross-platform UI differences

---

## Engineering Lessons

- Importance of user ownership in schemas
- File-based routing mental models
- Retrofitting auth is expensive later

---

# Future Scope

- Authentication integration
- Persistent settings
- Better validation
- Categories and tags
- Push notifications
- Drag-to-reorder
- Search and filtering
- Pagination
- Shared collaborative lists

---


# Contribution Guidelines

This is primarily a personal learning and portfolio project.

If contributing:

- Keep PRs focused
- Update documentation for architectural changes
- Maintain TypeScript strict mode compatibility

---

# License

MIT License — see `LICENSE`.

---

# Contact

**Heramb Chaudhari**

[![GitHub](https://img.shields.io/badge/GitHub-Heramb1221-black?style=for-the-badge&logo=github)](https://github.com/Heramb1221)

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Heramb%20Chaudhari-blue?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/heramb-chaudhari)

[![Email](https://img.shields.io/badge/Email-hchaudhari1221%40gmail.com-red?style=for-the-badge&logo=gmail)](mailto:hchaudhari1221@gmail.com)

---

Built to explore real-time reactive architecture, Convex subscriptions, Expo Router, and modern React Native cross-platform engineering.
