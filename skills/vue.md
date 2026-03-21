---
name: vue
description: Vue 3 best practices, Composition API, Pinia, performance optimization, and component patterns. Use when building Vue components, working with composables, managing state with Pinia, or designing Vue application architecture.
argument-hint: [component/composable/pattern to implement]
allowed-tools: Read, Edit, Write, Bash, Glob, Grep
---

# Vue 3 Development

## Composition API 核心

### setup() 基础
```vue
<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'

// ref — 基本类型响应式
const count = ref(0)
const name = ref<string>('')

// reactive — 对象响应式（注意不要解构，会丢失响应性）
const state = reactive({ count: 0, name: '' })

// computed — 派生值
const doubled = computed(() => count.value * 2)

// watch
watch(count, (newVal, oldVal) => {
  console.log(newVal, oldVal)
})

// watchEffect — 自动追踪依赖
watchEffect(() => {
  console.log(count.value) // 自动追踪 count
})

onMounted(() => {
  // DOM 已挂载
})
</script>
```

### ref vs reactive
```ts
// ref: 基本类型、需要整体替换的对象
const user = ref<User | null>(null)
user.value = fetchedUser // 可以整体替换

// reactive: 不会整体替换的复杂对象
const form = reactive({
  email: '',
  password: '',
  remember: false,
})
form.email = 'test@example.com' // 直接赋值，无需 .value

// toRefs — 解构 reactive 时保持响应性
const { email, password } = toRefs(form)
```

## Composables（自定义组合式函数）

```ts
// composables/useCounter.ts
import { ref, computed } from 'vue'

export function useCounter(initial = 0) {
  const count = ref(initial)
  const doubled = computed(() => count.value * 2)

  function increment() { count.value++ }
  function decrement() { count.value-- }
  function reset() { count.value = initial }

  return { count, doubled, increment, decrement, reset }
}

// 使用
const { count, increment } = useCounter(10)
```

### 异步数据 composable
```ts
// composables/useFetch.ts
export function useFetch<T>(url: MaybeRef<string>) {
  const data = ref<T | null>(null)
  const error = ref<Error | null>(null)
  const loading = ref(false)

  async function execute() {
    loading.value = true
    error.value = null
    try {
      const res = await fetch(toValue(url))
      if (!res.ok) throw new Error(`HTTP ${res.status}`)
      data.value = await res.json()
    } catch (e) {
      error.value = e as Error
    } finally {
      loading.value = false
    }
  }

  watchEffect(() => { execute() })

  return { data, error, loading, refresh: execute }
}
```

## 组件设计

### Props & Emits
```vue
<script setup lang="ts">
// Props 类型定义
const props = defineProps<{
  title: string
  count?: number
  variant?: 'primary' | 'secondary'
}>()

// 带默认值
const props = withDefaults(defineProps<{
  count?: number
  variant?: 'primary' | 'secondary'
}>(), {
  count: 0,
  variant: 'primary',
})

// Emits
const emit = defineEmits<{
  update: [value: string]
  close: []
  change: [id: number, value: string]
}>()

emit('update', 'new value')
</script>
```

### v-model 双向绑定
```vue
<!-- 父组件 -->
<MyInput v-model="text" />
<MyInput v-model:title="title" v-model:content="content" />

<!-- 子组件 -->
<script setup lang="ts">
const model = defineModel<string>() // Vue 3.4+
// 或多个
const title = defineModel<string>('title')
const content = defineModel<string>('content')
</script>
```

### Slots
```vue
<!-- 具名插槽 -->
<template>
  <div>
    <slot name="header" />
    <slot />  <!-- 默认插槽 -->
    <slot name="footer" :data="footerData" />  <!-- 作用域插槽 -->
  </div>
</template>

<!-- 使用 -->
<MyCard>
  <template #header><h1>Title</h1></template>
  <p>Content</p>
  <template #footer="{ data }">{{ data }}</template>
</MyCard>
```

### expose
```vue
<script setup>
// setup 组件默认是封闭的，需要显式暴露
defineExpose({
  focus,
  reset,
})
</script>
```

## 状态管理 — Pinia

```ts
// stores/user.ts
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', () => {
  // state
  const user = ref<User | null>(null)
  const token = ref<string>('')

  // getters
  const isLoggedIn = computed(() => !!token.value)
  const displayName = computed(() => user.value?.name ?? 'Guest')

  // actions
  async function login(email: string, password: string) {
    const res = await api.login(email, password)
    user.value = res.user
    token.value = res.token
  }

  function logout() {
    user.value = null
    token.value = ''
  }

  return { user, token, isLoggedIn, displayName, login, logout }
})

// 使用
const userStore = useUserStore()
const { isLoggedIn } = storeToRefs(userStore) // 保持响应性
await userStore.login(email, password)
```

## 路由 — Vue Router 4

```ts
// router/index.ts
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      component: () => import('@/views/Home.vue'), // 懒加载
    },
    {
      path: '/user/:id',
      component: () => import('@/views/User.vue'),
      meta: { requiresAuth: true },
    },
  ],
})

// 路由守卫
router.beforeEach((to, from) => {
  const auth = useAuthStore()
  if (to.meta.requiresAuth && !auth.isLoggedIn) {
    return { path: '/login', query: { redirect: to.fullPath } }
  }
})

// 在组件中使用
const route = useRoute()
const router = useRouter()
const id = route.params.id
router.push('/home')
```

## 性能优化

### 组件懒加载
```ts
import { defineAsyncComponent } from 'vue'

const HeavyChart = defineAsyncComponent({
  loader: () => import('./HeavyChart.vue'),
  loadingComponent: Spinner,
  errorComponent: ErrorDisplay,
  delay: 200,
})
```

### v-memo
```vue
<!-- 只在 item.id 或 selected 变化时重新渲染 -->
<div v-for="item in list" :key="item.id" v-memo="[item.id, selected === item.id]">
  {{ item.name }}
</div>
```

### shallowRef / shallowReactive
```ts
// 大型对象只需要浅层响应性时用
const bigData = shallowRef<BigObject>({})
bigData.value = newBigObject // 触发更新
bigData.value.nested = 'x'  // 不触发更新（节省开销）
```

### KeepAlive
```vue
<KeepAlive :include="['UserList']" :max="5">
  <component :is="currentComponent" />
</KeepAlive>
```

## 常用指令速查

```vue
v-if / v-else-if / v-else   条件渲染（销毁/重建 DOM）
v-show                       条件显示（display:none，保留 DOM）
v-for="item in list"         列表渲染，搭配 :key
v-bind / :                   动态绑定 prop/attr
v-on / @                     事件监听
v-model                      双向绑定
v-slot / #                   插槽
v-once                       只渲染一次
v-pre                        跳过编译
```

## TypeScript 最佳实践

```ts
// 类型化 inject/provide
const ThemeKey: InjectionKey<Ref<string>> = Symbol('theme')
provide(ThemeKey, ref('light'))
const theme = inject(ThemeKey) // 类型自动推断

// 类型化 ref
const el = ref<HTMLButtonElement | null>(null)

// 类型化 emits
const emit = defineEmits<{
  (e: 'change', value: string): void
  (e: 'submit'): void
}>()
```

## 工具库推荐

| 用途 | 推荐库 |
|------|--------|
| 状态管理 | Pinia |
| 路由 | Vue Router 4 |
| 数据请求 | VueUse/useFetch, TanStack Query for Vue |
| 工具函数 | VueUse (200+ composables) |
| UI 组件 | Naive UI, Element Plus, shadcn-vue |
| 表单验证 | VeeValidate + Zod |
| 测试 | Vitest + Vue Test Utils |
| 构建 | Vite + @vitejs/plugin-vue |

## VueUse 常用 Composables

```ts
import {
  useLocalStorage,    // 持久化 localStorage
  useDark,            // 暗黑模式
  useDebounce,        // 防抖
  useThrottle,        // 节流
  useIntersectionObserver, // 元素可见性
  useEventListener,   // 事件监听（自动清理）
  useFetch,           // 数据请求
  useBreakpoints,     // 响应式断点
} from '@vueuse/core'

const isDark = useDark()
const stored = useLocalStorage('key', defaultValue)
