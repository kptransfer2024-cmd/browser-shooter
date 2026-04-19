# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a browser-based game project — pure HTML5/CSS/JavaScript, no build tools, no dependencies, no package manager. Every game is a single self-contained `.html` file that opens directly in any browser.

## Running the Games

Open any `.html` file directly in a browser. No server, no build step required.

## Repository & Git Workflow

- **Remote:** `https://github.com/kptransfer2024-cmd/browser-shooter`
- **Branch:** `main`
- **Auth:** Token is embedded in the git remote URL — `git push` works without prompting.
- **Commit policy:** Commit and push after every meaningful change. Use clean, descriptive commit messages.

## Architecture

All games follow the same single-file pattern:

```
<style>     — Layout and canvas/body styling; cursor:none when canvas draws its own crosshair
<canvas>    — Single full-window rendering surface
<script>
  CONSTANTS + UTILS     — Canvas setup, math helpers (clamp, circleOverlap without sqrt)
  INPUT                 — Keyboard state map + mouse {x,y,down} + one-frame clicks[] cleared each frame
  PARTICLES             — Plain {} objects with a type string; factory functions per effect type
  BULLETS               — Array (no pooling); OOB auto-removes
  PLAYER                — Delta-time movement, angle = atan2(mouse - pos), invincibility timer
  ENEMIES               — Config-driven base class; type string switches draw() behavior
  LEVEL CONFIG          — LEVEL_CONFIGS array; levels beyond defined ones generated procedurally
  WAVE MANAGER          — spawnQueue + spawnTimer; advances wave when enemies.length === 0
  HUD                   — Drawn on canvas after game world; zero-padded score, health bar
  UI SCREENS            — drawMenu / drawLevelComplete / drawGameOver; click detection via clicks[]
  GAME STATE            — State machine: MENU → PLAYING → LEVEL_COMPLETE → GAME_OVER → MENU
  MAIN LOOP             — requestAnimationFrame; dt capped at 50ms to prevent tunneling
```

## Key Conventions

- **Delta-time everywhere** — all movement, timers, and animations multiply by `dt` (seconds), never frame counts.
- **Circle collision only** — `dx*dx + dy*dy < r*r` (no sqrt). Radii are set to 70–80% of visual size.
- **Canvas state** — every `draw()` that calls `ctx.save()` unconditionally calls `ctx.restore()` before returning.
- **No image files** — all sprites are drawn programmatically with canvas shapes (rects, arcs, lines).
- **Particles as plain objects** — `{}` literals with a `type` field, not class instances.
- **Section banners** — logical sections separated by `// === SECTION NAME ===` comments for easy navigation.
