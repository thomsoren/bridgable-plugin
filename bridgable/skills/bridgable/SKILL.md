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

### `bridgable_setup`

Return install and first-render instructions for one framework.

Use it when the user asks how to start using OpenBridge in:

- `lit`
- `react`
- `vue`
- `svelte`
- `angular`

Do not invent setup steps when this tool can provide them directly.

### `bridgable_whoami`

Return the signed-in Bridgable account and current usage limits.

Use it when:

- authentication looks wrong
- the user asks which account is active
- calls start failing because of quota or access confusion

## Working style

- Prefer Bridgable tool output over memory for OpenBridge-specific details.
- If the user asks for both setup and component usage, call `bridgable_setup`
  for the framework and `bridgable_query` for the component details.
- If search comes back broad, refine with a more exact component name or add
  the framework filter.

## Example prompts

- "Find the OpenBridge component for a given UI pattern."
- "How do I use `obc-slider` in React and handle value changes?"
- "Set up OpenBridge in Vue."
