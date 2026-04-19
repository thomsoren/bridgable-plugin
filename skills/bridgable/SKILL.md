---
name: bridgable
description: Search the OpenBridge maritime UI component library and get framework-specific setup guidance. Load when the user asks about OpenBridge components, props, events, slots, methods, CSS parts, or install/first-render steps for Lit, React, Vue, Svelte, or Angular. Exposes bridgable_query, bridgable_setup, and bridgable_whoami.
---

# Bridgable

Use Bridgable when the user is working with OpenBridge components and needs
real component facts instead of guesses.

## When to use it

- The user asks about an OpenBridge component, prop, event, slot, method, CSS
  part, or theme token.
- The user needs install or first-render instructions for Lit, React, Vue,
  Svelte, or Angular.
- The user needs to confirm which Bridgable account or quota the plugin is
  using.

## Tools

### `bridgable_query`

Search OpenBridge component documentation.

Use it first for:

- component discovery
- prop and event lookups
- slot, method, and CSS questions
- wrapper-specific usage questions
- theming and styling questions

Guidance:

- Prefer exact kebab-case component names when known, for example
  `obc-slider`, `obc-top-bar`, or `obc-button`.
- Pass `framework` when the user already named one. That keeps results aligned
  with the wrapper they are actually using.
- Summarize results in plain language and preserve the component names, module
  names, and constraints from the returned snippets.

#### Reading enum-typed props correctly

`bridgable_query` returns the **TypeScript enum member name**, not the string
value the HTML attribute expects. They diverge constantly. Examples seen in
the wild:

| Component | Member name (in docs) | Actual attribute value |
|---|---|---|
| `obc-toggle-button-option` `type` | `iconText` | `text-icon` |
| Many `variant` props | `inCommand` | `in-command` |
| Many `state` props | `loading` / `inCommand` | `loading` / `in-command` |

When a prop's type looks like a TS enum / union (capitalised name, no quotes
around the doc value, or a list of camelCase tokens), do **one** of:

1. Open the component's `dist/<path>/<file>.js` source and read the literal
   string values the enum maps to. The kebab-case form is what the
   attribute takes.
2. Use the framework wrapper (`@oicl/openbridge-webcomponents-react` etc.)
   and pass the value as a typed prop — TS will narrow it to the correct
   string for you.

Never copy a CamelCase enum member straight into an HTML attribute. The
component will silently fall back to its default and the bug looks like
"the prop did nothing".

#### CSS knobs aren't in `bridgable_query` output

Many components expose internal sizing/spacing via `--app-components-*`
CSS custom properties (e.g. floating-message-item is 640px wide via
`--app-components-floating-message-item-component-width`). These are **not**
returned by `bridgable_query` and outer wrappers cannot shrink the component
with normal CSS — set the var directly. See the `bridgable-theming` skill
for the discovery pattern.

### `bridgable_setup`

Return install and first-render instructions for one framework.

Use it when the user asks how to start using OpenBridge in:

- `lit`
- `react`
- `vue`
- `svelte`
- `angular`

Do not invent setup steps when this tool can provide them directly.

### `bridgable_app_pattern`

Return a copy-pasteable application-pattern scaffold for one framework.

Use it when the user wants the full bridge **app shell** around their
content — topbar with hamburger menu, navigation rail, brilliance sidebar,
alert button, settings page. Skip for one-screen demos.

Args: `pattern` (currently `"shell"`), `framework` (`lit`, `react`, `vue`,
`svelte`, `angular`).

The companion `bridgable-app-shell` skill describes when to compose this
tool with `bridgable_setup` and `bridgable_query`. See also
`bridgable-theming` for the color-token rules every custom CSS rule inside
the shell's content slot must follow.

### `bridgable_whoami`

Return the signed-in Bridgable account and current usage limits.

Use it when:

- authentication looks wrong
- the user asks which account is active
- calls start failing because of quota or access confusion

## Authentication

If any Bridgable tool call returns a message or error containing an OAuth
authorization URL (e.g. `https://app.bridgable.ai/api/auth/mcp/authorize?...`
or a `http://127.0.0.1:<port>/callback` redirect URL), open it for the user
immediately — do **not** print the URL and ask the user to paste it, because
terminals wrap long URLs and break the paste.

- macOS: run `open "<url>"` via the Bash tool.
- Linux: run `xdg-open "<url>"`.
- Windows: run `start "" "<url>"`.

Then tell the user: "Browser opened — sign in on bridgable.ai to authorize."
Do not include the URL itself in your reply.

## Working style

- Prefer Bridgable tool output over memory for OpenBridge-specific details.
- If the user asks for both setup and component usage, call `bridgable_setup`
  for the framework and `bridgable_query` for the component details.
- If the user wants a full bridge application (not a single screen), load
  the `bridgable-app-shell` skill and call `bridgable_app_pattern` for the
  scaffold before filling in content.
- Whenever you write hand-rolled CSS that lives next to OpenBridge
  components, load the `bridgable-theming` skill — it lists the color
  tokens to use and the anti-patterns (raw hex, `var(--text-color, #...)`)
  that break theme switching.
- If search comes back broad, refine with a more exact component name or add
  the framework filter.

### One disambiguation question before scaffolding

Before generating a non-trivial UI, ask **one** question if the answer
would change the structure. The cheapest example: any time the user asks
for "a screen with multiple controls / sections / cards", ask:

> Real OpenBridge bridge layout (top bar + nav rail + workspace), or just
> the screen itself?

OpenBridge apps follow conventions — top bar, navigation rail/menu, content
workspace. Inventing a custom card grid because the user said "single screen"
when they actually meant "control panel inside an app" is a common
miss. One clarifying question kills the made-up layout. Skip the question
only when the user has already been explicit ("just the screen, no
chrome", "embed in my existing app").

## Example prompts

- "Find the OpenBridge component for a given UI pattern."
- "How do I use `obc-slider` in React and handle value changes?"
- "Set up OpenBridge in Vue."
