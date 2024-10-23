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
# **Intercepting Routes**

Intercepting routes allows you to load a route from another part of your application within the current layout. This routing paradigm can be useful when you want to display the content of a route without the user switching to a different context.

use:

- `(.)` to match segments on the **same level**
- `(..)` to match segments **one level above**
- `(..)(..)` to match segments **two levels above**
- `(...)` to match segments from the **root** `app` directory

# Route Handlers

### **URL Query Parameters**

The request object passed to the Route Handler is a `NextRequest` instance, which has [some additional convenience methods](https://nextjs.org/docs/app/api-reference/functions/next-request#nexturl), including for more easily handling query parameters.

app/api/search/route.ts

TypeScript

JavaScriptTypeScript

```tsx
import { type NextRequest } from 'next/server'
 
export function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams
  const query = searchParams.get('query')
  // query is "hello" for /api/search?query=hello
}
```

# **headers**

`headers` is an **async** function that allows you to **read** the HTTP incoming request headers from a [Server Component](https://nextjs.org/docs/app/building-your-application/rendering/server-components).

app/page.tsx

```tsx
import { headers } from 'next/headers'
 
export default async function Page() {
  const headersList = await headers()
  const userAgent = headersList.get('user-agent')
}
```

# **cookies**

`cookies` is an **async** function that allows you to read the HTTP incoming request cookies in [Server Component](https://nextjs.org/docs/app/building-your-application/rendering/server-components), and read/write outgoing request cookies in [Server Actions](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations) or [Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers).

### getting cookie

```tsx
import { cookies } from 'next/headers'
 
export default async function Page() {
  const cookieStore = await cookies()
  const theme = cookieStore.get('theme')
  return '...'
}
```

### setting cookie

```tsx
'use server'
 
import { cookies } from 'next/headers'
 
export async function create(data) {
  const cookieStore = await cookies()
 
  cookieStore().set('name', 'lee')
  // or
  cookieStore().set('name', 'lee', { secure: true })
  // or
  cookieStore().set({
    name: 'name',
    value: 'lee',
    httpOnly: true,
    path: '/',
  })
}
```

### **Caching**

Route Handlers are not cached by default. You can, however, opt into caching for `GET` methods. Other supported HTTP methods are **not** cached. To cache a `GET` method, use a [route config option](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config#dynamic) such as `export const dynamic = 'force-static'` in your Route Handler file.

```tsx
export const dynamic = 'force-static'
 
export async function GET() {
  const res = await fetch('https://data.mongodb-api.com/...', {
    headers: {
      'Content-Type': 'application/json',
      'API-Key': process.env.DATA_API_KEY,
    },
  })
  const data = await res.json()
 
  return Response.json({ data })
}
```

- **Use Request Object**:
    - Reading from the request object (e.g., body, headers, cookies) automatically disables caching because the response may vary.
    - `const { cookies, headers } = req;`
- **Headers and Cookies**:
    - If the response is personalized based on headers or cookies, caching is automatically bypassed.
- **HTTP Methods Other Than GET**:
    - Methods like POST, PUT, DELETE are not cached by default as they modify data or are dynamic.
- Other supported HTTP methods are **not** cached, even if they are placed alongside a `GET` method that is cached, in the same file.

## Middleware

```tsx
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'
 
// This function can be marked `async` if using `await` inside
export function middleware(request: NextRequest) {
  return NextResponse.redirect(new URL('/home', request.url))
}
 
// See "Matching Paths" below to learn more
export const config = {
  matcher: '/about/:path*',
}
```
## Hydration

**Hydration** in React (and Next.js) is the process of turning static HTML (rendered on the server) into fully interactive client-side components. After the server sends the pre-rendered HTML, React "hydrates" it by attaching event listeners and restoring the app’s state on the client-side, enabling interactivity without re-rendering the entire page.

### Key Points:

- **SSR (Server-Side Rendering)** produces static HTML.
- **Hydration** happens when React takes over this HTML and makes it interactive.
- Ensures a smooth transition from server-rendered content to client-side functionality.

## Drawbacks of SSR - **All or Nothing Waterfall**

1. Data fetching must be completed before the server can begin rendering HTML.
2. The JavaScript required for the components needs to be fully loaded on the client side before the hydration process can start.
3. All components have to be hydrated before they become interactive.

These issues contribute to an "all-or-nothing" waterfall scenario, resulting in inefficiencies, especially if certain parts of your application are slower than others.



## **HTML streaming on the Server**

You don’t have to fetch everything before you can show anything.

If a particular section delays the initial HTML, it can be seamlessly integrated into the stream later.

This is the essence of how Suspense facilitates server-side HTML streaming.

## **Selective Hydration on the Client**

By wrapping the main section within `<Suspense>`, you've indicated to React that it should not prevent the rest of the page from not just streaming but also from hydrating.

This feature, called **selective hydration**, allows for the hydration of sections as they become available, before the rest of the HTML and the JavaScript code are fully downloaded.

Thanks to Selective Hydration, a heavy piece of JS doesn’t prevent the rest of the page from becoming interactive.

## Suspense

```tsx
import { Suspense } from 'react'
import { PostFeed, Weather } from './Components'
 
export default function Posts() {
  return (
    <section>
      <Suspense fallback={<p>Loading feed...</p>}>
        <PostFeed />
      </Suspense>
      <Suspense fallback={<p>Loading weather...</p>}>
        <Weather />
      </Suspense>
    </section>
  )
}
```

By using Suspense, you get the benefits of:

1. **Streaming Server Rendering** - Progressively rendering HTML from the server to the client.
2. **Selective Hydration** - React prioritizes what components to make interactive first based on user interaction.

- Selective Hydration offers a solution to the third issue: the necessity to "hydrate everything to interact with anything."
- React begins hydrating as soon as possible, enabling interactions with elements like the header and side navigation without waiting for the main content to be hydrated.
- This process is managed automatically by React.
- In scenarios where multiple components are awaiting hydration, React prioritizes hydration based on user interactions.

## **The Evolution of React**

CSR → SSR → Suspense for SSR

Suspense for SSR brought us closer to a seamless rendering experience.

**Challenges**

- Increased bundle sizes leading to excessive downloads for users
- Unnecessary hydration delaying interactivity
- Extensive client-side processing that could result in poor performance

To address these challenges React Server Components were introduced

## React Server Components

React Server Components allow you to write UI that can be rendered and optionally cached on the server. In Next.js, the rendering work is further split by route segments to enable streaming and partial rendering, and there are three different server rendering strategies:

- Static Rendering
- Dynamic Rendering
- Streaming

## [**Benefits of Server Rendering**]

There are a couple of benefits to doing the rendering work on the server, including:

- **Data Fetching**: Server Components allow you to move data fetching to the server, closer to your data source. This can improve performance by reducing time it takes to fetch data needed for rendering, and the number of requests the client needs to make.
- **Security**: Server Components allow you to keep sensitive data and logic on the server, such as tokens and API keys, without the risk of exposing them to the client.
- **Caching**: By rendering on the server, the result can be cached and reused on subsequent requests and across users. This can improve performance and reduce cost by reducing the amount of rendering and data fetching done on each request.
- **Performance**: Server Components give you additional tools to optimize performance from the baseline. For example, if you start with an app composed of entirely Client Components, moving non-interactive pieces of your UI to Server Components can reduce the amount of client-side JavaScript needed. This is beneficial for users with slower internet or less powerful devices, as the browser has less client-side JavaScript to download, parse, and execute.
- **Initial Page Load and [First Contentful Paint (FCP)]**: On the server, we can generate HTML to allow users to view the page immediately, without waiting for the client to download, parse and execute the JavaScript needed to render the page.
- **Search Engine Optimization and Social Network Shareability**: The rendered HTML can be used by search engine bots to index your pages and social network bots to generate social card previews for your pages.
- **Streaming**: Server Components allow you to split the rendering work into chunks and stream them to the client as they become ready. This allows the user to see parts of the page earlier without having to wait for the entire page to be rendered on the server.

## [**How are Client Components Rendered?**]

In Next.js, Client Components are rendered differently depending on whether the request is part of a full page load (an initial visit to your application or a page reload triggered by a browser refresh) or a subsequent navigation.

### [**Full page load**]

To optimize the initial page load, Next.js will use React's APIs to render a `static HTML preview` on the server for both Client and Server Components. This means, when the user first visits your application, they will see the content of the page immediately, without having to wait for the client to download, parse, and execute the Client Component JavaScript bundle.

On the server:

1. React renders Server Components into a special data format called the [**React Server Component Payload (RSC Payload)**], which includes references to Client Components.
2. Next.js uses the RSC Payload and Client Component JavaScript instructions to render **HTML** for the route on the server.

Then, on the client:

1. The HTML is used to immediately show a fast non-interactive initial preview of the route.
2. The React Server Components Payload is used to reconcile the Client and Server Component trees, and update the DOM.
3. The JavaScript instructions are used to hydrate Client Components and make their UI interactive.

## Summary

- Server components are rendered only on the server
- Client components are rendered once on the server and then on the client

## **Streaming**

Streaming is a strategy that allows for progressive UI rendering from the server.

Work is divided into chunks and streamed to the client as soon as it's ready.

This enables users to see parts of the page immediately, before the entire content has finished rendering.

Streaming significantly improves both the initial page loading performance and the rendering of UI elements that rely on slower data fetches, which would otherwise block the rendering of the entire route.

Streaming is integrated into the Next.js App Router by default.

## **Interleaving Server and Client Components**

### **Unsupported Pattern: Importing Server Components into Client Components**
```tsx
'use client'
 
// You cannot import a Server Component into a Client Component.
import ServerComponent from './Server-Component'
 
export default function ClientComponent({
  children,
}: {
  children: React.ReactNode
}) {
  const [count, setCount] = useState(0)
 
  return (
    <>
      <button onClick={() => setCount(count + 1)}>{count}</button>
 
      <ServerComponent />
    </>
  )
}
```

### **Supported Pattern: Passing Server Components to Client Components as Props**
```tsx
'use client'
 
import { useState } from 'react'
 
export default function ClientComponent({
  children,
}: {
  children: React.ReactNode
}) {
  const [count, setCount] = useState(0)
 
  return (
    <>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      {children}
    </>
  )
}
```

## **Data Cache**

Next.js has a built-in Data Cache that **persists** the result of data fetches across incoming **server requests** and **deployments**. This is possible because Next.js extends the native `fetch` API to allow each request on the server to set its own persistent caching semantics.

> In the browser, the `cache` option of `fetch` controls how a request interacts with the browser's HTTP cache. In Next.js, the `cache` option specifies how a server-side request interacts with the server's Data Cache. This means that even if you open the site in a new browser, the same cached data from the server will be sent, provided the cache options remain the same.
>

## **Request Memoization**

- Request memoization is a React feature, not specifically a Next.js feature.
- Memoization only applies to the GET method in fetch requests.
- Memoization only applies within the React Component tree. It does not extend to fetch requests in Route Handlers as they are not part of the React component tree.
- For cases where fetch is not suitable (e.g., some database clients, CMS clients, or GraphQL clients), you can use the React cache function to memoize functions.
