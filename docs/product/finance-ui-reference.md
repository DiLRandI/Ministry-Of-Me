# Finance UI Reference

This document captures the current UI reference for the finance module and links the repository docs to the Figma design artifact created on March 8, 2026.

## Figma File

- `Pulse Ledger Finance UI System`
- URL: <https://www.figma.com/design/yt4HC7iNkeCfnOu0NNpSMc>
- Scope: mobile-first personal finance UI, desktop/tablet adaptations, Material 3 light theme, Material 3 dark theme, component library, responsive layout guidance, and Flutter widget structure

## Reference Coverage

The Figma file includes:

- dashboard
- transactions
- budget
- analytics
- add transaction
- settings

It also includes:

- a component library for transaction cards, budget progress, chart containers, category selection, financial summaries, and adaptive navigation
- layout guidance for phone, tablet, and desktop breakpoints
- light and dark theme tokens
- Flutter widget hierarchy and `ThemeData` direction for implementation

## Responsive Layout Rules

- `0-839px`: mobile layout with bottom navigation, single-column scroll, summary cards, and a floating action button for quick transaction entry
- `840-1199px`: tablet layout with navigation rail and two-panel compositions where charts and detail panels remain visible together
- `1200px+`: desktop layout with sidebar navigation, multi-column dashboard grids, split panels, and always-visible chart surfaces above the fold

## Visual System Summary

- Design system: Material 3
- Visual direction: clean fintech UI with generous spacing, soft rounded corners, and high financial readability
- Primary income accent: emerald
- Primary expense accent: coral
- Neutral surfaces: low-noise light gray in light mode and deep slate in dark mode
- Typography: large currency figures, compact supporting labels, and strong contrast in both themes

## Flutter Implementation Direction

Recommended top-level structure:

```text
FinanceApp
  AdaptiveScaffold
    AppNavigationShell
      DashboardScreen
      TransactionsScreen
      BudgetScreen
      AnalyticsScreen
      SettingsScreen
    FloatingActionButton
      AddTransactionSheet
```

Key implementation notes:

- keep navigation adaptive by switching between `NavigationBar`, `NavigationRail`, and a desktop sidebar shell
- treat chart surfaces, financial summaries, and budget rows as reusable shared widgets
- use a bottom sheet for transaction entry on mobile and a centered dialog on desktop/tablet
- keep light and dark themes first-class in `ThemeData`, not as an afterthought layered onto one palette

## Documentation Use

- Use this document as the product/UI reference when implementation starts.
- Keep architecture decisions in ADRs and architecture docs; do not move technical decisions into the design file.
- If the Figma file changes materially, update this document and the product scope references in the same change.
