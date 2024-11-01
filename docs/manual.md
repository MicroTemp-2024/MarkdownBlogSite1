# USER MANUAL

- Install Module

```bash
npm install @mdx-js/loader @next/mdx @types/mdx
```

- edit > `next.config.mjs` (next.js version 15)

```js
import createMDX from '@next/mdx'
 
/** @type {import('next').NextConfig} */
const nextConfig = {
    // Configure `pageExtensions` to include markdown and MDX files
    pageExtensions: ['js', 'jsx', 'md', 'mdx', 'ts', 'tsx'],
    // Optionally, add any other Next.js config below
}
 
const withMDX = createMDX({
    // Add markdown plugins here, as desired
})
 
// Merge MDX config with Next.js config
export default withMDX(nextConfig)
```

- Create File&Floder

```bash
  my-project
  ├── app
  │   └── mdx-page
  │       └── page.jsx
  ├── markdown
  │   └── welcome.mdx
  |── mdx-components.jsx
  └── package.json
```

- `/app/mdx-page/page.jsx`

```js
import Welcome from '@/markdown/welcome.mdx'

function CustomH1({ children }) {
  return <h1 style={{ color: 'blue', fontSize: '100px' }}>{children}</h1>
}
 
const overrideComponents = {
  h1: CustomH1,
}
 
export default function Page() {
  return <Welcome components={overrideComponents} />
}
```

- `/markdown/welcome.mdx`

```md
# This is a heading

This is a list in markdown :

- One
- Two
- Three

```

=

```html
<h1>This is a heading</h1>
 
<p>This is a list in markdown:</p>
 
<ul>
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

**NOTE** : can change styles `<h1>` => `/app/mdx-page/page.jsx`

หากต้องการกำหนดรูปแบบมาร์กดาวน์ คุณสามารถจัดเตรียมส่วนประกอบที่กำหนดเองซึ่งแมปกับองค์ประกอบ HTML ที่สร้างขึ้นได้ รูปแบบและส่วนประกอบสามารถนำไปใช้งาน globals ในเครื่อง และ กับเค้าโครงที่ใช้ร่วมกันได้

## Styles and Components Global

การเพิ่มสไตล์ และ ส่วนประกอบ `mdx-components.jsx` จะส่งผลต่อไฟล์ MDX ทั้งหมด ในแอปพลิเคชันของคุณ

- `/mdx-components.jsx`

```js
import Image from 'next/image'
 
// This file allows you to provide custom React components
// to be used in MDX files. You can import and use any
// React component you want, including inline styles,
// components from other libraries, and more.
 
export function useMDXComponents(components) {
    return {
        // Allows customizing built-in components, e.g. to add styling.
        h1: ({ children }) => (
            <h1 style={{ color: 'red', fontSize: '48px' }}>{children}</h1>
        ),
        img: (props) => (
            <Image
            sizes="100vw"
            style={{ width: '100%', height: 'auto' }}
            {...props}
            />
        ),
        ...components,
    }
}
```

## Styles and Local Components

คุณสามารถใช้ Styles และ Local Components กับหน้า page เฉพาะได้โดยส่งผ่านไปยังส่วนประกอบ MDX

- `/app/mdx-page/page.js`

```js
import Welcome from '@/markdown/welcome.mdx'
 
function CustomH1({ children }) {
     return <h1 style={{ color: 'blue', fontSize: '100px' }}>{children}</h1>
}
 
const overrideComponents = {
     h1: CustomH1,
}
 
export default function Page() {
    return <Welcome components={overrideComponents} />
}
```

## เค้าโครงที่ใช้ร่วมกัน

หากต้องการแบ่งปันเค้าโครงระหว่างหน้า MDX คุณสามารถใช้การรองรับเค้าโครงในตัวกับ App Router ได้

- `app/mdx-page/layout.js`

```js
export default function MdxLayout({ children }) {
  // Create any shared layout or styles here
  return <div style={{ color: 'blue' }}>{children}</div>
}
```

## การใช้ปลั๊กอิน Tailwind

- `app/mdx-page/layout.js`

```js
export default function MdxLayout({ children }) {
  // Create any shared layout or styles here
  return (
    <div className="prose prose-headings:mt-8 prose-headings:font-semibold prose-headings:text-black prose-h1:text-5xl prose-h2:text-4xl prose-h3:text-3xl prose-h4:text-2xl prose-h5:text-xl prose-h6:text-lg dark:prose-headings:text-white">
      {children}
    </div>
  )
}
```

## Frontmatter

Frontmatter คือการจับคู่คีย์/ค่าแบบ YAML ที่สามารถใช้เพื่อจัดเก็บข้อมูลเกี่ยวกับหน้าได้ โดย `@next/mdx` จะไม่รองรับ `frontmatter` ตามค่าเริ่มต้น แม้ว่าจะมีโซลูชันมากมายสำหรับการเพิ่ม `frontmatter` ลงในเนื้อหา `MDX` ของคุณ เช่น :

- [remark-frontmatter](https://github.com/remarkjs/remark-frontmatter)
- [remark-mdx-frontmatter](https://github.com/remcohaszing/remark-mdx-frontmatter)
- [gray-matter](https://github.com/jonschlinkert/gray-matter)

`@next/mdx` ช่วยให้คุณสามารถใช้การส่งออกได้เช่นเดียวกับส่วนประกอบ JavaScript อื่นๆ :

```bash
  my-project
  ├── app
  │   ├── mdx-page
  │   │   └── page.jsx
  │   └── blog
  │       └── page.jsx
  ├── markdown
  │   └── welcome.mdx
  ├── mdx-components.jsx
  └── package.json
```

ตอนนี้จะสามารถอ้างอิงข้อมูลเมตาจากภายนอกไฟล์ MDX ได้แล้ว :

- `/app/blog/page.jsx`

```js
import BlogPost, { metadata } from '@/content/blog-post.mdx'
 
export default function Page() {
  console.log('metadata: ', metadata)
  //=> { author: 'John Doe' }
  return <BlogPost />
}
```

กรณีการใช้งานทั่วไปสำหรับสิ่งนี้คือเมื่อคุณต้องการทำซ้ำในคอลเล็กชันของ MDX และแยกข้อมูล

## Remark and Rehype Plugins

- `next.config.mjs`

```js
import remarkGfm from 'remark-gfm'
import createMDX from '@next/mdx'
 
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Configure `pageExtensions`` to include MDX files
  pageExtensions: ['js', 'jsx', 'md', 'mdx', 'ts', 'tsx'],
  // Optionally, add any other Next.js config below
}
 
const withMDX = createMDX({
  // Add markdown plugins here, as desired
  options: {
    remarkPlugins: [remarkGfm],
    rehypePlugins: [],
  },
})
 
// Wrap MDX and Next.js config with each other
export default withMDX(nextConfig)
```

## Remote MDX

```bash
  my-project
  ├── app
  │   ├── mdx-page
  │   │   └── page.jsx
  │   ├── blog
  │   │   └── page.jsx
  │   └── mdx-page-remote
  │       └── page.jsx
  ├── markdown
  │   └── welcome.mdx
  ├── mdx-components.jsx
  └── package.json
```

- `app/mdx-page-remote/page.js`

```js
import { MDXRemote } from 'next-mdx-remote/rsc'
 
export default async function RemoteMdxPage() {
  // MDX text - can be from a local file, database, CMS, fetch, anywhere...
  const res = await fetch('https://...')
  const markdown = await res.text()
  return <MDXRemote source={markdown} />
}
```

## สรุป

นี่เป็นเพียงตัวอย่างการใช้งาน modules mdx แสดงผลไฟล์ Markdown หลังจากนี้คุณจะสามารถปรับแต่ง Styles ด้วยตัวของคุณเอง

ขอบคุณที่ติดตาม
