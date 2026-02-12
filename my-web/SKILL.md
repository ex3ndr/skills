---
name: my-web
description: React web development conventions and patterns
---

# React Web Development Standards

When writing or reviewing React web application code, follow these principles.

## Stack

- **UI framework**: React with TypeScript (strict mode, no `any`)
- **Styling**: Tailwind CSS + shadcn/ui primitives
- **Routing**: TanStack Router (file-based route tree, loaders, auth guards)
- **State**: Zustand for client state, router loaders for server state
- **Build**: Vite

## Component Guidelines

### One Component Per File

Each file exports **one** React component. File name matches the component name: `MessageRow.tsx` exports `MessageRow`.

When a component has sub-components used only by it, group them in a folder:

```
Sidebar/
  Sidebar.tsx          -> main component
  SidebarItem.tsx      -> internal, used only by Sidebar
  SidebarSection.tsx   -> internal, used only by Sidebar
```

Simple components with no sub-components stay as standalone files — no folder needed. Do not put unrelated components in one file.

### Prefix-Style Naming

Component names use **domain-prefix** style — the domain comes first, then the specific part:

- `Sidebar`, `SidebarItem`, `SidebarSection` — not `Item`, `Section`
- `Message`, `MessageRow`, `MessageActions` — not `Row`, `Actions`
- `Channel`, `ChannelHeader`, `ChannelSettings` — not `Header`, `Settings`

The prefix makes it immediately clear which domain a component belongs to, even outside its folder. Grep-friendly and unambiguous.

Shared UI primitives (`components/ui/`) are the exception — `Button`, `Dialog`, `Avatar` need no domain prefix since they are domain-agnostic.

### Fragments

Fragments are large, self-contained UI regions — the biggest building blocks below a page. Think of them like Android Fragments or Atomic Design's organisms: a chat conversation, a profile editor, a settings form. Each fragment owns its own layout, internal state, and child components.

Fragments are **not** modals, panels, or wrappers — they are the main content regions that a route composes together. A page might render one fragment (full-screen chat) or several side by side (sidebar + chat + thread panel).

- Name fragments after what they **are**, not how they're displayed: `ChatConversation`, `ProfileEditor`, `ChannelSettings` — not `ChatModal`, `ProfilePanel`.
- A fragment manages its own internal component tree but receives its key data (IDs, config) as props from the route.
- Fragments live in a top-level `fragments/` folder. Each fragment gets its own folder, and fragment-specific components go in a `components/` subfolder within it:
  ```
  fragments/
    ChatConversation/
      ChatConversation.tsx          -> the fragment
      components/
        ChatMessageList.tsx         -> specific to this fragment
        ChatComposer.tsx            -> specific to this fragment
    ProfileEditor/
      ProfileEditor.tsx             -> the fragment
      components/
        ProfileAvatarUpload.tsx
        ProfileFormFields.tsx
  ```
- The separate top-level `components/` folder is for **shared, reusable** components (UI primitives, domain components used across multiple fragments). Fragment-specific components stay inside the fragment's own `components/` subfolder.
- Route files compose fragments — they don't build UI themselves:
  ```tsx
  function WorkspaceRoute() {
    const { channelId, threadId } = Route.useParams()
    return (
      <WorkspaceLayout>
        <Sidebar />
        <ChatConversation channelId={channelId} />
        {threadId && <ThreadPanel threadId={threadId} />}
      </WorkspaceLayout>
    )
  }
  ```

### Route Files Stay Thin

Route files handle **routing concerns only**: declaring the route, params, loaders, error boundaries, and rendering the top-level page component.

Move all business logic, layout, data transformation, and UI orchestration into dedicated components imported by the route.

```tsx
// Good: thin shell
function ChannelRoute() {
  const { channelId } = Route.useParams()
  return <ChannelView channelId={channelId} />
}

// Bad: entire page in the route file
function ChannelRoute() {
  const { channelId } = Route.useParams()
  const messages = useMessages(channelId)
  const members = useMembers(channelId)
  // ... 200 lines of UI, handlers, and logic
}
```

## Layout Stability with Loading States

Components with loading/placeholder states must never change their size when transitioning between loading and loaded. Reserve the exact dimensions so the layout stays stable.

- **List headers and footers**: if a list footer shows a "Load more" spinner or an "End of list" message, it must occupy the same height in both states. Use a fixed-height container or render an invisible placeholder of the same size.
- **Skeleton screens**: skeletons must match the dimensions of the real content they replace.
- **Pagination indicators**: when a paginated list reaches the end, swap the spinner for a static placeholder of the same size — never collapse to zero height.
- **General rule**: if a region can be in a loading state, wrap it in a container with explicit dimensions or `min-height` so surrounding content never shifts.

```tsx
// Good: fixed-height footer — no jump when loading ends
<div className="h-10 flex items-center justify-center">
  {isLoading ? <Spinner /> : hasMore ? null : <span className="text-muted">No more items</span>}
</div>

// Bad: footer collapses when loading ends, list jumps
{isLoading && <Spinner />}
```

## Avoid `useEffect`

`useEffect` is a code smell in most cases. Before reaching for it, consider alternatives:

- **Derived state**: compute inline during render or use `useMemo`. Never `useEffect` + `setState` to mirror a prop — just derive it.
- **Event handlers**: if something should happen in response to a user action, do it in the handler, not in an effect that watches for state changes.
- **Refs for imperative APIs**: use `useRef` + `useLayoutEffect` only when truly necessary (focus, scroll, measure).
- **Data fetching**: use router loaders or a dedicated fetching hook, not `useEffect` + `fetch` + `setState`.
- **Subscriptions**: use `useSyncExternalStore` or the store's own hook, not `useEffect` with manual subscribe/unsubscribe.

Legitimate uses are rare: setting up/tearing down non-React subscriptions with no hook abstraction, or one-time initialization with no better home. When you do use one, leave a comment explaining why.

## State Management

### Zustand Stores

- Separate stores by concern: `uiStore` (modals, drafts, sidebar), `connectionStore` (online/offline), `toastStore`.
- Keep stores small and focused — one responsibility per store.
- For selectors that return derived arrays or objects, use `useShallow` to keep snapshots stable and avoid render loops.

```tsx
// Good: stable selector with useShallow
const channels = useStore(useShallow((s) => s.channels.filter((c) => !c.archived)))

// Bad: creates new array reference every render
const channels = useStore((s) => s.channels.filter((c) => !c.archived))
```

### Data Fetching

- Use router loaders for data needed at route level.
- REST for mutations, SSE or WebSockets for real-time updates.
- Auth via bearer token in `Authorization` header.
- Keep API clients typed — one function per endpoint, return typed responses.

## File Organization

```
app/
  routes/          -> route files (thin shells only)
  fragments/       -> large self-contained UI regions (ChatConversation/, ProfileEditor/, ...)
    [Fragment]/
      Fragment.tsx
      components/  -> components specific to this fragment
  components/
    ui/            -> shadcn/ui primitives (button, input, dialog, avatar, etc.)
    [domain]/      -> shared domain components reused across fragments
  stores/          -> Zustand stores (one per concern)
  lib/             -> utilities, hooks, session management, route guards
  api/             -> typed API client, HTTP helpers, SSE subscriber
  types.ts         -> shared TypeScript types
```

### File Naming

- One public function/component per file. File name matches export name.
- Prefix notation for non-component files: `channelCreate` not `createChannel`, `messageSend` not `sendMessage`.
- `domainVerb.ts` + `domainVerb.spec.ts` side by side.
- Tests use `*.spec.ts`, live next to the file under test.
- Do not use barrel `index.ts` files.

## Conventions

- TypeScript only, ESM output.
- Keep files under ~700 LOC; split when it improves clarity.
- Brief comments for tricky or non-obvious logic only.
- Unix timestamps (milliseconds) for time values. `Date` only at boundaries for parsing/formatting.
- Prefer strict typing; avoid `any`.

## Dev Page

For non-trivial UI components, add a section on a dev route (`/dev`) for visual testing in isolation:

- Render the component with representative props covering key states (empty, loading, populated, error, long text, missing data).
- Each section: heading with component name, hardcoded/mock data, no dependency on real API state.
- Trivial components (styled wrappers, single-line formatters) don't need dev page entries.

## Visual Verification with Browser

Use the `agent-browser` skill to visually verify UI changes. After building or modifying components, launch the dev server and use agent-browser to navigate to the page, take screenshots, and confirm the result matches expectations.

- **After styling changes**: screenshot the affected page/component to verify layout and colors.
- **After adding new routes or pages**: navigate to the route and screenshot to confirm rendering.
- **Responsive checks**: resize the viewport and screenshot at different breakpoints.
- **Pixel-perfect comparison**: when matching a reference design, set the browser viewport to match the reference image dimensions exactly (account for `deviceScaleFactor` on Retina displays — a 1440x900 viewport at 2x produces a 2880x1800 screenshot).
- **Interactive flows**: fill forms, click buttons, and verify state transitions render correctly.

This replaces manual "open the browser and check" steps — let the agent verify visually.

## Summary

1. One component per file, domain-prefix names, colocate sub-components in folders
2. Route files are thin shells — logic lives in components
3. Avoid `useEffect` — derive state, handle events, use proper hooks
4. Zustand for client state with `useShallow` for derived selectors
5. Router loaders for data fetching, typed API clients for mutations
6. Prefix notation for files (`domainVerb.ts`), tests next to source
7. shadcn/ui + Tailwind for styling, keep the design system consistent
