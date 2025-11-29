# Next.js

## Overview

Next.js is a React framework for production that enables server-side rendering, static site generation, API routes, and excellent developer experience. It's maintained by Vercel and is one of the most popular frameworks for building modern web applications.

**Type**: Frontend Framework  
**License**: MIT (Open Source)  
**Primary Language**: JavaScript/TypeScript  
**Founded**: 2016  
**Website**: [nextjs.org](https://nextjs.org)

---

## What is Next.js?

Next.js is a full-stack React framework that provides everything you need to build production-ready web applications. It handles routing, rendering, API endpoints, and optimization out of the box.

### Key Characteristics

- **Server-Side Rendering (SSR)**: Render pages on the server
- **Static Site Generation (SSG)**: Pre-render pages at build time
- **API Routes**: Build backend API endpoints
- **File-Based Routing**: Automatic routing from file structure
- **Image Optimization**: Automatic image optimization
- **Code Splitting**: Automatic code splitting
- **TypeScript Support**: Built-in TypeScript support
- **Great DX**: Excellent developer experience

---

## Optimal Use Cases

### ✅ Best For

1. **Production React Applications**
   - E-commerce sites
   - SaaS dashboards
   - Content websites
   - Marketing sites

2. **SEO-Critical Applications**
   - Public-facing websites
   - Blog platforms
   - Marketing pages
   - Content sites

3. **Full-Stack Applications**
   - Apps needing API routes
   - Server-side logic
   - Database integration
   - Authentication

4. **JAMstack Applications**
   - Static site generation
   - Headless CMS integration
   - CDN-friendly deployments

5. **Enterprise Applications**
   - Large-scale applications
   - Team collaboration
   - Type safety with TypeScript
   - Performance optimization

6. **AI-Powered Applications**
   - Server-side AI integration
   - API routes for AI services
   - Real-time AI features

### ❌ Not Ideal For

1. **Simple Static Sites**
   - Overkill for basic HTML sites
   - Use plain HTML or simpler tools

2. **Mobile-Only Apps**
   - React Native better for mobile
   - Next.js is web-focused

3. **Very Simple SPAs**
   - Create React App might suffice
   - If you don't need SSR/SSG

---

## Architecture & Core Features

### Rendering Strategies

#### 1. Static Site Generation (SSG)
```typescript
// pages/blog/[slug].tsx
export async function getStaticProps({ params }) {
  const post = await getPost(params.slug);
  return { props: { post } };
}

export async function getStaticPaths() {
  const posts = await getAllPosts();
  return {
    paths: posts.map(post => ({ params: { slug: post.slug } })),
    fallback: false
  };
}
```

#### 2. Server-Side Rendering (SSR)
```typescript
// pages/profile.tsx
export async function getServerSideProps(context) {
  const user = await getUser(context.req);
  return { props: { user } };
}
```

#### 3. Incremental Static Regeneration (ISR)
```typescript
export async function getStaticProps() {
  return {
    props: { data },
    revalidate: 60 // Revalidate every 60 seconds
  };
}
```

#### 4. Client-Side Rendering (CSR)
```typescript
// Use React hooks for client-side data fetching
import { useEffect, useState } from 'react';

export default function Page() {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    fetch('/api/data').then(res => res.json()).then(setData);
  }, []);
  
  return <div>{data}</div>;
}
```

### Key Features

#### 1. App Router (Next.js 13+)
- New routing system
- Server Components
- Streaming
- Layouts
- Loading states

#### 2. API Routes
- Build backend endpoints
- Serverless functions
- Full Node.js API

#### 3. Image Optimization
- Automatic optimization
- Lazy loading
- Responsive images
- WebP/AVIF support

#### 4. Font Optimization
- Automatic font optimization
- Zero layout shift
- Custom fonts

---

## Comparison with Alternatives

### Next.js vs Create React App

| Feature | Next.js | Create React App |
|---------|---------|------------------|
| **SSR/SSG** | Yes | No |
| **API Routes** | Yes | No |
| **Routing** | File-based | React Router |
| **SEO** | Excellent | Poor |
| **Performance** | Optimized | Manual |
| **Deployment** | Easy | Manual |

**Choose Next.js when**: You need SSR, SEO, or API routes.  
**Choose CRA when**: Building simple client-side apps.

### Next.js vs Remix

| Feature | Next.js | Remix |
|---------|---------|-------|
| **Data Loading** | getServerSideProps | Loaders |
| **Forms** | Manual | Built-in |
| **Nested Routes** | Limited | Excellent |
| **Error Handling** | Manual | Built-in |
| **Focus** | SSR/SSG | Full-stack |

**Choose Next.js when**: You need SSG or larger ecosystem.  
**Choose Remix when**: You need nested routes or form handling.

### Next.js vs Gatsby

| Feature | Next.js | Gatsby |
|---------|---------|--------|
| **SSG** | Yes | Yes (primary) |
| **SSR** | Yes | Limited |
| **Data Sources** | Flexible | GraphQL-focused |
| **Plugin System** | Limited | Extensive |
| **Best For** | Full-stack | Content sites |

**Choose Next.js when**: You need SSR or API routes.  
**Choose Gatsby when**: Building content-heavy sites.

---

## Integration with AI Products

### API Routes for AI

```typescript
// app/api/chat/route.ts (App Router)
import { NextRequest, NextResponse } from 'next/server';
import OpenAI from 'openai';

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

export async function POST(request: NextRequest) {
  const { message } = await request.json();

  const completion = await openai.chat.completions.create({
    model: 'gpt-3.5-turbo',
    messages: [{ role: 'user', content: message }],
  });

  return NextResponse.json({
    response: completion.choices[0].message.content
  });
}
```

### Server Components with AI

```typescript
// app/page.tsx (Server Component)
import OpenAI from 'openai';

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

export default async function HomePage() {
  const completion = await openai.chat.completions.create({
    model: 'gpt-3.5-turbo',
    messages: [{ role: 'user', content: 'Hello' }],
  });

  return (
    <div>
      <h1>{completion.choices[0].message.content}</h1>
    </div>
  );
}
```

### Streaming AI Responses

```typescript
// app/api/stream/route.ts
import { NextRequest } from 'next/server';
import OpenAI from 'openai';

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

export async function POST(request: NextRequest) {
  const { message } = await request.json();

  const stream = await openai.chat.completions.create({
    model: 'gpt-3.5-turbo',
    messages: [{ role: 'user', content: message }],
    stream: true,
  });

  const encoder = new TextEncoder();
  const readable = new ReadableStream({
    async start(controller) {
      for await (const chunk of stream) {
        const content = chunk.choices[0]?.delta?.content || '';
        controller.enqueue(encoder.encode(content));
      }
      controller.close();
    },
  });

  return new Response(readable);
}
```

---

## Getting Started

### Installation

```bash
# Create new Next.js app
npx create-next-app@latest my-app

# With TypeScript
npx create-next-app@latest my-app --typescript

# With App Router
npx create-next-app@latest my-app --app
```

### Basic Usage

**Pages Router (Traditional):**

```typescript
// pages/index.tsx
export default function Home() {
  return <h1>Hello Next.js!</h1>;
}

// pages/about.tsx
export default function About() {
  return <h1>About Us</h1>;
}
```

**App Router (Next.js 13+):**

```typescript
// app/page.tsx
export default function Home() {
  return <h1>Hello Next.js!</h1>;
}

// app/about/page.tsx
export default function About() {
  return <h1>About Us</h1>;
}
```

### API Routes

```typescript
// pages/api/hello.ts (Pages Router)
import type { NextApiRequest, NextApiResponse } from 'next';

export default function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  res.status(200).json({ message: 'Hello from API!' });
}

// app/api/hello/route.ts (App Router)
import { NextResponse } from 'next/server';

export async function GET() {
  return NextResponse.json({ message: 'Hello from API!' });
}
```

### Environment Variables

```bash
# .env.local
DATABASE_URL=postgresql://...
OPENAI_API_KEY=sk-...
NEXT_PUBLIC_API_URL=https://api.example.com
```

```typescript
// Access environment variables
const apiKey = process.env.OPENAI_API_KEY; // Server-side only
const apiUrl = process.env.NEXT_PUBLIC_API_URL; // Client-side accessible
```

---

## Performance Considerations

### Best Practices

1. **Use SSG when possible**: Pre-render static pages
2. **Image Optimization**: Always use next/image
3. **Code Splitting**: Automatic, but be mindful of bundle size
4. **Caching**: Use ISR for dynamic content
5. **Font Optimization**: Use next/font
6. **Lazy Loading**: Use dynamic imports

### Image Optimization

```typescript
import Image from 'next/image';

<Image
  src="/hero.jpg"
  alt="Hero"
  width={800}
  height={600}
  priority // Load immediately
  placeholder="blur" // Blur placeholder
/>
```

### Dynamic Imports

```typescript
import dynamic from 'next/dynamic';

const HeavyComponent = dynamic(() => import('../components/Heavy'), {
  loading: () => <p>Loading...</p>,
  ssr: false // Disable SSR if needed
});
```

### Caching Strategy

```typescript
// ISR with revalidation
export async function getStaticProps() {
  return {
    props: { data },
    revalidate: 3600 // Revalidate every hour
  };
}
```

---

## Security Features

1. **Environment Variables**: Secure server-side variables
2. **API Routes**: Server-side only execution
3. **CSP Headers**: Content Security Policy support
4. **HTTPS**: Automatic with Vercel
5. **Middleware**: Request/response manipulation

### Security Best Practices

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  // Add security headers
  const response = NextResponse.next();
  response.headers.set('X-Frame-Options', 'DENY');
  response.headers.set('X-Content-Type-Options', 'nosniff');
  return response;
}
```

---

## When to Choose Next.js

### ✅ Choose Next.js If:

- Building production React applications
- Need SEO optimization
- Want server-side rendering
- Building full-stack applications
- Need API routes
- Want excellent performance
- Building SaaS products
- Need TypeScript support

### ❌ Consider Alternatives If:

- Building simple static sites (use plain HTML)
- Mobile-only apps (use React Native)
- Very simple SPAs (use Create React App)
- Need specific framework features (e.g., Remix nested routes)

---

## Resources

- **Documentation**: [nextjs.org/docs](https://nextjs.org/docs)
- **Learn**: [nextjs.org/learn](https://nextjs.org/learn)
- **Examples**: [github.com/vercel/next.js/tree/canary/examples](https://github.com/vercel/next.js/tree/canary/examples)
- **Blog**: [nextjs.org/blog](https://nextjs.org/blog)
- **Discord**: [nextjs.org/discord](https://nextjs.org/discord)

---

## Summary

Next.js is the premier framework for building production React applications. Its combination of SSR, SSG, API routes, and excellent developer experience makes it ideal for modern web applications, especially SaaS products and AI-powered applications.

**Best For**: Production React apps, SEO-critical sites, full-stack apps, SaaS products, AI applications  
**Not Ideal For**: Simple static sites, mobile-only apps, very simple SPAs

