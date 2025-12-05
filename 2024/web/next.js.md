# Introduction
Next.js is a React framework for building full-stack web applications. You use React Components to build user interfaces, and Next.js for additional features and optimizations.  
Under the hood, Next.js also abstracts and automatically configures tooling needed for React, like bundling and more. This allows you to focus on building your application instead of spending time with configuration.  
Whether your're an individual developer or part of a larger team, Next.js can help you build interactive, dynamic, and fast React applications.  

## Main Features
Feature | Description
 -- | -- 
Routing | A file-system based router built on top of Server Components that supports layouts, nested routing, loading states, error handling, and more
Rendering | Client-side and Server-side Rendering with Client and Server Components. Further optimized with Static and Dynamic Rendering on the server with Next.js Streaming on Edge and Node.js runtimes.
Data Fetching | Simplified data fetching with async/await in Server Components, and an extended fetch API for request memoization, data caching and revalidation.
Styling | Support for your preferred styling methods, including CSS Modules, Tailwind CSS, and CSS-in-JS
Optimizations | Image, Fonts, and Script Optimizations to improve your application's Core Web Vitals and User Experience.
TypeScript | Improved support for TypeScript, with better type checking and more efficient compilation, as well as custom TypeScript Plugin and type checker.

# Installation
```
npx create-next-app@latest
```
## Manual Installation

## Hello World
layout.tsx
```
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html>
      <body>{children}</body>
    </html>
  )
}
```

page.tsx
```
export default function Page() {
  return <h1>Hello, World!</h1>
}
```
## The public folder(optional)
Create a public folder to store static assets such as images, fonts, etc. Files inside public directory can then be referenced by your code starting from the base URL(/).
## Run
```
npm run dev
```

# Project Structure
 app | App Router  
 pages | Pages Router  
 public | Static assets to be served  
 src | Optional application source folder
 
## app Routing Conventions

# Building Your Application
## Routing
The App Router works in a new directory named app. The app directory works alongside the pages directory to allow for incremental adoption. This allows you to opt some routes of your application into the new bwhavior while keeping other routes in the pages directory for previous behivor.  
By default, components inside app are React Server Components.
## Defining Routes
## Pages
A page is UI that is unique to a route. You can define a page by default exporting a component from a page.js file.  
app/dashboard/page.tsx
```typescript
export default function Page() {
  return <h1>Hello, Dashboard Page!</h1>
}
```
## Layouts and Templates
The special files layout.js and template.js allow you to create UI that is shared between routes.  
You can define a layout by default exporting a React component from a layout.js file. The component should accept a children prop that will be populated with a child layout or a page during rendering.  
```typescript
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <section>
      {/* Include shared UI here e.g. a header or sidebar */}
      <nav></nav>

      {children}
    </section>
  )
}
```
## Nesting Layouts
By default, layouts in the folder hierarchy are nested, which means they wrap child layouts via their children prop.  
Only the root layout can contain \<html\> and \<body\> tags.  
When a layout.js and page.js file are defined in the same folder, the layout will wrap the page.  
Layouts are Server Components by default but can be set to a Client Component.  
Layouts can fetch data.  
Passing data between a parent layout and its children is not possible. However, you can fetch the same data in a route more than once, and React will automatically dedupe the requests without affecting performance.  
Layouts do not have access to pathname. But imported Client Components can access the pathname using usePathname hook.  
Layouts do not have access to the route segments below itself.

## Templates
A template can be defined by exporting a default React component from a template.js file. The component should accept a children prop.
```typescript
export default function Template({children}: {children:React.ReactNode}) {
    return <div>{children}</div>
}
```

# Linking and Navigating
There are four ways to navigate between routes in Next.js:  
1. Using the \<Link\> Component  
2. using the useRouter hook(Client Components)  
3. Using the redirect function(Server Components)  
4. Using the native History API  

## \<Link\> Component
\<Link\> is a built-in component that extends the HTML \<a\> tag to provide prefetching and client-side navigation between routes. It is the primary and recommended way to navigate between routes in Next.js.  
```typescript
import Link from 'next/link'

export default function Page() {
    return <Link href="/dashboard">Dashboard</Link>
}
```
```typescript
import Link from 'next/link'

export default function PostList({posts}) {
    return (
        <ul>
            {posts.map((post) => (
                <li key={post.id}>
                    <Link href={`/blog/${post.slug}`}>{post.title}</Link>
                </li>
            ))}
        </ul>
    )
}
```
Scrolling to an id
```typescript
<Link href="/dashboard#settings">Settings</Link>
```
## useRouter() hook
The userRouter hook allows you to programmatically change routes from Client Components.
```typescript
import {useRouter} from 'next/navigation'

export default function Page() {
    const router = useRouter()

    return (
        <button type="button" onClick={() => router.push('/dashboard')}>Dashboard</button>
    )
}
```
## redirect function
For Server Components, use the redirect function instead.
## Using the native History API

# Project Organization
Even though route structure is defined through folders, a route is not publicly accessible until a page.js or route.js file is added to a route segment.  
Only the content returned by page.js or route.js is sent to the client.  
This is different from the pages directory, where any file in pages is considered a route.  

Next.js supports storing application code inside an optional src directory. 
```javascript
// before
import { Button } from '../../../components/button'
// after 
import { Button } from '@/components/button'
```

# Route Handlers
Route Handlers are defined in a route.js|ts file inside the app directory.  
```typescript
export async function GET(request: Request) {}
```
There cannot be a route.js file at the same route segment level as page.js.  
## Extended NextRequest and NextResponse APIs
Request, Response APIs and NextRequest, NextResponse APIs.  
## Caching
```
export const dynamic = 'force-static'

export async function GET() {
  const res = await fetch('https://data.cirray.cn/...', {
    headers: {
      'Content-Type': 'application/json',
      'API-Key': process.env.DATA_API_KEY,
    },
  })
  const data = await res.json()

  return Response.json({data})
}
```