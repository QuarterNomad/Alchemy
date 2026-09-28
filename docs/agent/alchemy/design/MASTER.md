# Design System Master File

> **LOGIC:** When building a specific page, first check `docs/agent/alchemy/design/pages/[page-name].md`.
> If that file exists, its rules **override** this Master file.
> If not, strictly follow the rules below.

---

**Project:** Alchemy
**Verified:** 2026-08-17
**Category:** A-share stock screening data product (authenticated SPA)
**Style:** Data-Dense Dashboard + Soft UI Evolution (light elevation, 8–12px radius)
**Design Dials:** Variance 4/10 (Balanced / Modern) | Motion 3/10 (Subtle) | Density 6/10 on product chrome, 7/10 on tables

CLI `--design-system` raw output is **not** the source of truth. The following replaces unverified hits.

---

## Stack (do not deviate)

- Frontend: Vue 2.6 + Element UI 2.15 + existing `svg-icon`. **Not** Vue 3, Pinia, VeeValidate, GSAP, or Phosphor.
- CJK type: `Helvetica Neue, Helvetica, PingFang SC, Hiragino Sans GB, Microsoft YaHei, Arial, sans-serif`. Do not replace the whole stack with Fira / IBM Plex (no CJK). Hierarchy comes from size/weight/`font-variant-numeric: tabular-nums`, not a new font file.
- Icons: keep RuoYi SVG sprite + Element icons. Do not add a second icon family on product pages.
- Charts: daily snapshot tables only. No streaming area charts, no candlesticks on the screener.

## Discarded CLI recommendations

- **Enterprise Gateway** landing pattern (Contact Sales, video hero, logo carousel) — this is an authenticated workbench, not a marketing site.
- **Blue + amber** palette (`#1E40AF` / `#D97706`) and Element defaults `#409EFF` / `#1890ff`.
- **Dark OLED as app default** — login/register may stay dark; the workbench stays light.
- **`--stack vue` catalog** — entries target Vue 3.5; ignore as implementation instructions.
- **Neumorphism** — high contrast risk; use one soft card shadow instead.
- **Phosphor as a second icon set** on product pages.

---

## Color Palette (light workbench default)

| Role | Hex | CSS Variable |
|------|-----|--------------|
| Primary / CTA | `#15803D` | `--color-primary` |
| On Primary | `#FFFFFF` | `--color-on-primary` |
| Hover | `#166534` | `--color-primary-hover` |
| Secondary | `#1A1F2E` | `--color-secondary` |
| Background | `#F8FAFC` | `--color-background` |
| Foreground | `#1A1F2E` | `--color-foreground` |
| Card | `#FFFFFF` | `--color-card` |
| Card Foreground | `#1A1F2E` | `--color-card-foreground` |
| Muted | `#E2E8F0` | `--color-muted` |
| Muted Foreground | `#475569` | `--color-muted-foreground` |
| Border | `#E2E8F0` | `--color-border` |
| Destructive | `#DC2626` | `--color-destructive` |
| On Destructive | `#FFFFFF` | `--color-on-destructive` |
| Ring | `#15803D` | `--color-ring` |

`#15803D` is green-700 so white label text meets 4.5:1 on filled buttons. Do not use `#9aa5b5` / `#6b7b8f` for body or captions on white.

Primary is a highlight (filled search, active nav pill, metric badge). Do not paint large chrome with it. Do not use Element `warning` orange for export.

Element `$--color-primary`, Vuex `settings.theme` default, and sidebar active color must all be `#15803D`. If `localStorage` `layout-setting.theme` is `#409EFF` or `#1890ff`, treat as unset and migrate.

## Spacing

| Token | Value | Usage |
|-------|-------|-------|
| `--space-xs` | `2px` | Tight gaps |
| `--space-sm` | `4px` | Icon gaps |
| `--space-md` | `8px` | Compact padding |
| `--space-lg` | `12px` | Section gaps |
| `--space-xl` | `16px` | Card padding |
| `--space-2xl` | `24px` | Page inset |
| `--space-3xl` | `32px` | Title block |

## Radius, elevation, type

| Token | Value | Usage |
|-------|-------|-------|
| `--radius-md` | `8px` | Inputs, nav pills, badges |
| `--radius-lg` | `12px` | Product cards |
| `--shadow-card` | `0 1px 2px rgba(15,23,42,0.04), 0 8px 24px rgba(15,23,42,0.06)` | Workbench / profile cards |
| `--font-title` | `22px` | Page `h1` |
| `--font-body` | `14px` | Names, body |
| `--font-num` | `13px` | Tabular metrics |
| `--font-meta` | `12px` | Captions, table headers |
| `--table-row-height` | `48px` | Screener / product tables |

## Motion

- CSS only, 150–300ms, `ease` / `ease-out`. No GSAP.
- `:focus-visible { outline: 2px solid var(--color-ring); outline-offset: 2px; }`
- `html { scroll-padding-top: 84px; }` so sticky header + tagsView do not fully cover focus.
- `@media (prefers-reduced-motion: reduce)`: set layout/sidebar/search-panel transitions to `0`.
- ThemePicker `ORIGINAL_THEME` stays `#409EFF` to match fetched `theme-chalk` CSS. Compiled Element `$--color-primary` and Vuex default are `#15803D`.

## Interaction (product + chrome)

- Visible labels on forms; native `autocomplete` (`username` / `email` / `current-password` / `new-password` / `one-time-code`). Never `autocomplete="off"` on auth fields.
- Validate on blur (Element `trigger: "blur"`). Failed submit on auth pages: keep inline field errors and focus the first invalid field. Profile password form may still use a top-of-form error summary (`role="alert"`, `tabindex="-1"`).
- Native form submit so Enter works in every field.
- Page title is `h1`.
- Icon-only controls need `aria-label` (and `aria-expanded` when they toggle). Decorative icons beside visible text: `aria-hidden="true"`.
- Avatar menu: `trigger="click"` (not hover).
- 用户菜单「退出登录」直接登出，不弹确认框。
- 桌面产品侧栏右缘圆形钮折叠/展开，状态用现有 `sidebarStatus` cookie；移动端仍用汉堡，不显示该钮。
- Clickable elements: `cursor: pointer` and a hover/pressed change that does not shift layout.
- Loading: delay table spinner ~200ms; set `aria-busy` on the busy region. Do not flash for instant responses.
- Do not confirm non-destructive export. Do not live-search on every keystroke.
- Product sidebar: active item is a rounded pill (no 3px edge bar). Do not insert a second brand label under the logo.

### Notification center

- Entry: `NoticeCenterEntry` pinned at the **bottom of the product sidebar**, hairline divider above, between the menu scrollbar and `SidebarUser`; on mobile the same control moves to the navbar (hamburger right). Icon: RuoYi `svg-icon` sprite `bell` + label「通知中心」when expanded.
- Opens a right `el-drawer` titled「通知中心」, fixed width **400px** (`size="400px"`). Row/detail layout: see `docs/agent/alchemy/design/pages/notice.md`.
- Unread badge: Element `el-badge` on the bell; hidden when zero; `max` 99. `aria-label`:「通知中心」or「通知中心，{n} 条未读」.
- Guest read state: browser `localStorage` key `alchemy.noticeRead.ids` (JSON array of notice IDs); **does not** write `sys_notice_read`. Logged-in users use inbox APIs and the read table.
- Shell polls unread count every **60s** (`noticeInbox.POLL_MS`) via `notice/fetchUnread` on layout mount.
- **No** full-width top announcement bar / marquee for notices; inbox drawer only.

### Bookmark tip card

- After login, a bottom-right bookmark tip card may appear on the workbench shell.
- Warm orange gradient `#F59E0B` → `#EA580C`, not product primary green.
- Not an auto-dismiss toast; user dismisses via close / 知道了 / Escape.
- 「不再提示」 persists in localStorage; otherwise session only.
- Hidden on mobile. Do not steal focus. Existing `svg-icon` star + Element close icon.

## Anti-patterns

- Ornate decoration, emoji-as-icons, `outline: none` with no replacement
- Low-contrast muted gray on white
- Hover-only primary actions
- Streaming / OHLC charts on the screener
- Restyling every RuoYi CRUD page; system/monitor/tool inherit Element primary only
- Stretching a filter row across the full content width so Search / Export / icon buttons sit in leftover whitespace
