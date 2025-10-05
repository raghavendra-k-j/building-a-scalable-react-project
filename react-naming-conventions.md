# React naming conventions for a scalable and consistent project structure

> Version: 1.0 • Scope: Any TypeScript/React/Next.js/Node project (web apps, packages, services)  
> Goal: **Consistency, clarity, predictability** across teams and projects.

---

## Guiding principles
- **Be consistent first.** One rule > many exceptions.
- **Name by intent.** Prefer `order-service` over `utils2`.
- **Keep it portable.** Avoid case-sensitive pitfalls across OS/tooling.
- **Prefer small, local “public APIs.”** (folder-level barrels).

---

## Global casing (at a glance)
| Thing | Case | Examples |
|---|---|---|
| **Folders** | **kebab-case** | `user-profile`, `order-history` |
| **Files (general)** | **kebab-case** | `user-service.ts`, `date-format.ts` |
| **React components** | **PascalCase (export name)** | `OrderCard`, `BrandLogo` |
| **Class names** | **PascalCase** | `UserService`, `ApiClient` |
| **Types & interfaces** | **PascalCase** | `User`, `OrderDto`, `ApiResponse<T>` |
| **Functions & variables** | **camelCase** | `getUser`, `formatDate`, `userId` |
| **Enums** | **PascalCase**, members **SCREAMING_SNAKE_CASE** | `Role.ADMIN` |
| **Constants** | **SCREAMING_SNAKE_CASE** | `MAX_RETRIES`, `API_BASE_URL` |
| **Env vars** | **SCREAMING_SNAKE_CASE** | `NEXT_PUBLIC_API_URL` |

**Why:** Kebab-case filenames are readable, URL/route-friendly, and avoid case-sensitivity issues. PascalCase signifies constructs (components/classes/types). camelCase matches JS function/variable norms.

---

## Directory structure (suggested)
```
src/
  components/           # UI building blocks
  domain/               # business-aware UI & logic per entity
  utils/                # helpers (date, number, object, etc.)
  services/             # API clients, repositories
  hooks/                # React hooks
  dtos/                 # request/response DTOs & schemas
  app-shell/            # shared layout/chrome (Header, Sidebar)
```

- Folders are **kebab-case** and **domain-driven**: `payment`, `reporting`, `user`.
- Keep depth shallow; use feature folders before type-only silos when it improves discoverability.

---

## React components
- **Export name:** **PascalCase**
- **File name:** **kebab-case**
- **One component per file**; colocate tests/stories.

**Example**
```
src/components/cards/order-card.tsx   // export function OrderCard() {}
src/components/cards/order-card.test.tsx
src/components/cards/order-card.stories.tsx
```

**Barrels (local only)**
```
src/components/cards/index.ts
export { OrderCard } from './order-card';
export { InvoiceCard } from './invoice-card';
```
> Use **named re-exports** (no `export *`, no default “object” exports) to preserve tree-shaking.

---

## Classes
- **Single class per file:** file may be **PascalCase** (strict 1:1) **or** **kebab-case** (uniform). Pick one style per repo.
  - Example 1: `src/services/ApiClient.ts` → `export class ApiClient {}`
  - Example 2: `src/services/api-client.ts` → `export class ApiClient {}`
- **Multiple tightly-related classes:** use a **kebab-case plural** filename.
  - `src/domain/shapes/shapes.ts` → `export class Rectangle {}`, `export class Circle {}`

**Recommendation:** Prefer **one class per file** for discoverability and easier refactors. Use multi-class files only for tiny, cohesive types (e.g., grouped error classes).

---

## Utilities
- Group **related functions** in one **kebab-case** file; functions are **camelCase**.
  - `src/utils/date-format.ts` → `formatDate`, `formatDateTime`, `formatTime`
  - `src/utils/number-format.ts` → `formatNumber`, `formatCurrency`, `formatPercentage`
- Keep modules **side-effect free** to enable dead‑code elimination.

---

## DTOs, types, schemas
- **Types & interfaces:** **PascalCase** (`User`, `LoginReq`).
- **Multiple related DTOs in one file**: **kebab-case plural**.
  - `src/dtos/auth-dtos.ts` → `LoginReq`, `LoginRes`, `SignUpReq`, `SignUpRes`
- Validation schemas (Zod/Yup): colocate by domain (e.g., `user-schemas.ts`).

---

## Hooks
- **Files:** **kebab-case**; **export names:** `useThing`.
  - `src/hooks/use-auth.ts` → `export function useAuth() {}`
  - `src/hooks/use-debounce.ts` → `useDebounce`

---

## Services & API clients
- **Files:** **kebab-case** describing role.
  - `src/services/user-service.ts` → `export class UserService {}`
  - `src/services/order-repository.ts` → repository abstraction
- Keep runtime config & base URLs out of names (avoid `user-service-v2.ts`).

---

## Styles & assets
- **CSS modules / style files:** **kebab-case** (`order-card.module.css`).
- **Assets:** **kebab-case** (`empty-state.svg`, `user-avatar.png`).

---

## Index files (barrels) — do this, not that
**Do**
```ts
// src/components/primitives/button/index.ts
export { FilledButton }   from './filled-button';
export { OutlinedButton } from './outlined-button';
export { TextButton }     from './text-button';
export { GhostButton }    from './ghost-button';
```
**Don’t**
```ts
// Avoid giant root barrels or wildcard re-exports across large trees:
export * from '../..';
// Avoid default-exported objects that aggregate everything (breaks tree-shaking):
export default { FilledButton, OutlinedButton, TextButton, GhostButton };
```

**Rule of thumb**
- Barrels are **local and surgical** (per small folder).
- Use **named ESM re-exports**; keep modules **side-effect free**.

---

## Imports & path aliases
**tsconfig.json**
```jsonc
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@components/*": ["src/components/*"],
      "@domain/*": ["src/domain/*"],
      "@utils/*": ["src/utils/*"],
      "@services/*": ["src/services/*"],
      "@dtos/*": ["src/dtos/*"],
      "@hooks/*": ["src/hooks/*"],
      "@app-shell/*": ["src/app-shell/*"]
    }
  }
}
```
**Editor preference (VS Code)**  
Set imports to be non‑relative:
```jsonc
{
  "typescript.preferences.importModuleSpecifier": "non-relative",
  "javascript.preferences.importModuleSpecifier": "non-relative"
}
```

**Optional (auto‑import hygiene)**  
Hide deep files so only barrels appear in suggestions (TS 4.8+ / 5.6+):
```jsonc
{
  "typescript.preferences.autoImportFileExcludePatterns": [
    "**/components/**/button/filled-button.tsx"
  ]
}
```

---

## Tests, stories, mocks
- **Suffix files** in the same folder:
  - `*.test.tsx`, `*.spec.ts`, `*.stories.tsx`
  - `__mocks__/*.ts`
- Keep names in **kebab-case**.

---

## Linting & enforcement (snippets)
**Filename case**
```jsonc
// .eslintrc.json
{
  "plugins": ["unicorn", "import"],
  "rules": {
    "unicorn/filename-case": ["error", { "cases": { "kebabCase": true } }],
    "max-classes-per-file": ["error", 1],
    "import/no-cycle": "error"
  }
}
```
> If your repo uses PascalCase filenames for single class/component files, allow both:
```jsonc
"unicorn/filename-case": ["error", { "cases": { "kebabCase": true, "pascalCase": true } }]
```

---

## Quick reference (cheat sheet)
- **Folders & files:** kebab‑case → `user-service.ts`, `order-card.tsx`
- **Components/classes/types:** PascalCase → `OrderCard`, `UserService`, `LoginReq`
- **Functions/vars:** camelCase → `formatDate`, `userId`
- **Enums/constants:** `Role.ADMIN`, `MAX_RETRIES`
- **Barrels:** local, named re‑exports only
- **One component/class per file** (multi‑class files only when tiny & cohesive)
- **Side-effect free modules** to keep tree-shaking effective

---

## Examples
```tsx
// src/components/cards/order-card.tsx
export function OrderCard() { /* … */ }

// src/services/api-client.ts
export class ApiClient { /* … */ }

// src/utils/date-format.ts
export function formatDate(d: Date) { /* … */ }
export function formatDateTime(d: Date) { /* … */ }
export function formatTime(d: Date) { /* … */ }

// src/dtos/auth-dtos.ts
export interface LoginReq { /* … */ }
export interface LoginRes { /* … */ }
export interface SignUpReq { /* … */ }
export interface SignUpRes { /* … */ }
```
