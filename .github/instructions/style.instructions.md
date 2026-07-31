---
description: 'Tailwind CSS v4 styling patterns and dark theme guidelines'
applyTo: '**/*.{astro,css}'
---

# Tailwind CSS Instructions

## Tailwind CSS v4 Configuration

This project uses Tailwind CSS v4.1.14 via the `@tailwindcss/vite` plugin.

### Global CSS Setup

- Import Tailwind in `global.css`: `@import "tailwindcss";`
- No separate `tailwind.config.js` file is used
- Configuration is handled through the Vite plugin

## Dark Theme Styling

ALL UI components MUST use dark theme colors:

### Color Palette

- Background colors: `bg-slate-800`, `bg-slate-900`, `bg-slate-950`
- Text colors: `text-slate-100`, `text-slate-200`, `text-slate-300`
- Border colors: `border-slate-700`, `border-slate-600`
- Accent colors for hover/focus states

### Common Patterns

- Cards and containers: `bg-slate-800 rounded-xl p-6 shadow-lg`
- Hover effects: `hover:bg-slate-700 transition-colors duration-200`
- Borders: `border border-slate-700`
- Gradients for visual interest: `bg-gradient-to-br from-slate-800 to-slate-900`
- Backdrop effects: `backdrop-blur-sm bg-slate-900/50`

### Responsive Design

- Use responsive prefixes: `sm:`, `md:`, `lg:`, `xl:`
- Mobile-first approach
- Ensure readability on all screen sizes

## Utility Classes

- Prefer utility classes over custom CSS when possible
- Use semantic grouping: layout, spacing, colors, typography
- Keep utility combinations readable and maintainable

## Modern UI Patterns

- Rounded corners: `rounded-lg`, `rounded-xl`, `rounded-2xl`
- Smooth transitions: `transition-all duration-200 ease-in-out`
- Shadows for depth: `shadow-md`, `shadow-lg`, `shadow-xl`
- Focus states for accessibility: `focus:ring-2 focus:ring-blue-500`

## TypeScript Code Formatting

### Type Annotations

- Use **explicit types** for function parameters and return values in the data layer (`db/`, `src/lib/`). This aids readability and enables proper type checking with `tsgo` (TypeScript 7).

```ts
// Good: explicit types
export async function getAllGames(db: Database): Promise<Game[]> {
  // ...
}

// Avoid: inferred types in exported functions
export async function getAllGames(db) {  // ❌ avoid this
  // ...
}
```

### Spacing and Line Length

- Keep lines readable (aim for ~100 characters max).
- Use consistent indentation (2 spaces).
- Add a blank line between logical sections in files.

### Comments and Documentation

All exported functions and types **must have JSDoc/TSDoc comments** describing purpose, parameters, and return values. See [`comments-and-docs.instructions.md`](comments-and-docs.instructions.md) for detailed patterns.

```ts
/**
 * Derives a deterministic star rating (3.0–5.0) from a game title.
 * 
 * @param title - Game title to hash
 * @returns number - Star rating in range [3.0, 5.0]
 */
export function ratingFromTitle(title: string): number {
  // ...
}
```

### Enforcement

TypeScript formatting is verified by:
- **ESLint** (`npm run lint`) — enforces code quality and style for `.ts`, `.tsx`, and `.astro` files
- **Type checking** (`npm run typecheck`) — ensures explicit types via `tsgo` (TypeScript 7) on the data layer
