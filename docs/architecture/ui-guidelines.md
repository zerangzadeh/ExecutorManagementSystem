# UI and UX Standards — Dezful Qualified Building Executors System

**Version:** `0.1-draft`  
**Scope:** All frontend pages and components in the first release under `src/frontend/`  
**Stack:** `React + TypeScript + Vite`  
**Objective:** Consistent forms, readable Persian content, fewer operational errors, and incremental development without unnecessary complexity.

> This document is a **proposed design and implementation standard**. Legal requirements, approval authorities, and capacity formulas come from the documents in `docs/requirements/`, not from UI assumptions. Items marked `Draft` must not be presented as legally verified requirements.

## 1. Core Principles

1. **Persian and `RTL` are the defaults.** A component that looks correct in English is not acceptable until tested in Persian.
2. **The UI must serve the workflow.** No layout may obscure the distinctions among “project status,” “executor responsibility,” “request status,” and “capacity occupancy.”
3. **One shared pattern for each recurring problem.** Check for an existing shared component before creating another form, table, selector, or notification.
4. **Keep the MVP appropriately simple.** Multi-step forms, sophisticated data grids, and new libraries require a real use case.
5. **Make behavior predictable.** Show loading, error, success, draft-saving, and step-transition states clearly.
6. **Build for accessibility.** Important actions must work with a keyboard and must not rely on color alone.

## 2. Language, Writing Direction, and Numbers

### 2.1. Root Configuration

- The HTML document must use `lang="fa"` and `dir="rtl"`. Preserve the base right-to-left direction on the application's root element as well.
- Prefer logical CSS properties such as `margin-inline-start`, `padding-inline-end`, `inset-inline-start`, and `text-align: start` instead of relying on `left/right`.
- Test tab order, back/forward icons, table layout, breadcrumbs, and pagination in `RTL`. Do not reverse icons indiscriminately.
- Isolate English data, URLs, codes, paths, API identifiers, version numbers, and technical strings within their local context using `dir="ltr"` or `unicode-bidi: isolate`.

Minimal example in `src/frontend/index.html`:

```html
<html lang="fa" dir="rtl">
```

### 2.2. Numbers

- Numbers may be displayed with Persian digits, but values sent to the API must use the **standard, locale-independent numeric representation** defined in the data contract.
- Accept Persian digits (`۰۱۲۳۴۵۶۷۸۹`) and Arabic-Indic digits (`٠١٢٣٤٥٦٧٨٩`) in user input and normalize them for validation and conversion. Handle decimal marks and thousands separators explicitly.
- File numbers, license numbers, national identification codes, and other identifiers are **strings, not numbers**. Preserve leading zeros.
- Clearly align numerical columns and show units: `square meters`, `percent`, and `count` (in Persian in the actual UI). Avoid presentation transformations that change a number's meaning.

### 2.3. Fonts and Typography

- Host one legible, properly licensed Persian font locally in the project, **only after the Product Owner selects and approves it**. No specific font is mandated by this draft.
- Establish a clear visual hierarchy for headings, body text, labels, hints, error messages, and tabular data.
- Avoid very small text, excessive density, or de-emphasizing legally or operationally significant information.

## 3. Jalali Dates, Gregorian Dates, and Date Ranges

- **User interface:** Display request, inspector-report, review, approval, and date-filter values in the Jalali (Solar Hijri) calendar. The date picker must support the Jalali calendar as well.
- **Technical contract:** Transfer and store date-only values as Gregorian `YYYY-MM-DD` according to the established contract. Record event and audit times as timestamps with an explicit timezone, preferably UTC. Convert to local time only for display.
- **Business-effective timestamps:** The effective date and time of final approval by the head of office must be determined on the server; a user-selected date must never replace it.
- **Date ranges:** Support start/end selection, validation of `start <= end`, clearing, and useful error messages. Clearly define whether the API treats the end of a date range as inclusive or exclusive.
- Required tests: Jalali leap years, the month of Esfand, bidirectional conversion, year boundaries, range restrictions, empty dates, and differences between local date display and server timestamps.
- Initially approved library: `react-multi-date-picker`, used through a **shared wrapper component**. Do not send Jalali-formatted strings to the API unless an explicit API contract requires them.

## 4. Form Patterns

### 4.1. Consistent Field Components

Where applicable, every field must provide:

- An unambiguous label and unit, a concise hint, and an indication of whether the field is required or optional.
- A default value only when it comes from reliable data, not from a guess about a case's status.
- An error message next to the affected field, in Persian, explaining what needs to be corrected.
- Distinguishable `disabled`, `read-only`, loading, and insufficient-permission states.
- Correct label-to-input associations (`label`, `id`), `aria-describedby`, and `aria-invalid` when an error exists.

### 4.2. Long and Sectioned Forms

- Group medium and long forms into logically named sections. Do not turn every page into an unnecessary collection of decorative cards.
- For forms such as “Project,” “Executor,” and “Request,” arrange information from core details through technical information and documents to final review.
- Use multi-step forms **only when the UX justifies them**. Moving between steps must not unexpectedly discard entered values.
- In a multi-step form, show the current step, completed steps, and the error count for each step.
- Enable draft saving only for workflows that support a server-side `Draft` state. Warn appropriately when navigating away with unsaved changes; do not interrupt every minor action with a confirmation dialog.
- Provide confirmation proportional to the consequences of final submission, state changes, and irreversible operations.

### 4.3. Validation

- Default form-management tool: `react-hook-form`. Use `zod` with `@hookform/resolvers` for data-shape and formatting validation.
- **Client-side validation never replaces server-side validation.** Capacity rules, professional eligibility, permission to modify a case, and duplicate-request restrictions must be enforced by the server.
- When submission fails, preserve valid entered values. Show a summary near the top of the form and errors beside the corresponding fields; focus the first invalid field where practical.
- Error labels should say what is wrong and how to fix it. Avoid generic “Unknown error” messages or raw server error codes in the end-user interface.

## 5. Advanced Selectors and Search

- Use a shared wrapper around `react-select` for long lists of executors, people, projects, and similar data.
- Support text search, keyboard interaction, loading and empty-result states, clearing, and stable integration with the form's selected values.
- For large datasets, fetch bounded or paginated results from the server. Do not load the entire executor registry into the browser.
- Make Persian text search tolerant of whitespace differences, `ی/ي`, `ک/ك`, and alternate digit forms. Search normalization must not alter stored official identifiers.
- Enable multi-selection only for genuinely multi-valued data. Hiding an ineligible option is not a substitute for authorization or server-side eligibility validation.

## 6. Lists and Data Tables

- Start ordinary lists with simple tables and server-side pagination/filtering; a complex table library is not mandatory.
- If lists require complex filtering, sorting, or selection, `TanStack Table` is the recommended option; **installation is not currently required**.
- Keep essential columns readable: identifier, status, assigned user, date, executor, available/occupied capacity, and permitted actions.
- Search, filters, and sorting must agree with the API. `Excel` exports must respect both the selected filters and the user's permission scope.
- For empty results, show an informative Persian “No results found” message and offer a way to clear filters. Never show fabricated or `mock` data in the production interface.
- Render numbers and dates appropriately for Persian/`RTL` tables. Avoid unnecessary page-level horizontal scrolling.

## 7. Notifications, Confirmations, and Error Handling

### 7.1. Library Responsibilities

| Situation | Pattern / Library |
|---|---|
| Successful save, routine update, brief notice | `Sonner` transient `toast` |
| Confirmation of request rejection, cancellation, termination, capacity release, or other sensitive action | `SweetAlert2` confirmation dialog with a clear title and explanation |
| Field validation error | Message beside the field and a form-level summary |
| Network or system error | Understandable message and retry option, without sensitive technical details |
| Long-running operation | Action-specific loading state and protection against duplicate submissions |

- Sensitive confirmations must **name the action and its effect**, rather than merely asking “Are you sure?”
- Display success feedback **only after the server confirms success**.
- A UI confirmation dialog never replaces a required reason, authorization, transaction, or server-side audit record.

### 7.2. API Error Contract

- Prefer the ASP.NET Core `ProblemDetails` pattern and a defined contract for field-level errors.
- Common HTTP statuses: `400/422` for invalid input, `401` for unauthenticated access, `403` for forbidden access, `404` for not found, `409` for state/edit conflicts, and `500` for internal errors. Exact behavior depends on the approved API contract.
- On an `Optimistic Concurrency` conflict, a second submission **must not silently overwrite newer data**. Instruct the user to reload the latest data while retaining the attempted edits where practical.
- Never expose secrets, database table names, server file paths, or `stack trace` details to end users.
- Map field errors to known fields. Show other errors in a clear, form-level message.

## 8. Domain-Specific Screens and States

- In case details, show **project status**, **review-request status**, **current executor allocation**, and **occupied/released capacity** separately.
- Reaching the configured physical-progress threshold does not release capacity by itself; a valid formal approval by the head of office is required to change capacity.
- The executor's formal responsibility-acceptance form is required for both initial and replacement allocations. The UI must distinguish “Form generated,” “Signed form verified,” and “New signature required.”
- For decisions and permitted exceptions, make the reason, supporting document, user, date, and evaluation outcome visible. Do not claim legal permission beyond the current valid rules.
- A user without permission might not see a button, but authorization is always enforced by the API.
- Role-scoped lists, filters, and `Excel` exports must honor authorization boundaries and never expose more data than permitted.

## 9. File Uploads and Documents

- Physical receipt of documents is common in the first release; scanning every document is not mandatory. The UI must support recording receipt/verification status, date, and physical archive location.
- Begin with a native file input when uploading is necessary. Install `React Dropzone` or `Uppy` only after approval of a specific need.
- Validate file types, sizes, and extensions on the server. Never use an untrusted filename as an unsanitized storage path.
- Version and trace official `DOCX` templates and generated `PDF` documents. Correcting a signed form requires a new signature, according to the domain decisions.
- Uploading a document or clicking a “Signed” checkbox must not be misrepresented as a legally valid digital signature.

## 10. Responsive Design and Accessibility

- Test pages at reference widths of `390px`, `768px`, `1024px`, and `1440px`. These are test sizes, not a requirement to define exactly that many breakpoints.
- Stack columns on mobile; do not hide final-action buttons or field errors behind other panels.
- Never convey essential information by color alone. Add meaningful text and/or icons.
- Show visible keyboard focus and use a logical Tab order. Manage focus inside dialogs and restore it when a dialog closes.
- Ensure assistive technologies can detect error messages; icons without visible labels need accessible names.
- Forms and tables must not cause page-level horizontal scrolling at the test widths. Limited, deliberate scrolling within a table container may be acceptable.

## 11. Libraries and Installation Status

Based on the `src/frontend/package.json` reviewed in the repository, the following dependencies **are declared in the dependency manifest**. This document does not reinstall them:

| Library | Status / Purpose |
|---|---|
| `react-hook-form`, `zod`, `@hookform/resolvers` | Form state and validation foundation |
| `react-select` | Searchable selection and authorized multi-selection |
| `react-multi-date-picker` | Jalali date and date-range selection, with API conversion |
| `sweetalert2` | Sensitive confirmations and attention-requiring messages |
| `sonner` | Non-blocking transient notifications |
| `lucide-react` | Consistent application icons |

- Do **not** install `TanStack Table`, `Recharts`, `React IMask`, `React Dropzone`, `Uppy`, `React Flow`, or other tools by default. Before adding one, review the actual need, maintenance, licensing, and existing alternatives.
- Keep libraries as project dependencies with locked versions in `package-lock.json`. **Do not manually copy library source code into `src/`**, unless there is an explicit, justified decision to do so.
- Do not commit `node_modules/` to Git. Deployment assets must allow the running application to operate without a mandatory CDN dependency.

## 12. Suggested Component Layout, Without Premature Creation

These names are **conceptual conventions**, not a claim that these files already exist:

```text
src/frontend/src/
├── components/
│   ├── forms/           # FormField, FormSection, ErrorSummary, SearchSelect
│   ├── date/            # JalaliDatePicker, JalaliDateRangePicker
│   ├── feedback/        # ConfirmDialog, AppToast, LoadingState
│   └── data/            # AppTable, EmptyState, StatusIndicator
├── features/            # Functional application areas
├── lib/                 # API client, date conversion, input normalization
└── styles/              # Shared RTL rules and visual tokens
```

**Create a component only when a real use case needs it.** Do not construct a large component library before implementing the first operational screen.

## 13. Shared Acceptance Checklist for Every Page

Before approving any page or form:

- [ ] Base `RTL` direction, Persian copy, and embedded `LTR` values display correctly.
- [ ] Dates appear in the Jalali calendar and are converted to the standard API format correctly.
- [ ] Required fields, client/server errors, and long-form or multi-step states work as expected.
- [ ] Loading, empty-data, network-error, and retry states are covered.
- [ ] Both action controls and the API enforce the required permissions.
- [ ] Duplicate submissions, silent concurrent edits, and false success notifications are prevented.
- [ ] Entered data is not needlessly lost after validation failure.
- [ ] The page works at the reference widths and with keyboard navigation.
- [ ] `npm run build` and `npm run lint` complete without new errors.
- [ ] If business logic changed, the relevant server tests and acceptance scenarios are updated.

## 14. Managing Changes to This Document

- Small visual adjustments within the scope of this document may be made with clear reporting. Changing a core library, API contract, date/time standard, or sensitive workflow behavior requires Product Owner approval.
- When UI behavior conflicts with domain rules, adapt the UI to the domain. If the domain is ambiguous, do not silently decide policy.
- This document can leave `Draft` status after the Product Owner approves a final version. Until then, maintain the distinction between an implementation recommendation and a confirmed business rule.
