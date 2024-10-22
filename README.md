# LastMinuteNextJs
# React vs Next.js - Intro Differences

### React:
- A **library** for building user interfaces, focusing on client-side rendering.
- Provides flexibility for building single-page applications (SPAs) but requires additional setup for routing, server-side rendering (SSR), etc.

### Next.js: (Mainly used to build production-ready applications)
- A **framework** built on top of React, providing extra features like SSR, static site generation (SSG), API routes, and routing out of the box.
- Ideal for SEO optimization, performance improvements, and full-stack capabilities.

"React is a UI library for building SPAs, while Next.js is a framework that enhances React by offering features like SSR, SSG, routing, and full-stack capabilities, making it more suited for performance and SEO-critical applications."

---

## Catch All Segments with Slug

Here’s a simple beginner example of handling a slug as a string array in Next.js, including the use of `slug.length` in the component.

### Code Example:

1. **Create a Dynamic Route File**:
    
    In the `pages` directory, create `[...slug].js` for dynamic route handling as an array.
    
2. **Handling `slug` as an Array**:
    
    ```jsx
    // [...slug]/page.tsx
    import { useRouter } from 'next/router';
    
    export default function SlugPage({ slug }) {
      const router = useRouter();
    
      // Show loading during fallback state
      if (router.isFallback) {
        return <div>Loading...</div>;
      }
    
      // Handling slug length
      if (slug.length === 1) {
        return <h1>Single slug: {slug[0]}</h1>;
      } else if (slug.length > 1) {
        return (
          <div>
            <h1>Multiple slugs:</h1>
            <ul>
              {slug.map((part, index) => (
                <li key={index}>{part}</li>
              ))}
            </ul>
          </div>
        );
      } else {
        return <h1>No slugs provided</h1>;
      }
    }
    
    export async function getStaticPaths() {
      return {
        paths: [],
        fallback: true,
      };
    }
    
    export async function getStaticProps({ params }) {
      const { slug } = params;
      return {
        props: { slug }, // Pass slug array to the component
      };
    }
    ```

### Explanation:
- **`[...slug]`**: The `...` syntax captures all parts of the URL after the `/` into an array, e.g., `/example/slug/parts` becomes `['example', 'slug', 'parts']`.
- **Optional catch all segments**: Can be handled using `[[...slug]]`.
- **`slug.length`**: You can check the length of the `slug` array to handle different cases (e.g., a single slug or multiple parts).

---

## [**`notFound()`**](https://nextjs.org/docs/app/api-reference/functions/not-found#notfound)

Invoking the `notFound()` function throws a `NEXT_NOT_FOUND` error and terminates rendering of the route segment in which it was thrown. Specifying a [**not-found** file](https://nextjs.org/docs/app/api-reference/file-conventions/not-found) allows you to gracefully handle such errors by rendering a Not Found UI within the segment.

### Example:

```javascript
import { notFound } from 'next/navigation';
 
async function fetchUser(id) {
  const res = await fetch('https://...');
  if (!res.ok) return undefined;
  return res.json();
}
 
export default async function Profile({ params }) {
  const user = await fetchUser(params.id);
 
  if (!user) {
    notFound();
  }
 
  // ....
}
   ```



# Next.js Key Concepts and Features

## Private Folders

Private folders are not required for colocation, but they can be useful for:

- Separating UI logic from routing logic.
- Consistently organizing internal files across a project and the Next.js ecosystem.
- Sorting and grouping files in code editors.
- Avoiding potential naming conflicts with future Next.js file conventions.

### URL Segments:
You can create URL segments that start with an underscore by prefixing the folder name with `%5F` (the URL-encoded form of an underscore): `%5FfolderName`.

---

## Metadata - Title

In Next.js, metadata like the page title can be defined in different ways to control how the title is set across pages:

- **Absolute**: A fully defined title that overrides all other titles.
  - Example: `"My Website - Home"`
  - This completely sets the page title as provided.
  
- **Default**: A fallback title applied when no specific title is set for a page.
  - Example: `"My Website"`
  - Used when no page-specific title is defined.

- **Template**: Allows for dynamic title structures using placeholders like `%s`.
  - Example: `"%s - My Website"`
  - If the page title is `"About"`, the full title will be `"About - My Website"`.

---

## Next.js `<Link>` Component

In Next.js, the `<Link>` component's `replace` prop controls how navigation affects the browser's history.

### Key Points:
- **`replace={true}`**: Replaces the current history entry with the new URL. The "Back" button won't return the user to the previous page.
- **`replace={false}`** (default): Pushes the new URL onto the history stack, allowing users to return to the previous page using the "Back" button.

### Example Usage:

```jsx
import Link from 'next/link';

<Link href="/about" replace={true}>
  Go to About
</Link>
   ```

# Error Handling and Parallel Routes in Next.js

## Error Boundaries

Error boundaries in Next.js must be Client Components to catch errors in rendering, lifecycle methods, and constructors of child components. They allow you to catch and handle errors without crashing the entire app.

### Example: Error Boundary Component

```tsx
'use client'; // Error boundaries must be Client Components

import { useEffect } from 'react';

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    // Log the error to an error reporting service
    console.error(error);
  }, [error]);

  return (
    <div>
      <h2>Something went wrong!</h2>
      <button
        onClick={() => reset()}
      >
        Try again
      </button>
    </div>
  );
}
   ```
## Slots in Parallel Routes

In Next.js, **parallel routes** allow for rendering different sections of a page simultaneously using **slots**. Slots help to break down complex layouts into manageable parts, improving maintainability and performance.

### Example: Layout with Slots

```tsx
export default function Layout({
  children,
  team,
  analytics,
}: {
  children: React.ReactNode;
  analytics: React.ReactNode;
  team: React.ReactNode;
}) {
  return (
    <>
      {children}
      {team}
      {analytics}
    </>
  );
}
   ```
