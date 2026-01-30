## ✨ Next.js Coding Guidelines

## 📘 Purpose

This file defines coding standards, architectural conventions, and best practices
for all code written in this Next.js project. Gemini Code Assist must follow
these rules when generating, refactoring, or reviewing code.

---

## 🧠 General Guidelines

- Follow the patterns defined in this file.
- Prioritize maintainability, readability, and performance.
- Prefer idiomatic **Next.js**, **React**, and **TypeScript**.
- If something is ambiguous, ask for clarification instead of guessing.

---

## 📂 Project Structure (App Router)

This project uses the Next.js **App Router**:
/app
/(routes)
/api
/components
/hooks
/lib
/styles
/types

Rules:

- Put server components in `/app`.
- Put client components in `/components` or `/app` with `"use client"`.
- Place reusable business logic in `/lib`.
- Place custom hooks in `/hooks`.

---

## ✨ Coding Best Practices for Next.js

### ✔ TypeScript

- All code must use **TypeScript**.
- Use `type` aliases for objects and function signatures.
- Prefer `zod` or similar libraries for schema validation.
- Avoid using `any`; use `unknown` if needed.

### ✔ Components

- Default to **React Server Components (RSC)** unless client-side interactivity is required.
- For stateful logic or browser APIs, use **Client Components** with `"use client"` at the top.
- Keep components small and focused.
- Use functional components only.

### ✔ Styling

- Use **Tailwind CSS** unless otherwise specified.
- Avoid inline styles unless necessary.
- Use class utilities instead of custom CSS when possible.

### ✔ Fetching Data

- Server Components: Use `fetch()` or direct DB queries (if allowed).
- Client Components: Use **SWR** or React Query.
- Avoid waterfalls by using `Promise.all` or parallel data fetching.
- Cache everything that can be cached.

### ✔ API Routes

- Use the App Router API structure:
  /app/api/feature/route.ts
- Always return JSON with the shape:
  {
  success: boolean;
  data?: T;
  error?: string;
  }

yaml
Copier le code

- Validate inputs using `zod`.
- Keep business logic outside route handlers — place it in `/lib`.

---

## 🗂️ File Naming Conventions

- Components: `PascalCase`
- Hooks: `useSomething.ts`
- API routes: lowercase folders + `route.ts`
- Lib utilities: `camelCase`

---

## 🔐 Security Practices

- Do not expose secrets in client components.
- Use environment variables only in server environments.
- Sanitize user input and validate everything coming into an API route.
- Use secure cookies for authentication when possible.
- Never log sensitive values (tokens, passwords, keys).

---

## 🧪 Testing Practices

- Use **Jest** + **Testing Library** for components.
- Use **Playwright** for end-to-end tests.
- Unit tests should mock remote services and DB calls.
- Test components based on behavior, not implementation.

---

## ⚡ Performance Best Practices

- Use RSC to avoid shipping unnecessary JavaScript.
- Use `next/image` for all images.
- Use dynamic imports for heavy dependencies.
- Preload critical resources when applicable.
- Avoid unnecessary client components.

---

## 🧹 File Generation & Refactoring Rules

When generating files:

- Components → `/components` or in route-specific folders under `/app`.
- Hooks → `/hooks`
- Server utilities → `/lib/server`
- Shared utilities → `/lib`
- Types → `/types`

Refactoring constraints:

- Do not change public APIs unless requested.
- Keep route structure intact.
- Prefer incremental improvements over large rewrites.
- Remove unused code.

---

## 📝 Behavior Expectations

- Provide full files unless specifically asked for diffs.
- Keep imports clean and organized.
- Follow existing patterns in the codebase.
- Provide explanations for complex logic.
- Do not generate placeholder images, random secrets, or sensitive data.

---

## 🚫 Do Not

- Do not modify `.geminiignore`,`.aiexclude`, `.env`, or environment variables.
- Do not introduce new frameworks unless explicitly approved.
- Do not use deprecated Next.js APIs (`pages/`, `getServerSideProps`, etc.).
- Do not generate insecure code (logging secrets, exposing env vars, etc.).

---
