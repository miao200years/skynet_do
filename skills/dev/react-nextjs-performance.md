---
name: react-nextjs-performance
source: https://github.com/vercel-labs/agent-skills
author: Vercel Engineering (Official)
category: dev
tags: [React, Next.js, performance, optimization, frontend]
collected: 2026-05-08
---

# react-nextjs-performance

> React 和 Next.js 性能优化指南（40+ 规则，按影响力分级）

## 触发场景

- 写新的 React 组件或 Next.js 页面
- 实现数据获取（客户端或服务端）
- 代码 Review 中发现性能问题
- 优化打包体积或加载速度

## 高影响力规则（Critical）

### 消除瀑布请求

```tsx
// ❌ 串行请求（瀑布）
const user = await getUser(id)
const posts = await getPosts(user.id)  // 等待 user 完成后才开始

// ✅ 并行请求
const [user, posts] = await Promise.all([
  getUser(id),
  getPosts(id)  // 同时发起
])
```

### 打包体积优化

```tsx
// ❌ 导入整个库
import _ from 'lodash'
import { format } from 'date-fns'

// ✅ 按需导入
import debounce from 'lodash/debounce'
import { format } from 'date-fns/format'

// ✅ 动态导入（懒加载重型组件）
const HeavyChart = dynamic(() => import('./HeavyChart'), {
  ssr: false,
  loading: () => <Spinner />
})
```

## 服务端性能（High）

```tsx
// ✅ Server Components 处理数据获取（减少客户端 JS）
// app/page.tsx (Server Component - 默认)
async function Page() {
  const data = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 }  // ISR: 1小时缓存
  })
  return <DataDisplay data={data} />
}

// ✅ Streaming 提升感知性能
import { Suspense } from 'react'

function Page() {
  return (
    <>
      <Header />
      <Suspense fallback={<Skeleton />}>
        <SlowComponent />
      </Suspense>
    </>
  )
}
```

## 客户端数据获取（Medium-High）

```tsx
// ✅ 使用 SWR 或 React Query，内置缓存/去重/重试
import useSWR from 'swr'

function Profile({ id }) {
  const { data, error, isLoading } = useSWR(`/api/user/${id}`, fetcher)
  if (isLoading) return <Skeleton />
  if (error) return <Error />
  return <UserCard user={data} />
}
```

## 重渲染优化（Medium）

```tsx
// ✅ memo 避免不必要重渲染
const ExpensiveItem = memo(({ item, onSelect }) => {
  return <div onClick={() => onSelect(item.id)}>{item.name}</div>
})

// ✅ useCallback 稳定函数引用
const handleSelect = useCallback((id) => {
  setSelected(id)
}, [])  // 依赖为空 = 永远稳定

// ✅ useMemo 缓存计算结果
const sortedList = useMemo(
  () => items.sort((a, b) => a.price - b.price),
  [items]
)
```

## 图片与资源

```tsx
// ✅ 使用 Next.js Image（自动优化、懒加载、WebP）
import Image from 'next/image'

<Image
  src="/hero.jpg"
  width={800}
  height={400}
  alt="Hero image"
  priority  // 首屏图片加 priority
/>

// ✅ 字体优化（避免 FOUT）
import { Inter } from 'next/font/google'
const inter = Inter({ subsets: ['latin'], display: 'swap' })
```

## 性能检查清单

- [ ] 首屏无瀑布请求
- [ ] 重型组件做了动态导入
- [ ] 图片使用 next/image
- [ ] Server Components 处理数据获取
- [ ] 列表项使用了 memo
- [ ] 打包分析（`next build --analyze`）无意外大包
