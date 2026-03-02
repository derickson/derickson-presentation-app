# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev       # Start development server (Vite, hot reload)
npm run build     # Production build to dist/
npm run preview   # Preview production build at localhost:4173
```

No lint or test scripts are configured.

## Architecture

This is a React + Vite single-page presentation app for Elastic's sales team. It has no backend — all state is in localStorage and all assets are static.

### Scene System

The core abstraction is a **scene**: a full-screen React component that receives `{ onNext, scenes, allScenes, onNavigate }` props.

`src/App.jsx` is the single source of truth for scenes:
- The `scenes` array defines the master ordered list — reorder it to change the flow
- `hideFromAgenda: true` scenes are hidden from the agenda slide but still in navigation
- `useEnabledScenes()` (from `SceneSettings.jsx`) wraps the scenes array to provide runtime filtering/reordering via the settings panel, persisted to `localStorage` under key `presentation-scene-config`

To add a new scene:
1. Create `src/scenes/MyScene.jsx`
2. Import and add it to the `scenes` array in `App.jsx`

### Theme System

Two themes: `dark` (default, Developer Blue `#101C3F` background) and `light`.

- `ThemeContext.jsx` — provides `{ theme, toggleTheme }`, persists to `localStorage` under key `presentation-theme`
- `useThemeStyles.js` — the primary hook used in scene/component files; returns Tailwind class strings for text, backgrounds, and borders that adapt to theme
- Brand colors live in `tailwind.config.js` under the `elastic.*` namespace

**Always use `useThemeStyles()` for theme-aware styling** rather than hardcoding dark/light variants inline.

### Team Configuration

`TeamContext.jsx` provides team member data, with fallback loading from `public/config/team.json`. Changes are persisted to `localStorage` under `presentation-team-config`. Photos are stored as base64 strings.

### Key localStorage Keys

| Key | Purpose |
|-----|---------|
| `presentation-scene-config` | Scene visibility, order, durations |
| `presentation-team-config` | Team member data |
| `presentation-theme` | Dark/light preference |

### Static Assets

- `public/config/team.json` — default team config (loaded when no localStorage data exists)
- `public/photos/` — team member photos
- `public/*.svg, *.png` — brand logos and assets

### Analytics

Vercel Analytics (`@vercel/analytics`) is integrated in `App.jsx`. It only activates on Vercel deployments with analytics enabled in project settings.
