# React naming conventions — AI brief

**Goal:** minimal, consistent, portable. (TS/React/Next/Node)

## Casing
- Folders & files: **kebab-case** (e.g., `user-service.ts`, `order-card.tsx`)
- Components & classes: **PascalCase** (`OrderCard`, `UserService`)
- Types & interfaces: **PascalCase** (`User`, `LoginReq`)
- Functions & variables: **camelCase** (`formatDate`, `userId`)
- Enums: **PascalCase** with **SCREAMING_SNAKE_CASE** members (`Role.ADMIN`)
- Constants & env: **SCREAMING_SNAKE_CASE** (`MAX_RETRIES`, `NEXT_PUBLIC_API_URL`)

## Structure
- One component/class per file (exceptions: tiny grouped errors).
- Group related utils/types in single files (kebab-case plural ok: `auth-dtos.ts`).
- Collocate tests/stories: `*.test.ts(x)`, `*.stories.tsx`, `__mocks__/`.

## Barrels (index.ts)
- **Local only**, per small folder.
- **Named exports** only: `export { X } from './x'`.
- **Avoid** `export *` and default-exported aggregate objects.
- Keep modules **side-effect free** (tree-shaking).

## Imports
- Prefer non-relative with path aliases (`@components/*`, `@domain/*`, `@utils/*`).
- Editor hint (VS Code): `"typescript.preferences.importModuleSpecifier": "non-relative"`.

## Examples
- `components/cards/order-card.tsx` → `export function OrderCard() {}`
- `services/user-service.ts` → `export class UserService {}`
- `utils/date-format.ts` → `export function formatDate() {}`
- `components/cards/index.ts` → `export { OrderCard } from './order-card'`
