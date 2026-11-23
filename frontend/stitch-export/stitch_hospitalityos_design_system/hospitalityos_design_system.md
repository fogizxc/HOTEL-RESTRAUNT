---
name: "HospitalityOS — Apple-Inspired Enterprise Design System"
description: "A calm, precision-crafted, multi-tenant hotel and restaurant management design system combining macOS clarity, iOS touch ergonomics, and enterprise information density across light and dark modes."
---

## Overview

HospitalityOS is a cloud-based, multi-tenant Hotel and Restaurant Management platform built for operational calm and high-precision execution. Its visual language synthesizes the editorial poise of Apple's web typography, the structured clarity of modern macOS window chrome, and the immediate touch ergonomics of iOS.

- **Brand Personality:** Calm, authoritative, tactile, and unhurried. Every pixel earns its place; decoration is replaced by proportion, alignment, and subtle tonal separation.
- **Target Audience:** Hotel General Managers, Front Desk Agents, Restaurant Cashiers, Kitchen Line Chefs, Dining Guests (QR Portal), and Platform Super Admins across 12 unified workspaces.
- **Design Style:** **Apple-Inspired Minimalist Enterprise (Corporate / Modern)** — soft neutral canvases, crisp white elevated surfaces in light mode, layered charcoal surfaces in dark mode, restrained semantic accents, and disciplined typography.
- **Do's and Don'ts:**
  - **Do** rely on whitespace, 1px hairline borders, and tonal surface contrast to organize dense operational data.
  - **Do** reserve `#0071E3` (and `#2997FF` in dark mode) strictly for primary actions, active navigation states, and focus rings.
  - **Do** adapt layout density and touch target size by workspace (compact tabular density for Super Admin & Front Desk; large 44px+ touch targets for Restaurant POS, Kitchen Display, and QR Portal).
  - **Don't** use dark navy dashboard templates, heavy drop shadows, thick borders, saturated sidebar backgrounds, or decorative illustrations.
  - **Don't** place icons beside every heading or mix unrelated icon styles; use lightweight `Material Symbols Outlined` consistently.
  - **Don't** rely on color alone for status—always pair status colors with concise labels or icons.

## Colors

The palette is anchored in soft Apple-inspired neutrals—white, cool mist grey, and deep graphite—with a single restrained blue accent (`#0071E3`) and calibrated semantic status tones. Dark mode is authored independently with deep graphite (`#111113`) and layered charcoal surfaces (`#1C1C1E`, `#242426`, `#2C2C2E`) rather than inverting light mode.

- **Background & Surfaces:** Light mode uses `#F5F5F7` as the primary canvas with `#FFFFFF` elevated cards and `#FAFAFC` subtle containers. Dark mode steps from `#111113` canvas to `#1C1C1E` cards and `#242426` / `#2C2C2E` nested controls.
- **Text Hierarchy:** Primary text (`#1D1D1F` light / `#F5F5F7` dark), secondary text (`#6E6E73` light / `#A1A1A6` dark), and muted captions (`#86868B` light / `#8E8E93` dark).
- **Semantic Status:** Restrained green for confirmed/ready states, subtle amber for pending/preparing states, and refined red for urgent/overdue/error states.

```yaml
colors:
  light:
    background: "#F5F5F7"
    on-background: "#1D1D1F"
    surface: "#FFFFFF"
    on-surface: "#1D1D1F"
    surface-container-low: "#FAFAFC"
    surface-container: "#F5F5F7"
    surface-container-high: "#EBEBF0"
    on-surface-variant: "#6E6E73"
    muted: "#68686D"
    primary: "#0071E3"
    on-primary: "#FFFFFF"
    primary-container: "#ECF4FF"
    on-primary-container: "#003B7A"
    outline: "#D2D2D7"
    outline-variant: "#E5E5EA"
    success: "#1E8E3E"
    on-success: "#101010"
    success-container: "#EAF7EE"
    on-success-container: "#0B4619"
    warning: "#9A5B00"
    on-warning: "#FFFFFF"
    warning-container: "#FFF4E0"
    on-warning-container: "#492900"
    error: "#D93025"
    on-error: "#FFFFFF"
    error-container: "#FDECEC"
    on-error-container: "#690002"
  dark:
    background: "#111113"
    on-background: "#F5F5F7"
    surface: "#1C1C1E"
    on-surface: "#F5F5F7"
    surface-container-low: "#161618"
    surface-container: "#242426"
    surface-container-high: "#2C2C2E"
    on-surface-variant: "#A1A1A6"
    muted: "#8E8E93"
    primary: "#2997FF"
    on-primary: "#001B3F"
    primary-container: "#00458F"
    on-primary-container: "#D7E2FF"
    outline: "#38383A"
    outline-variant: "#2C2C2E"
    success: "#30D158"
    on-success: "#002108"
    success-container: "#003912"
    on-success-container: "#C6FFC6"
    warning: "#FFB340"
    on-warning: "#2C1700"
    warning-container: "#492900"
    on-warning-container: "#FFDCBC"
    error: "#FF453A"
    on-error: "#410002"
    error-container: "#690002"
    on-error-container: "#FFDAD5"
```

## Typography

HospitalityOS uses **Inter** across all display, heading, body, and UI label roles as the web-hosted equivalent to SF Pro, paired with **Source Code Pro** (`tabular-nums`) for kitchen timers, folio balances, POS totals, and reference IDs.

- **Weights & Restraint:** Semibold (`600`) and Medium (`500`) establish hierarchy cleanly without heavy `700`/`800` blocks.
- **Overview vs. Operational Scale:** Overview dashboards use `display-lg` (`40px`) and `headline-lg` (`28px`) with generous breathing room; dense operational tables, POS, and Front Desk views rely on `title-md` (`15px`), `body-md` (`14px`), and `body-sm` (`13px`) for crisp scanability.
- **Iconography:** Lightweight `Material Symbols Outlined` (`font-weight: 300–400`, `opsz: 20`) across all workspaces.

```yaml
typography:
  display-lg:
    fontFamily: "Inter"
    fontSize: 40px
    fontWeight: 600
    lineHeight: 46px
    letterSpacing: -0.8px
  display-lg-mobile:
    fontFamily: "Inter"
    fontSize: 30px
    fontWeight: 600
    lineHeight: 36px
    letterSpacing: -0.5px
  headline-lg:
    fontFamily: "Inter"
    fontSize: 28px
    fontWeight: 600
    lineHeight: 34px
    letterSpacing: -0.4px
  headline-md:
    fontFamily: "Inter"
    fontSize: 22px
    fontWeight: 600
    lineHeight: 28px
    letterSpacing: -0.25px
  title-lg:
    fontFamily: "Inter"
    fontSize: 18px
    fontWeight: 600
    lineHeight: 24px
    letterSpacing: -0.15px
  title-md:
    fontFamily: "Inter"
    fontSize: 15px
    fontWeight: 600
    lineHeight: 20px
    letterSpacing: -0.1px
  body-lg:
    fontFamily: "Inter"
    fontSize: 15px
    fontWeight: 400
    lineHeight: 22px
    letterSpacing: 0px
  body-md:
    fontFamily: "Inter"
    fontSize: 14px
    fontWeight: 400
    lineHeight: 20px
    letterSpacing: 0px
  body-sm:
    fontFamily: "Inter"
    fontSize: 13px
    fontWeight: 400
    lineHeight: 18px
    letterSpacing: 0px
  label-lg:
    fontFamily: "Inter"
    fontSize: 14px
    fontWeight: 500
    lineHeight: 20px
    letterSpacing: 0px
  label-md:
    fontFamily: "Inter"
    fontSize: 12px
    fontWeight: 500
    lineHeight: 16px
    letterSpacing: 0.1px
  label-sm:
    fontFamily: "Inter"
    fontSize: 11px
    fontWeight: 500
    lineHeight: 14px
    letterSpacing: 0.2px
  mono-lg:
    fontFamily: "Source Code Pro"
    fontSize: 20px
    fontWeight: 600
    lineHeight: 26px
    letterSpacing: -0.2px
  mono-md:
    fontFamily: "Source Code Pro"
    fontSize: 13px
    fontWeight: 500
    lineHeight: 18px
    letterSpacing: 0px
```

## Layout & Spacing

A disciplined `4px` base grid governs every workspace. Desktop (`1440px` and `1280px`) uses a persistent, collapsible `248px` macOS-style translucent sidebar paired with a fluid 12-column main canvas (`max-width: 1440px`, `24px` gutters).

- **Responsive Breakpoints & Specialised Modes:**
  - `1440px` / `1280px` (Desktop & Laptop): Full 12-column grid with persistent sidebar, multi-pane Front Desk timelines, and 4-up KPI rows.
  - `1024px` (Tablet & POS Terminal): Collapsible drawer sidebar, 8-column grid, 44px+ touch targets for Restaurant POS.
  - `390px` (Mobile & QR Customer Portal): Single-column fluid stack, sticky bottom action bar, thumb-zone category pills.
  - **Kitchen Display (KDS):** Edge-to-edge high-contrast ticket grid (`16px` gutters, zero sidebar chrome, oversized `mono-lg` timers).

```yaml
spacing:
  micro: 4px
  compact: 8px
  component: 12px
  standard: 16px
  section: 24px
  major: 32px
  page: 48px
```

## Elevation & Depth

Hierarchy is communicated primarily through **tonal surface contrast** and **1px hairline dividers** (`outline-variant` / `outline`), supplemented by ultra-diffused ambient shadows only on floating overlays and elevated cards.

- **Cards (Light Mode):** `#FFFFFF` surface on `#F5F5F7` canvas with `1px solid #E5E5EA` hairline border and `0 1px 3px rgba(0, 0, 0, 0.04)` ambient shadow.
- **Cards (Dark Mode):** `#1C1C1E` surface on `#111113` canvas with `1px solid #2C2C2E` hairline border and zero drop shadow.
- **Shell & Overlays:** Sidebars and sticky headers use translucent surfaces (`backdrop-filter: blur(20px)`) with subtle vertical separation. Modals and dropdown menus use `0 16px 40px rgba(0, 0, 0, 0.12)` in light mode and `0 16px 40px rgba(0, 0, 0, 0.48)` in dark mode.

## Shapes

Corners are consistently soft and calibrated to element scale—never sharp `0px` boxes and never overly bubbly across operational tables.

- **Inputs, Buttons & Small Controls:** `10px` (`DEFAULT`) or `6px` (`sm`) for compact table badges and checkboxes.
- **Cards & Containers:** `12px` (`md`) for compact operational cards and `16px` (`lg`) to `18px` (`xl`) for primary dashboard cards and modals.
- **Pills & Avatars:** `9999px` (`full`) for status pills, segmented control thumbs, and user avatars.

```yaml
rounded:
  sm: 6px
  DEFAULT: 10px
  md: 12px
  lg: 16px
  xl: 18px
  full: 9999px
```

## Motion & Micro-interaction

Motion is crisp, tactile, and understated—never bouncy or theatrical.

- **Interactive Feedback:** Buttons, table rows, and sidebar items respond within `120ms` (`state-change`).
- **Overlays & Drawers:** Modals and popovers enter with a gentle `240ms` deceleration curve (`surface-enter`) and exit faster at `160ms` (`surface-exit`).
- **Accessibility:** All transitions respect `@media (prefers-reduced-motion: reduce)` by collapsing duration to `0ms`. Only `transform` and `opacity` are animated for overlays.

```yaml
motion:
  state-change:
    duration: 120ms
    delay: 0ms
    timingFunction: cubic-bezier(0.25, 0.1, 0.25, 1)
  surface-enter:
    duration: 240ms
    delay: 0ms
    timingFunction: cubic-bezier(0.16, 1, 0.3, 1)
  surface-exit:
    duration: 160ms
    delay: 0ms
    timingFunction: cubic-bezier(0.4, 0, 1, 1)
  drawer-slide:
    duration: 280ms
    delay: 0ms
    timingFunction: cubic-bezier(0.16, 1, 0.3, 1)
```

## Voice & Content Strategy

HospitalityOS speaks like a composed, attentive concierge: clear, direct, and free of enterprise jargon.

- **Tone of Voice:** Calm, precise, and helpful. Use *"Check in guest"* rather than *"Execute Check-In Workflow"*.
- **Capitalisation:** Sentence case for all page titles, section headers, buttons, table headers, and menu items. Title Case is reserved solely for proper nouns (property names, guest names, workspace names like *Front Desk* or *Kitchen Display*).
- **Button & Action Labels:** Concise verb + noun phrases (*"Assign room"*, *"Confirm order"*, *"Export folio"*). Never use generic *"OK"* or *"Submit"*.
- **Empty, Loading & Error States:** Empty states state what is missing and offer one primary action (*"No arrivals scheduled for today. Create a reservation to get started."*). Errors state what failed and the next step (*"Unable to process card. Verify terminal connection and retry."*).
- **Numbers, Dates & Units:** Use `tabular-nums` (`Source Code Pro` or `Inter` with `font-variant-numeric: tabular-nums`) for all currency, room numbers, occupancy percentages, and kitchen timers. Format dates as `Nov 23, 2026` and times in 24-hour or local property format (`14:30`).

## Components

Component implementations live as individual HTML sticker sheet files in
`components/`. Each file contains the canonical, styled HTML components with
`<!-- COMPONENT: name -->` comment markers. Read each file before use.

- **Badge** → `@components/badge.html`: `badge-numeric`, `badge-dot`
- **Button** → `@components/button.html`: `button-primary`, `button-icon`, `button-with-icon`
- **Card** → `@components/card.html`: `card-elevated`, `card-action`, `card-interactive`, `card-interactive-2`
- **Input Field** → `@components/input-field.html`: `input-field-text`, `input-field-icon`
- **Modal** → `@components/modal.html`: `modal-standard`, `modal-alert`, `modal-drawer`
- **Table** → `@components/table.html`: `table-standard`, `table-interactive`, `table-selectable`
- **Typography** → `@components/typography.html`: `typography-display`, `typography-body`, `typography-label`
