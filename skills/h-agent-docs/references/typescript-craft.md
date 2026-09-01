# TypeScript Craft — Patterns and Conventions

> The source of language conventions when agent-docs generates AGENTS.md for TS/frontend projects.

## Toolchain

- Package management: `pnpm`

## No any

Business code forbids any: use `unknown` + narrowing for unknown values, not any.

## Type-first

Constraints encodable in the type system aren't deferred to runtime:

```ts
// literal union instead of magic string
type Status = "idle" | "loading" | "success" | "error";

// const + union instead of scattered constants
const Permission = { Read: "read", Write: "write" } as const;
type Permission = (typeof Permission)[keyof typeof Permission];

// union / as const first; enum is incompatible with isolatedModules (enabled by default in Nuxt/Vite; const enum causes runtime errors)
type Color = "red" | "green" | "blue";
```

## type predicate instead of as

```ts
// WRONG — as is an assertion
const user = resp.data as User;

// CORRECT — type predicate narrows
function isUser(data: unknown): data is User {
  return typeof data === "object" && data !== null && "id" in data;
}
```

## Discriminated Union + exhaustive switch

Use discriminated unions (tagged unions) for polymorphic data; an exhaustive `never` switch makes the compiler enforce coverage of all variants:

```ts
type Action =
  | { type: "http"; url: string }
  | { type: "delay"; seconds: number };

function handle(a: Action): void {
  switch (a.type) {
    case "http":
      break;
    case "delay":
      break;
    default: {
      const _exhaustive: never = a;
      return _exhaustive;
    }
  }
}
```

After a new variant is added, `_exhaustive: never = a` fails to compile, forcing you to handle the new variant.

## JSDoc / TSDoc (public API)

JSDoc/TSDoc follows the project convention; when adopted, give callers the contract (parameters/returns/generics).

## Extract Named Functions

Extract repeated logic into functions; don't expose raw call chains:

```ts
function joinIds(items: { id: number }[]): string {
  return items.map((i) => i.id).join(",");
}
```

---

# Nuxt / Vue Craft (incl. alova)

## Toolchain

- Package management: `pnpm`; commands: dev / build / lint / typecheck

## Application Structure

The three entry points of a Nuxt 3 app each have their own role:

- `app.vue` — layout entry, assembling the Provider chain (i18n → UI component library → NuxtPage)
- `error.vue` — global error boundary, platform-aware (web returns / Tauri closes)
- `spa-loading-template.html` — first-screen loading placeholder for SPA mode

## Automatic OpenAPI Type Alignment

Backend OpenAPI schema → automatically generated frontend types and conversion functions. Generated code is marked "do not edit manually".

## Component Patterns

| Scenario     | Approach                                         |
| ------------ | ------------------------------------------------ |
| UI components| shadcn-vue — auto-imported                       |
| Low-level    | reka-ui — always through the shadcn wrapper      |
| v-model      | `defineModel<Type>('name', { required: true })`  |
| Events       | typed `defineEmits<{ success: [data: T] }>()`    |
| Icons - static | import from `@lucide/vue`                      |
| Icons - dynamic | `<Icon>` with `material-symbols:xxx` (NuxtIcon) |

## Component Organization

Group by domain into subdirectories; don't spread business components across the `components/` root:

```
components/
├── part/{domain}/    — business parts (users, devices, orders)
├── layout/           — layout components
└── ui/               — shadcn
```

## Routing

Path params and query params are both parsed from `useRoute()`; no hand-written string concatenation.

## Permissions

Route-level guard permissions are declared via `definePageMeta`; component-level UI visibility is controlled when needed. Permission rules are defined centrally, not hardcoded in components.

## State Persistence

Typed storage accessors wrapping `JSON.parse` / `JSON.stringify` behind the scenes; no hand-written serialization in components.

## Seamless Updates

After a mutation completes, **write the freshly returned server content back into local data** (create: insert at the top of the list; edit: write back to the original object reference — must be the same reference as the list element; delete: remove from the list); don't re-request, don't refresh the page; all referencing parties immediately see the new effect. Server returns are authoritative; no local guessing. Lists that don't hold the data / backend aggregations only re-request when a full refresh is needed.

## alova — API Calls

### Mutation

`immediate: false`; `send()` returns the response data itself:

```ts
const {
  send: create,
  loading,
  error,
} = useRequest(() => api.post("/devices", form.value), { immediate: false });
// send() returns Promise<RespType>
const result = await create();
```

### Query

`initialData` fills default values, avoiding null checks:

```ts
const { data: device, send: refresh } = useRequest(
  () => api.get(`/devices/${id}`),
  { initialData: { name: "", status: "offline" } as Device },
);

const { data: list } = useRequest(() => api.get("/devices"), {
  initialData: [],
});
```

### API Client

The `api` instance centrally manages baseURL, interceptors, and token refresh; URLs are not constructed in components.