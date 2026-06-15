---
name: vue-sidebar-redesign
description: Redesigns a Vue 3 app's layout from a horizontal top navigation bar to a modern vertical sidebar SaaS layout. Rewrites App.vue with a fixed 240px dark sidebar, vertical nav links with inline SVG icons, and a sticky top bar. Preserves all script logic, composables, modals, and global component styles.
---

# Vue Sidebar Redesign

Converts a Vue 3 application from a sticky horizontal top-nav layout to a professional SaaS-style vertical sidebar layout. Only `App.vue` and one line in `FilterBar.vue` change — all views, components, composables, and backend code are untouched.

## Discovery — Read These Files First

Before writing a single line, read all five files:

1. `client/src/App.vue` — identify the current template structure and every CSS rule that belongs to nav/layout vs. global component styles
2. `client/src/components/FilterBar.vue` — find the `top: Npx` value in `.filters-bar` (must change to `top: 0`)
3. `client/src/components/ProfileMenu.vue` — note the exact class names: `.profile-button`, `.profile-name`, `.chevron`, `.dropdown-menu`
4. `client/src/components/LanguageSwitcher.vue` — note: `.language-button`, `.language-label`, `.globe-icon`, `.chevron`, `.dropdown-menu`
5. `client/src/main.js` — confirm all route paths to write accurate `:class="{ active: $route.path === '...' }"` bindings

## What to Preserve Without Any Change

- The **entire `<script>` block** — all imports, `export default`, `components`, `setup()`, refs, computed, async methods, `onMounted`. Do not touch a single line.
- All **global CSS** from `.page-header` downward: `.page-header`, `.stats-grid`, `.stat-card` (all variants), `.card`, `.card-header`, `.card-title`, `.table-container`, `table`, `thead`, `th`, `td`, `tbody tr`, `.badge` (all variants), `.loading`, `.error`. These are app-wide styles used by every view.
- The global reset (`*`) and `body` rules.
- All **modal component APIs**: `ProfileDetailsModal` still takes `:is-open` and `@close`; `TasksModal` still takes `:is-open`, `:tasks`, and emits `@close`, `@add-task`, `@delete-task`, `@toggle-task`.

## Target Template Structure

Replace the entire `<template>` block with this structure:

```html
<template>
  <div class="app">
    <aside class="sidebar">
      <div class="sidebar-brand">
        <span class="sidebar-brand-name">{{ t('nav.companyName') }}</span>
        <span class="sidebar-brand-sub">{{ t('nav.subtitle') }}</span>
      </div>

      <nav class="sidebar-nav">
        <router-link to="/" :class="{ active: $route.path === '/' }">
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <rect x="2" y="2" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
            <rect x="10" y="2" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
            <rect x="2" y="10" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
            <rect x="10" y="10" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
          </svg>
          {{ t('nav.overview') }}
        </router-link>

        <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }">
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <rect x="2" y="2" width="14" height="14" rx="2" stroke="currentColor" stroke-width="1.5"/>
            <path d="M6 6h6M6 9h6M6 12h4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          {{ t('nav.inventory') }}
        </router-link>

        <router-link to="/orders" :class="{ active: $route.path === '/orders' }">
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <path d="M3 5h12M3 9h8M3 13h5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          {{ t('nav.orders') }}
        </router-link>

        <router-link to="/spending" :class="{ active: $route.path === '/spending' }">
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <circle cx="9" cy="9" r="7" stroke="currentColor" stroke-width="1.5"/>
            <path d="M9 5v8M7 7h3a1 1 0 010 2H8a1 1 0 000 2h3" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          {{ t('nav.finance') }}
        </router-link>

        <router-link to="/demand" :class="{ active: $route.path === '/demand' }">
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <path d="M2 14l4-5 3 3 3-4 4-4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
          {{ t('nav.demandForecast') }}
        </router-link>

        <router-link to="/reports" :class="{ active: $route.path === '/reports' }">
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <rect x="3" y="2" width="12" height="14" rx="1" stroke="currentColor" stroke-width="1.5"/>
            <path d="M6 6h6M6 9h6M6 12h3" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          Reports
        </router-link>
      </nav>

      <div class="sidebar-footer">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="main-wrapper">
      <header class="top-bar">
        <FilterBar />
      </header>
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />
    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>
```

## CSS Rules to Remove

Delete these rules entirely from the `<style>` block — they belong to the old horizontal nav:

- `.app` (will be replaced)
- `.top-nav`
- `.nav-container`
- `.nav-container > .nav-tabs`
- `.nav-container > .language-switcher`
- `.logo`
- `.logo h1`
- `.subtitle`
- `.nav-tabs`
- `.nav-tabs a`
- `.nav-tabs a:hover`
- `.nav-tabs a.active`
- `.nav-tabs a.active::after`
- `.main-content` (will be replaced)

## CSS Rules to Add

Insert these new layout and sidebar rules between `body {}` and `.page-header {}`:

```css
/* ── Layout shell ── */
.app {
  display: flex;
  flex-direction: row;
  min-height: 100vh;
}

/* ── Sidebar ── */
.sidebar {
  position: fixed;
  top: 0;
  left: 0;
  width: 240px;
  height: 100vh;
  background: #0f172a;
  display: flex;
  flex-direction: column;
  z-index: 100;
  /* No overflow: hidden — dropdowns in the footer must be able to escape the sidebar bounds */
}

.sidebar-brand {
  padding: 1.5rem 1.25rem 1rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  flex-shrink: 0;
}

.sidebar-brand-name {
  display: block;
  font-size: 1rem;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: -0.025em;
  line-height: 1.3;
}

.sidebar-brand-sub {
  display: block;
  font-size: 0.75rem;
  color: rgba(255, 255, 255, 0.45);
  margin-top: 0.25rem;
}

/* ── Sidebar navigation ── */
.sidebar-nav {
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
  padding: 0.75rem;
  flex: 1;
  overflow-y: auto;
}

.sidebar-nav a {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.875rem;
  color: rgba(255, 255, 255, 0.6);
  text-decoration: none;
  font-size: 0.875rem;
  font-weight: 500;
  border-radius: 6px;
  transition: all 0.15s ease;
  position: relative;
}

.sidebar-nav a:hover {
  color: #ffffff;
  background: rgba(255, 255, 255, 0.07);
}

.sidebar-nav a.active {
  color: #ffffff;
  background: rgba(37, 99, 235, 0.35);
}

/* Active indicator: vertical left stripe (replaces old horizontal ::after underline) */
.sidebar-nav a.active::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 3px;
  height: 60%;
  background: #3b82f6;
  border-radius: 0 2px 2px 0;
}

.nav-icon {
  flex-shrink: 0;
  opacity: 0.8;
}

.sidebar-nav a.active .nav-icon,
.sidebar-nav a:hover .nav-icon {
  opacity: 1;
}

/* ── Sidebar footer ── */
.sidebar-footer {
  flex-shrink: 0;
  padding: 0.75rem;
  border-top: 1px solid rgba(255, 255, 255, 0.08);
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

/* Expand child components to fill sidebar width */
.sidebar-footer .language-switcher,
.sidebar-footer .profile-menu {
  width: 100%;
}

/* Override scoped button styles to fit dark sidebar background */
.sidebar-footer .language-button,
.sidebar-footer .profile-button {
  width: 100%;
  background: rgba(255, 255, 255, 0.07) !important;
  border-color: rgba(255, 255, 255, 0.12) !important;
  color: rgba(255, 255, 255, 0.85) !important;
}

.sidebar-footer .language-button:hover,
.sidebar-footer .profile-button:hover {
  background: rgba(255, 255, 255, 0.12) !important;
  border-color: rgba(255, 255, 255, 0.2) !important;
}

.sidebar-footer .profile-name,
.sidebar-footer .language-label {
  color: rgba(255, 255, 255, 0.85) !important;
}

.sidebar-footer .chevron,
.sidebar-footer .globe-icon {
  color: rgba(255, 255, 255, 0.5) !important;
}

/* Flip dropdowns upward so they don't clip below the viewport */
.sidebar-footer .profile-menu .dropdown-menu,
.sidebar-footer .language-switcher .dropdown-menu {
  top: auto !important;
  bottom: calc(100% + 0.5rem) !important;
}

/* ── Main content area ── */
.main-wrapper {
  margin-left: 240px;
  flex: 1;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.top-bar {
  position: sticky;
  top: 0;
  z-index: 90;
  background: #f8fafc;
}

.main-content {
  flex: 1;
  padding: 1.5rem 2rem;
}
```

## FilterBar.vue — One Line Change

In `client/src/components/FilterBar.vue`, find the `.filters-bar` scoped rule and change:

```css
/* before */
top: 70px;

/* after */
top: 0;
```

The original offset compensated for the fixed top-nav header height. In the new layout, FilterBar lives inside `.top-bar` (which is itself sticky at `top: 0`), so FilterBar's own offset must be `0`.

## Icon Design Reference

Each route uses a purpose-fit inline SVG (`stroke="currentColor"`, `18×18`, `stroke-width="1.5"`). Using `currentColor` means icons automatically inherit the link's color on hover and active states — no additional CSS needed.

| Route | Icon shape |
|---|---|
| `/` Dashboard | 2×2 grid of small squares |
| `/inventory` | Rectangle with horizontal ruled lines |
| `/orders` | Three lines of decreasing width |
| `/spending` | Circle with dollar sign stroke |
| `/demand` | Upward-trending polyline |
| `/reports` | Document rectangle with ruled lines |

## Active Link Pattern

The old layout used `::after` to draw a 2px underline along the bottom edge of a horizontal link. That pattern has no meaning in a vertical nav. The replacement is a `::before` left-edge stripe:

- `width: 3px`, `height: 60%` — a pill that fills most of the link height
- `left: 0`, vertically centered with `top: 50%; transform: translateY(-50%)`
- `border-radius: 0 2px 2px 0` — flush on left, rounded on right
- Color `#3b82f6` (blue-500) on a `rgba(37, 99, 235, 0.35)` tinted background

This is the standard SaaS sidebar active indicator (used by Notion, Linear, Vercel).

## Dropdown Specificity Notes

Both `ProfileMenu` and `LanguageSwitcher` use `<style scoped>`, so their `.dropdown-menu` rules are compiled as `.dropdown-menu[data-v-xxx]` (specificity 0,2,0). App.vue uses unscoped `<style>`, so `.sidebar-footer .profile-menu .dropdown-menu` has specificity (0,3,0) and wins without `!important`. For the button color overrides, `!important` is used as a safety net since the scoped attribute selector and the parent selector have equal specificity and cascade order is build-tool-dependent.

Do NOT add `overflow: hidden` to `.sidebar`. Without it, the absolute-positioned dropdowns can render outside the sidebar's box, which is required.

## Verification Checklist

After applying the redesign, check each item:

1. **All 6 routes render** — click each sidebar link, confirm the view loads and the active stripe updates
2. **Active state is exclusive** — only one link shows the stripe at a time; the Dashboard link (`/`) does not stay active when on `/inventory` (the explicit `$route.path === '/'` check prevents this)
3. **FilterBar is sticky below the viewport top** — scroll a long page; the filter bar sticks to the top of the main area, not the top of the browser window
4. **ProfileMenu dropdown opens upward** — click the profile button in the sidebar footer; confirm the dropdown opens above, not below (which would clip behind the viewport bottom)
5. **LanguageSwitcher dropdown opens upward** — same test; confirm locale label changes after switching
6. **Modals overlay the full viewport** — open ProfileDetailsModal and TasksModal; confirm they cover the sidebar as well (they teleport to `<body>`, so no layout changes needed)
7. **No horizontal scrollbar** — the sidebar (240px fixed) + main-wrapper (margin-left: 240px) should fill exactly 100vw
