# Frontend ERP Architecture & Quality Review Skill

## Overview

This skill reviews frontend projects developed with React and TypeScript, focusing on software architecture, code quality, maintainability, documentation, and frontend development best practices.

The review follows Clean Architecture principles, separation of concerns, modular design, and clean code practices.

The objective is to ensure that the project remains scalable, maintainable, understandable, and consistent with professional software development standards.

---

# Project Architecture

Expected project structure:

```text
src
├── api
├── modules
│   ├── auth
│   ├── inventory
│   ├── purchases
│   └── sales
├── routes
├── shared
│   ├── components
│   └── layouts
└── types
```

## Responsibilities

### api

Contains all communication with backend services.

### modules

Contains business domains:

- auth
- inventory
- purchases
- sales

Each module should be self-contained and responsible only for its own domain.

### routes

Contains application routing configuration.

### shared/components

Reusable UI components shared across modules.

### shared/layouts

Reusable application layouts.

### types

Global TypeScript interfaces and types.

---

# Development Guidelines

## General Principles

### Early Return

Prefer early returns over nested conditions.

### Avoid Duplication

Create reusable abstractions instead of repeating logic.

### File Size

- Components under 200 lines when possible.
- Functions under 50 lines when possible.
- Split large files into smaller modules.

### Function Complexity

Maximum recommended nesting: 3 levels.

### Naming

Use domain-oriented names.

Good examples:

- InventoryTable
- PurchaseService
- SalesForm
- AuthGuard

Avoid:

- Utils
- Helpers
- Misc
- CommonStuff

---

# MUST HAVE

## Architecture

- Modular architecture respected.
- Domain separation maintained.
- Routes centralized.
- Shared components properly reused.
- Shared layouts properly reused.
- API communication isolated in api.
- Types centralized in types.

## Separation of Concerns

- No business logic inside UI components.
- No API calls directly inside components.
- No routing logic scattered across modules.
- Components focus on presentation.

## TypeScript Quality

- TypeScript strict mode enabled.
- No unnecessary use of any.
- Typed component props.
- Typed API responses.
- Typed application state.

## Clean Code

- Descriptive naming.
- No duplicated logic.
- Small focused functions.
- Small focused components.
- Early return pattern used.
- Maximum three nesting levels.

## Error Handling

- Proper error handling.
- User-friendly error messages.
- No silent failures.
- Loading states implemented.

## Documentation

Project must contain:

- README
- Installation guide
- Execution guide
- Architecture description
- Main dependencies description

---

# SHOULD HAVE

## Code Quality

- ESLint configured.
- ESLint warnings resolved.
- Prettier configured.
- Consistent formatting.

## Reusability

- Reusable custom hooks.
- Reusable UI components.
- Shared service abstractions.

## User Experience

- Responsive design.
- Loading indicators.
- Empty states.
- Success feedback.
- Error feedback.

## Maintainability

- Consistent folder structure.
- Consistent naming conventions.
- Updated dependencies.
- Minimal technical debt.

---

# Anti-Patterns

## Avoid

### Architecture

- Business logic inside components.
- API calls directly in pages.
- Domain leakage between modules.
- Circular dependencies.

### Code

- Massive components.
- Massive functions.
- Duplicated code.
- Magic numbers.
- Hardcoded configuration.

### Naming

Avoid generic names:

- utils
- helpers
- common
- sharedFunctions
- misc

Use domain-oriented names instead.

---

# Review Output

For each category report:

✅ PASS

⚠️ WARNING

❌ FAIL

Categories:

- Architecture
- Separation of Concerns
- TypeScript Quality
- Code Quality
- Error Handling
- Documentation
- Maintainability

---

# Final Report

Architecture Score: XX%

Code Quality Score: XX%

Documentation Score: XX%

Overall MUST Compliance: XX%

Overall SHOULD Compliance: XX%

Final Result:

🟢 APPROVED

or

🔴 REJECTED
