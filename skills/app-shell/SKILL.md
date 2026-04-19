---
name: bridgable-app-shell
description: Drop-in OpenBridge application shell — topbar with hamburger toggle, navigation rail with flyout, brilliance (day/dusk/night/bright) sidebar, alert button, settings page. Load when the user asks for full bridge application chrome around their content. Skip for one-screen demos or single-instrument widgets.
---

# Bridgable App Shell

Use this skill when the user wants a real OpenBridge **application** — not
just a single screen or instrument. The shell is the standard Bridge layout:
fixed top bar with hamburger + page name + alerts + dimming + clock,
left-side navigation rail that flies out when expanded, optional brilliance
menu sidebar for palette/brightness, and a content area where the user's
actual screens live.

## When to load

Trigger on requests like:

- "Build me a navigation app with a sidebar."
- "I need a full bridge application with a topbar and burger menu."
- "Add a brilliance / dimming sidebar."
- "Wrap this dashboard in the OpenBridge app shell."
- "Set up a multi-page app."

## When NOT to load

- One-screen demos (e.g. a standalone conning display, a single radar widget,
  a sandbox to try out one component).
- The user explicitly says "just the screen" / "no chrome" / "no shell".
- The project already has its own non-OpenBridge app shell.

If unsure, ask one question: "Do you want the standard Bridge app chrome
(topbar, hamburger menu, brilliance sidebar) around it, or just the screen?"

This question is **especially important** when the user asks for a
"control panel", "dashboard", "settings screen", or anything with multiple
distinct sections — those phrasings sound like single screens but in
practice almost always live inside the standard bridge layout. Inventing a
card grid when the user expected a top-bar + nav-rail + workspace is the
most common failure mode the plugin sees. One question kills it.

## How to use

1. Confirm the user's framework (`lit`, `react`, `vue`, `svelte`, `angular`).
   If they haven't picked, ask before scaffolding.
2. If the project hasn't been initialized with OpenBridge yet, call
   `bridgable_setup(framework)` first.
3. Call `bridgable_app_pattern(pattern="shell", framework=<framework>)`. It
   returns a complete, copy-pasteable scaffold with imports, state, layout,
   theme persistence (localStorage), settings page, and a clearly marked
   `// build your page content here` slot.
4. Drop the scaffold into the project's root page / app component.
5. Build the user's actual content **inside the marked slot** — never inside
   the topbar / nav region unless the user explicitly asks to extend the
   chrome.
6. Compose with the **bridgable-theming** skill when writing any custom CSS
   inside the content area, so the user's screens follow the palette switch.

## Scope

The `shell` pattern is intentionally one-size-fits-most: a Regular template
(48px topbar + icon-only-large rail). For other layouts (Tall, Double, Split,
Inactive, Small) ask the user which one they want, then use `bridgable_query`
to look up the exact `ObcTopBar` / `ObcNavigationMenu` props for that variant
— additional pattern files may be added under
`bridgable_app_pattern(pattern=...)` over time.

## Compose with the other skills

- `bridgable_setup` for the install + global CSS prerequisite.
- `bridgable_app_pattern` for this scaffold.
- `bridgable_query` for the components that go *inside* the content slot.
- `bridgable-theming` for any custom CSS the user writes alongside.

Never hand-author the topbar / nav / brilliance from memory — call the tool.
