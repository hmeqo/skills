# TypeScript Craft — Patterns and Conventions

> agent-docs 生成 TS/前端项目 AGENTS.md 时的语言规范来源。

## Toolchain

- 包管理：`pnpm`

## 禁止 any

业务代码禁止 any。

## 类型优先

能用类型系统编码的约束不留到运行时：

```ts
// literal union 代替 magic string
type Status = "idle" | "loading" | "success" | "error";

// const + union 代替散落常量
const Permission = { Read: "read", Write: "write" } as const;
type Permission = (typeof Permission)[keyof typeof Permission];

// enum 或 const enum
enum Color {
  Red,
  Green,
  Blue,
}
```

## type predicate 代替 as

```ts
// WRONG — as 是断言
const user = resp.data as User;

// CORRECT — type predicate 窄化
function isUser(data: unknown): data is User {
  return typeof data === "object" && data !== null && "id" in data;
}
```

## 封装过程噪声

重复逻辑提取函数，不裸露调用链：

```ts
function joinIds(items: { id: number }[]): string {
  return items.map((i) => i.id).join(",");
}
```

---

# Nuxt / Vue Craft（含 alova）

## 工具链

- 包管理：`pnpm`（dev / build / lint / typecheck）

## 应用结构

Nuxt 3 应用的三个入口各有分工：

- `app.vue` — 布局入口，组装 Provider 链（i18n → UI 组件库 → NuxtPage）
- `error.vue` — 全局错误边界，平台感知（web 返回 / Tauri 关闭）
- `spa-loading-template.html` — SPA 模式的首屏加载占位

## OpenAPI 类型自动对齐

后端 OpenAPI schema → 自动生成前端类型和转换函数。生成的代码标注"勿手动编辑"。

## 组件模式

| 场景      | 做法                                            |
| --------- | ----------------------------------------------- |
| UI 组件   | shadcn-vue — auto-imported                      |
| 底层      | reka-ui — 永远通过 shadcn wrapper               |
| v-model   | `defineModel<Type>('name', { required: true })` |
| 事件      | typed `defineEmits<{ success: [data: T] }>()`   |
| 图标-静态 | import from `@lucide/vue`                       |
| 图标-动态 | `<Icon>` with `material-symbols:xxx` (NuxtIcon) |

## 组件组织

按领域分包，不把业务组件摊在 `components/` 根目录：

```
components/
├── part/{domain}/    — 业务部件（用户、设备、订单）
├── layout/           — 布局组件
└── ui/               — shadcn
```

## 路由

路径参数和查询参数都从 `useRoute()` 解析，不做手写字符串拼接。

## 权限

路由级通过 `definePageMeta` 声明守卫权限，组件级在需要时控制 UI 显隐。权限规则集中定义，不在组件中硬编码。

## 状态持久化

类型化的 storage 访问器，将 `JSON.parse` / `JSON.stringify` 封装在背后，组件中不手写序列化。

## 无感更新

列表创建/编辑/删除后直接修改本地数组，不重新请求：

```ts
list.items.unshift(result);
list.total += 1;

const idx = items.findIndex((x) => x.id === id);
if (idx !== -1) items.splice(idx, 1);
```

## alova — API 调用

### Mutation

`immediate: false`，`send()` 返回响应数据本身：

```ts
const {
  send: create,
  loading,
  error,
} = useRequest(() => api.post("/devices", form.value), { immediate: false });
// send() 返回 Promise<RespType>
const result = await create();
```

### Query

`initialData` 填充默认值，避免判空：

```ts
const { data: device, send: refresh } = useRequest(
  () => api.get(`/devices/${id}`),
  { initialData: { name: "", status: "offline" } as Device },
);

const { data: list } = useRequest(() => api.get("/devices"), {
  initialData: [],
});
```

### API 客户端

`api` 实例统一管理 baseURL、拦截器、token 刷新，不在组件中构造 URL。
