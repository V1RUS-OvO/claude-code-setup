---
name: react
description: React best practices, hooks, patterns, performance optimization, and component design. Use when building React components, debugging hooks, optimizing renders, or designing component architecture.
argument-hint: [component/hook/pattern to implement]
allowed-tools: Read, Edit, Write, Bash, Glob, Grep
---

# React Development

## 组件设计原则

- **单一职责** — 每个组件只做一件事
- **组合优于继承** — 用 children/render props 而非 class 继承
- **受控优先** — 表单组件默认用受控模式
- **最小 state** — 能从 props/其他 state 派生的不存 state

## Hooks 最佳实践

### useState
```tsx
// 复杂初始值用函数初始化（避免每次渲染重复计算）
const [state, setState] = useState(() => expensiveCompute())

// 对象 state 用展开合并
setState(prev => ({ ...prev, field: newValue }))

// 布尔切换
setState(prev => !prev)
```

### useEffect
```tsx
// 依赖项要完整，不要骗 linter
useEffect(() => {
  const controller = new AbortController()
  fetch(url, { signal: controller.signal })
    .then(r => r.json())
    .then(setData)
  return () => controller.abort() // 清理副作用
}, [url])

// 不需要同步到外部系统时，用 useMemo/派生值替代 useEffect
// BAD
useEffect(() => { setFullName(`${first} ${last}`) }, [first, last])
// GOOD
const fullName = `${first} ${last}`
```

### useCallback / useMemo
```tsx
// 只在真正有性能问题时用，不要过早优化
// 适合场景：传给 memo 组件的函数、作为其他 hook 依赖的函数
const handleClick = useCallback(() => {
  doSomething(id)
}, [id])

// 计算成本高的派生值
const sorted = useMemo(() => items.sort(compareFn), [items])
```

### useRef
```tsx
// 存储不触发重渲染的可变值
const timerRef = useRef<number>(null)
const prevValueRef = useRef(value)

// DOM 引用
const inputRef = useRef<HTMLInputElement>(null)
<input ref={inputRef} />
```

### 自定义 Hook
```tsx
// 封装可复用的有状态逻辑
function useLocalStorage<T>(key: string, initial: T) {
  const [value, setValue] = useState<T>(() => {
    try {
      const item = localStorage.getItem(key)
      return item ? JSON.parse(item) : initial
    } catch {
      return initial
    }
  })

  const set = useCallback((v: T) => {
    setValue(v)
    localStorage.setItem(key, JSON.stringify(v))
  }, [key])

  return [value, set] as const
}
```

## 性能优化

### React.memo
```tsx
// 只在父组件频繁重渲染且子组件渲染开销大时使用
const ExpensiveChild = memo(function ExpensiveChild({ data }: Props) {
  return <div>{/* 复杂渲染 */}</div>
})
```

### 避免不必要的重渲染
```tsx
// 状态下移 — 把频繁变化的 state 放到最近需要它的组件
// 内容提升 — 把稳定的 JSX 作为 children 传入
function ParentWithCounter() {
  return (
    <StableWrapper>
      <HeavyComponent />
    </StableWrapper>
  )
}

function StableWrapper({ children }: { children: ReactNode }) {
  const [count, setCount] = useState(0)
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      {children} {/* HeavyComponent 不会因 count 变化重渲染 */}
    </div>
  )
}
```

### 列表渲染
```tsx
// key 用稳定唯一 ID，不要用 index（除非列表不会重排）
{items.map(item => (
  <Item key={item.id} data={item} />
))}

// 长列表用虚拟化
import { useVirtualizer } from '@tanstack/react-virtual'
```

## 状态管理

### 选型参考
```
本地 UI state        → useState / useReducer
跨组件共享 (少量)    → Context + useContext
跨组件共享 (频繁更新) → Zustand / Jotai
服务端数据           → TanStack Query / SWR
复杂全局状态         → Redux Toolkit (大型项目)
```

### Context 正确用法
```tsx
// 把频繁变化的值和稳定的值拆成不同 Context
const ThemeContext = createContext<Theme>('light')    // 稳定
const UserContext = createContext<User | null>(null)  // 中频
const CartContext = createContext<CartState>(...)     // 频繁

// Provider 组件单独抽出
export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<Theme>('light')
  const value = useMemo(() => ({ theme, setTheme }), [theme])
  return <ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>
}
```

### Zustand (推荐轻量方案)
```tsx
import { create } from 'zustand'

interface BearStore {
  count: number
  increment: () => void
  reset: () => void
}

const useBearStore = create<BearStore>(set => ({
  count: 0,
  increment: () => set(state => ({ count: state.count + 1 })),
  reset: () => set({ count: 0 }),
}))

// 使用时按需订阅，避免过度重渲染
const count = useBearStore(state => state.count)
```

## 数据获取

### TanStack Query
```tsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'

function UserList() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(r => r.json()),
    staleTime: 5 * 60 * 1000, // 5 分钟内不重新请求
  })

  if (isLoading) return <Spinner />
  if (error) return <Error message={error.message} />
  return <ul>{data.map(u => <li key={u.id}>{u.name}</li>)}</ul>
}

// 乐观更新
const queryClient = useQueryClient()
const mutation = useMutation({
  mutationFn: updateUser,
  onSuccess: () => queryClient.invalidateQueries({ queryKey: ['users'] }),
})
```

## 常用模式

### 条件渲染
```tsx
// 用 && 时注意 falsy 0 会被渲染
{count > 0 && <Badge count={count} />}  // GOOD
{count && <Badge count={count} />}      // BAD — count=0 时渲染 "0"

// 多分支用三元或提前 return
if (isLoading) return <Spinner />
if (error) return <Error />
return <Content />
```

### 错误边界
```tsx
import { ErrorBoundary } from 'react-error-boundary'

<ErrorBoundary
  fallback={<ErrorFallback />}
  onError={(error, info) => logError(error, info)}
>
  <RiskyComponent />
</ErrorBoundary>
```

### 表单处理
```tsx
// React Hook Form — 性能最好，最少重渲染
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
})

function LoginForm() {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(schema)
  })
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}
    </form>
  )
}
```

## TypeScript 集成

```tsx
// 组件 Props 类型
interface ButtonProps {
  variant?: 'primary' | 'secondary'
  onClick?: () => void
  children: ReactNode
  disabled?: boolean
}

// 泛型组件
function List<T extends { id: string }>({ items, renderItem }: {
  items: T[]
  renderItem: (item: T) => ReactNode
}) {
  return <ul>{items.map(item => <li key={item.id}>{renderItem(item)}</li>)}</ul>
}

// 事件类型
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {}
const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {}
```

## 常用工具库

| 用途 | 推荐库 |
|------|--------|
| 状态管理 | Zustand, Jotai |
| 服务端数据 | TanStack Query |
| 表单 | React Hook Form + Zod |
| 路由 | React Router v6 / TanStack Router |
| 动画 | Framer Motion |
| UI 组件 | shadcn/ui, Radix UI |
| 虚拟列表 | @tanstack/react-virtual |
| 测试 | Vitest + React Testing Library |
