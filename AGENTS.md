<!-- BEGIN:nextjs-agent-rules -->

# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->

# Custom Project Rules & Agent Instructions

This file serves as the **Command Center** for all AI agents (Antigravity, Claude, Cursor, Copilot, etc.) working on this codebase.

## 1. The "Source of Truth" Pointer
Before making architectural or UI changes, agents MUST consult `SPEC.md`, specifically Section 10 (Development Rules & Standards) and Section 11. Do not invent patterns; use the ones established in the spec.

## 2. Strict Database Integrity Rules (Critical)
- **NEVER DELETE RECORDS:** Use `is_active` flags or soft-delete patterns (`is_voided`, `is_deleted`). Nothing is ever permanently deleted.
- **UUIDs Only:** All primary keys must be UUIDs, never auto-incrementing integers.
- **Check Types & RLS:** Always review `src/types/database.ts` before writing queries. Ensure Row Level Security (RLS) is accounted for.

## 3. UI/UX and Component Standards
- **Theme-Aware Colors Only:** NEVER use hardcoded colors like `bg-white` or `text-black`. You must strictly use CSS variables (`bg-background`, `text-foreground`, `bg-popover`, etc.) to support light/dark modes.
- **Strictly shadcn/ui:** Do not build custom UI components (like dropdowns, modals, or buttons) from scratch if a shadcn/ui component already exists.
- **Dialogs & Notifications:** NEVER use browser-native `alert()`, `confirm()`, or `prompt()`. All alerts must use the toast notification system. All confirmations must use the app's `AlertDialog` components.
- **Tooltips Required:** All interactive icon-only elements must include descriptive tooltips/titles.

## 4. Cross-Agent Handoff & Session Logs
- **Handoffs:** When concluding a session, summarize your work in a markdown file inside the `/docs/logs/agent_handoffs/` directory so the next agent can seamlessly pick up where you left off. Always check this folder for context if you are starting a new task.

## 5. Performance & Data Fetching
- **No N+1 Queries:** Never loop through records to make individual API calls. Use bulk queries or `.in()` clauses.
- **Server-Side Auth:** User creation must use server API routes with the service role key. Never create users from client-side code.

## 6. Documentation & Logging Directory
Do not guess where to find information or where to log your work. Use this directory structure:

### Core Rules (Read Before Coding)
- **`SPEC.md`**: The absolute source of truth for app features, business logic, and strict dev rules.
- **`DESIGN.md`**: The visual style guide (Tailwind variables, a11y, layout rules).

### Architecture & Domain Logic
- **`/docs/architecture/`**: Read files here (e.g., `AUTH_LOGIC.md`, `CALCULATIONS.md`) to understand complex system flows before touching backend code.
- **`/docs/research/`**: Reference these for domain-specific medical data handling and OTC compliance.

### Agent Logging (Mandatory Workflow)
- **`/docs/logs/agent_handoffs/`**: When you finish a task, create a markdown summary here (e.g., `feature_x_summary.md`) detailing commits, fixes, and next steps.
- **`/docs/logs/SESSION_INDEX.md`**: After creating your summary, add a row to the table in this file linking to it, so the next agent can find your work.

### Mimo Code Transcripts & Memory
- **`/.mimocode/`**: All Mimo Code IDE generated transcripts, memory files, and plans reside here. If an agent requires historical transcripts or prior Mimo Code memory contexts (like `MEMORY-infrastructure.md`), they should consult `/.mimocode/memory/projects/` and `/.mimocode/memory/sessions/`. Do not move these files or it will reset the IDE's memory.
- **`/docs/logs/mimo_backups/`**: If you manually backup a Mimo Code session or transcript, place it here to separate it from active IDE memory and agent handoffs.

## 7. CI/CD & Deployments
- **Supabase Auto-Deploy:** We use GitHub Actions for database migrations and edge functions. Agents must NEVER run `supabase db push` or `supabase functions deploy` manually from the terminal. Instead, commit your changes and push to `main` (or `master`); the `.github/workflows/supabase-deploy.yml` will handle the deployment automatically.
