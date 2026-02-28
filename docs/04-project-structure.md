# Chapter 4 — Project Structure & Folder Architecture

## 4.1 Why Structure Matters

Early-stage projects often ignore structure in favor of speed.

However, as features grow, poor structure becomes technical debt.

This project is intentionally structured from the beginning to support:

- Clear separation of concerns
- Scalability
- Maintainability
- Feature isolation
- Future expansion (admin panel, premium logic, analytics)

The goal is not just to make it work — but to make it sustainable.

---

## 4.2 High-Level Folder Structure

The project uses Next.js App Router with a layered structure:

greek-live-cam-app
│
├── app/
│ ├── greece/
│ │ ├── [city]/
│ │ │ ├── [slug]/
│ │ │ │ └── page.tsx
│ │ │ └── page.tsx
│ │ └── page.tsx
│ │
│ ├── api/
│ │ └── cameras/
│ │ └── route.ts
│ │
│ └── layout.tsx
│
├── components/
│ ├── CameraCard.tsx
│ ├── CameraPlayer.tsx
│ ├── CategoryFilter.tsx
│ └── Navbar.tsx
│
├── lib/
│ ├── db.ts
│ ├── fetchers.ts
│ └── utils.ts
│
├── types/
│ └── camera.ts
│
├── prisma/
│ └── schema.prisma
│
└── styles/
└── globals.css

This structure separates:

- Routing layer
- UI layer
- Data access layer
- Type definitions
- Database schema

---

## 4.3 The `/app` Directory (Routing Layer)

The `/app` directory defines the routing system using Next.js App Router.

Example route:

/greece/athens/monastiraki-live-cam

Corresponding file structure:

app/greece/[city]/[slug]/page.tsx

### Why Dynamic Segments?

Dynamic segments allow:

- Clean SEO URLs
- Geographic scalability
- Multi-country expansion without restructuring

Future expansion example:

/italy/rome/colosseum-live

The routing structure remains unchanged.

---

## 4.4 API Route Layer

API routes live inside:

app/api/

Example:
app/api/cameras/route.ts

Basic structure:

```ts
import { NextResponse } from "next/server";
import { getAllCameras } from "@/lib/fetchers";

export async function GET() {
  const cameras = await getAllCameras();
  return NextResponse.json(cameras);
}
```

Design Principle

API routes act as controllers only.

They do not:

Contain database logic

Contain business logic

Contain formatting logic

All heavy lifting is delegated to /lib.

This prevents route-level chaos as complexity increases.
4.5 Component Layer (/components)

UI components are isolated and reusable.

Example:

```ts
type CameraCardProps = {
title: string
city: string
thumbnail: string
}

export default function CameraCard({
title,
city,
thumbnail
}: CameraCardProps) {
return (
<div className="card">
<img src={thumbnail} alt={title} />
<h3>{title}</h3>
<p>{city}</p>
</div>
)
}
```

Key Rules

Components do not directly query the database

Components receive typed props

Components remain presentation-focused

This improves reusability and testability.

## 4.6 The /lib Directory (Logic Layer)

The /lib directory centralizes reusable logic.

Example structure:
lib/
├── db.ts
├── fetchers.ts
└── utils.ts
db.ts

Handles database connection:

```ts
import { prisma } from "./db";

export async function getAllCameras() {
  return prisma.camera.findMany({
    orderBy: { createdAt: "desc" },
  });
}
```

Why This Layer Exists

Keeps API routes clean

Allows reuse in server components

Makes logic testable

Prepares for scaling

This separation prevents structural decay.

## 4.7 Type Definitions (/types)

Centralized types improve consistency.

Example:

```ts
export interface Camera {
  id: string;
  title: string;
  slug: string;
  country: string;
  city: string;
  category: string;
  youtubeUrl: string;
}
```

Benefits:

Strong type safety

Reduced runtime errors

Better developer experience

4.8 Separation of Concerns Diagram
User
↓
Routing Layer (/app)
↓
API Layer (/app/api)
↓
Logic Layer (/lib)
↓
Database (PostgreSQL)
Each layer has a clear responsibility.

This prevents tight coupling.

## 4.9 Future Expansion Planning

This structure allows future additions without refactoring:

Possible new folders:
app/admin/
app/premium/
app/search/
No restructuring required.

This is intentional.

## 4.10 Anti-Patterns Avoided

The following are deliberately avoided:

Mixing DB calls inside components

Hardcoded mock data in production

Business logic inside route files

Deeply nested component hierarchies

Global state misuse

Early discipline prevents later refactoring crises.

##4.11 Structure Summary

The folder architecture is designed to:

Support Greece-first launch

Enable global expansion

Keep logic isolated

Improve maintainability

Reduce technical debt

This structure reflects startup engineering maturity rather than experimental development.
