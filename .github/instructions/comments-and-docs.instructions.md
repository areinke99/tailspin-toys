---
description: 'Commenting philosophy and documentation standards'
applyTo: '**/*'
---

# Comments and Documentation Standards

Clear, well-documented code keeps the codebase consistent and makes it easier for contributors and tooling (including Copilot) to understand intent and produce correct changes.

## Core Philosophy

### Comment Intent, Not Mechanics

**Comments should explain *why* code exists and the reasoning behind non-obvious decisions, not restate *what* the code already says.**

Bad examples (restate what the code shows):
```ts
// Increment the counter
count++;

// Check if the user is logged in
if (user.isAuthenticated) {
```

Good examples (explain intent/decisions):
```ts
// Increment only once per session to avoid double-counting
count++;

// Allow access only if explicitly authenticated, not through cookies alone
// This is stricter than typical but required for our security model.
if (user.isAuthenticated) {
```

### Keep Comments Current

Treat outdated comments as bugs — update or delete them whenever you touch related code. A wrong comment is worse than no comment.

## TSDoc / JSDoc Standards

Every **exported function or type** in `db/` and `src/lib/` must have a comment block describing:

- **Purpose**: What does this function do?
- **Parameters**: Each parameter's type and meaning
- **Return value**: What does it return? What could be `null`?
- **Usage notes**: Anything non-obvious about how to call it (e.g. injection pattern, side effects)

### Function Documentation Examples

#### Data-Access Helper (injectable db pattern)

```ts
/**
 * Retrieves all games from the database, ordered by title.
 * Used at build time to enumerate all game pages.
 * 
 * @param db - Injected database instance (real or test). Allows testability without mocking.
 * @returns Promise<Game[]> - All games sorted alphabetically by title (deterministic for static builds).
 * @throws If the database query fails.
 * 
 * @example
 * ```ts
 * const games = await getAllGames(getDatabase());
 * ```
 */
export async function getAllGames(db: Database): Promise<Game[]> {
  // implementation
}
```

#### Pure Transform Function

```ts
/**
 * Derives a deterministic star rating (3.0–5.0) from a game title.
 * Used to populate seed data consistently across builds.
 * 
 * @param title - Game title to hash
 * @returns number - Star rating in range [3.0, 5.0], always the same for the same title.
 * 
 * @example
 * ```ts
 * const rating = ratingFromTitle('Code Quest');  // always 4.2
 * ```
 */
export function ratingFromTitle(title: string): number {
  // implementation
}
```

#### Database Helper (non-injectable)

```ts
/**
 * Builds and returns a singleton Drizzle client over libSQL for this build.
 * Called from pages during build-time static generation.
 * 
 * @returns Drizzle client configured to the database at DATABASE_URL.
 * @throws If DATABASE_URL is not set or the database file is corrupted.
 */
export function getDatabase(): Database {
  // implementation
}
```

### Type Documentation

Export interfaces with JSDoc, especially those exposed as component Props:

```ts
/**
 * Published game details.
 */
export interface Game {
  /** Unique integer ID (auto-increment from schema). */
  id: number;

  /** Human-readable game title. */
  title: string;

  /** Optional star rating (3.0–5.0 or null if not yet evaluated). */
  starRating: number | null;
}
```

## Astro Component Documentation

Every reusable component must document its `Props` interface.

### Component Props Documentation

```astro
---
/**
 * Displays a single game as a card in the game list.
 * - Shows title, publisher, and category
 * - Links to the detail page
 * - Includes accessibility and testability attributes
 */
interface Props {
  /** The Game object to render. */
  game: Game;

  /** Optional custom CSS class to merge with component styles. */
  class?: string;
}

const { game, class: className } = Astro.props;
---

<article class={`card ${className ?? ''}`} data-testid={`game-card-${game.id}`}>
  <!-- component markup -->
</article>
```

### Layout Documentation

```astro
---
/**
 * Main site layout wrapping all pages.
 * - Includes header, navigation, and footer
 * - Imports global styles (Tailwind)
 * - Uses semantic HTML structure
 */
interface Props {
  /** Page title (rendered in <title> and <h1>). */
  title: string;

  /** Optional additional HTML head content. */
  head?: string;
}

const { title, head = '' } = Astro.props;
---

<!DOCTYPE html>
<html lang="en">
  <!-- layout markup -->
</html>
```

## Inline Comments

Use inline comments sparingly. Reserve them for:

- **Complex algorithms**: Explain the approach at a high level.
- **Non-obvious control flow**: E.g. early returns that prevent deeply nested conditionals.
- **Workarounds**: Explain why we're doing something the unconventional way (reference the issue or decision if possible).

```ts
// Group games by category, filtering out unreleased ones.
// This prevents spam in the public listing while we finish development.
const publishedByCategory = games
  .filter((g) => g.releaseDate <= today)
  .reduce((acc, g) => {
    const key = g.category.name;
    acc[key] ??= [];
    acc[key].push(g);
    return acc;
  }, {} as Record<string, Game[]>);
```

Avoid inline comments that just restate the next line:

```ts
// Don't do this:
const count = items.length; // Get the count

// Do this if you need a comment at all:
// Only count items that are not archived (archived items don't count toward totals)
const count = items.filter((i) => !i.archived).length;
```

## Commit Messages

Commit messages should document the *why* of changes:

```
fix: ensure game sort is deterministic for static builds

Games are sorted by title to make pagination deterministic across builds.
Sorting by ID or creation time would cause page shuffles on rebuild.

Co-authored-by: Copilot App <223556219+Copilot@users.noreply.github.com>
```

## Summary

- **Comment *why*, not *what*.** Code already says what it does; explain intent.
- **Export TSDoc/JSDoc.** All exported functions and types in data/lib layers must be documented.
- **Document component Props.** Every reusable `.astro` component must document its interface.
- **Keep comments current.** Update or delete comments that are wrong or outdated.
- **Be concise.** A short, clear explanation beats a verbose one.
