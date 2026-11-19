# Development Agent Instructions | ExecutorManagementSystem

> **Purpose:** This file provides standing instructions for collaboration with Codex and other coding agents. Read the requirements and design details in `docs/`; this file does not replace them.
>
> **Status:** Version 0.1, proposed for Product Owner approval. Prepared on 1405/08/29 (Solar Hijri calendar).

## 1. Scope and Source Precedence

- Product: An internal system for managing the assignment of construction work to qualified building executors. The first release is for the Dezful representative office.
- Initial operating users: one owner-request registration specialist, one reviewing specialist, one head of office, and one system administrator. **Do not hard-code these user counts into the database.**
- The first release excludes owner/executor/inspector portals, shared expert work queues, a dynamic workflow designer, SMS, and external system integrations.
- **Primary domain decision source:** `docs/requirements/Dezful_Executor_System_Decision_Register_v01.md`.
- **Business rules and data model:** `docs/requirements/Business_Rules_Specification_Dezful_MVP_v04_Draft.md` and `docs/requirements/Data_Dictionary_Entity_Schema_Dezful_MVP_v04_Draft.md`.
- **User flows:** `docs/requirements/User_Flows_Wireframe_Logic_Dezful_MVP_v04_Draft.md`.
- **Architecture:** `docs/architecture/Technology_Stack_Architecture_Dezful_MVP_v04_Draft_NET11.md`.
- **Required UI standard for new changes:** `docs/architecture/ui-guidelines.md`.
- **Implementation priorities and acceptance criteria:** `docs/planning/Product_Backlog_Dezful_MVP_v01_Draft.md`.
- These documents are currently marked `Draft`. Report inconsistencies or ambiguities; **never invent, amend, or approve business rules or legal requirements on your own.** A newer explicit Product Owner decision takes precedence over an older assumption.

## 2. Technology Stack and Architecture

- Backend: `C#`, `ASP.NET Core 11`, target framework `net11.0`, `EF Core 11`, and `SQL Server`.
- Frontend: `React`, `TypeScript`, and `Vite` in `src/frontend/`.
- Architecture: **Modular Monolith**, using the existing `Domain`, `Application`, `Infrastructure`, and `Api` projects.
- Implement each capability in its appropriate layer. Sensitive capacity calculations and authorization policies must never exist exclusively in React.
- Prefer simple, testable solutions. Do not automatically introduce `Microservices`, application-wide `CQRS`, a `Generic Repository`, a workflow engine, messaging infrastructure, or unnecessary abstractions.
- Inspect the existing structure before making changes. Do not duplicate projects, folders, components, or packages.

## 3. Educational Collaboration with the Product Owner

1. Before making a change, identify the goal, affected files, dependencies, and necessary tests.
2. For analysis-only or “review only” tasks, **do not edit any files**. For implementation tasks, first explain a short change plan and its technical rationale, unless the Product Owner's request already explicitly authorizes that specific implementation.
3. Keep changes small, reviewable, and confined to the request. Avoid unrelated refactoring or unsolicited “improvements.”
4. After implementation, report exactly which files changed, why they changed, how they were tested, and what limitations remain.
5. Clearly distinguish **confirmed decisions, technical recommendations, assumptions, and matters requiring legal verification**.
6. **Without explicit Product Owner approval**, do not run `git commit` or `git push`, delete data, perform destructive cleanup, execute production database migrations, change decision records, or install new dependencies.
7. When the Product Owner wants to perform an action personally, provide commands in order and explain their effects and how to reverse them where applicable.

## 4. Global UI Standards

- The UI must be **Persian-first, responsive, `RTL`-compatible, and accessible**. Binding details are in `docs/architecture/ui-guidelines.md`.
- Display and select dates using the Jalali (Solar Hijri) calendar in the UI; keep standard Gregorian API contracts and date storage separate from presentation formatting.
- Use shared form patterns, readable sections, explicit labels, inline field errors, and form-level error messages. Use multi-step forms only when they genuinely help the user.
- Searchable selectors for large datasets, loading/empty/error states, clear Persian messages, and keyboard navigation are required.
- Sensitive business actions, such as final approval, capacity release, and termination of an allocation, require **server-side** authorization and validation. Hiding a button is insufficient.
- Preserve the distinction among **project status, executor allocation status, and capacity availability** in every relevant page and message.
- Set `lang="fa"` and `dir="rtl"` correctly in the main `index.html` and UI components; isolate identifiers, paths, and English fragments as `LTR` where appropriate.

## 5. Library Policy

- First inspect `src/frontend/package.json` and the existing code. Reuse installed packages and shared components; **do not install new packages without permission**.
- Baseline libraries recorded when this document was prepared: `react-hook-form`, `zod`, `@hookform/resolvers`, `react-select`, `react-multi-date-picker`, `sweetalert2`, `sonner`, and `lucide-react`.
- These libraries have specific purposes and are not mandatory on every page. Preferred starting points are `React Hook Form + Zod` for forms, `SweetAlert2` for sensitive confirmation dialogs, `Sonner` for transient notifications, `React Multi Date Picker` for Jalali dates, `Lucide React` for icons, and `React Select` for advanced selection controls.
- Add advanced tables (`TanStack Table`), charts (`Recharts`), input masks, and advanced upload tools **only when there is a demonstrated need and explicit approval**. Avoid redundant libraries with overlapping functions.
- Commit `package.json` and `package-lock.json` to Git. Do not commit `node_modules/`, `bin/`, or `obj/`.
- The running product must not require a public CDN. Distinguish offline operation/deployment from offline installation/build requirements.

## 6. Quality, Security, and Error Contracts

- Validate form input on the client for usability, but **the server is always the authority for validation**.
- Expose API errors safely and predictably with understandable Persian messages and field-level errors. Never display secrets or internal server details.
- Prevent silent overwrites when an `Optimistic Concurrency` conflict occurs; explain that the user needs to reload the latest data.
- Enforce `RBAC` permissions and the head of office's organizational authority at the API layer, independently of which UI buttons are visible.
- Every capacity allocation/release change must be tested, atomic, protected against duplicate application, and recorded in the audit trail.
- Test loading, empty-data, network-error, validation-error, authorization-error, and version-conflict states in the UI.
- Baseline verification commands: `dotnet build`, `dotnet test`, and, from `src/frontend/`, `npm run build` and `npm run lint`. Clearly identify additional tests and run them only if their tools are actually available.

## 7. Completion Report Format for Every Task

- **Scope completed:** What was implemented or reviewed?
- **Files changed:** Path and summary of each change.
- **Rationale:** Why does the solution fit the MVP requirements and architecture?
- **Tests:** Commands, number/results of tests, and remaining failures.
- **Ambiguities, risks, and next steps:** Separate blockers from optional recommendations.

> **Guiding principle:** “Make the smallest sufficient change while giving the Product Owner maximum visibility.” This is a learning project as well as a product-development project.
