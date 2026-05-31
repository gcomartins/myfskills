# gcomartins · Claude Code skills marketplace

A small, opinionated marketplace of **frontend craft** skills for [Claude Code](https://code.claude.com).
One plugin, six composable skills: a design-direction method, four
reference-backed style lenses, and React animation best practices.

## Install

```bash
# in Claude Code
/plugin marketplace add gcomartins/claude-skills-marketplace
/plugin install awesome-frontend@gcomartins
```

Then update any time with `/plugin marketplace update` and `/plugin update`.

## What's inside — `awesome-frontend`

A **core + lenses** design system plus animation. The lenses apply *on top of*
the core method; the core hands motion off to the animation skill.

| Skill | What it does |
|---|---|
| **awesome-frontend-design** | The style-agnostic **method**: concept → type → color → composition → motion direction. Triggers on "make it look designed / premium / not generic". Includes a lens index. |
| **editorial-web-design** | Loud magazine/lookbook lens — type in tension, numbered indices, masthead credits, vast negative space, organic choreographed motion. Teardown: heyhoncho.com. |
| **swiss-minimal-design** | International Typographic Style — strict grid, one neutral grotesque, primary accents, zero ornament. Teardown: swissted.com. |
| **brutalist-web-design** | Neo-brutalism — thick black borders, flat offset shadows, loud flat color, "press" interactions. Teardown: gumroad.com. |
| **luxury-web-design** | Quiet luxury — warm restrained palette, refined type set small, vast calm space, slow soft motion. Teardown: aesop.com. |
| **awesome-react-animations** | 60fps React animation best practices — keep React off the per-frame path, animate on the compositor (framer-motion / `m`, LazyMotion, View Transitions, scroll reveals). |

Each style lens is anchored in a real site teardown (measured design tokens +
bundled reference screenshots), so the guidance is grounded, not generic.

## How they compose

- No style named ("just make it look good") → **awesome-frontend-design** (method).
- A named aesthetic ("editorial", "swiss", "brutalist", "luxury/premium") → the
  matching **lens**, which pulls the core method in with it.
- Any motion to implement → **awesome-react-animations** for smooth 60fps output.

## Related (not bundled)

For React code-health scanning, install [React Doctor](https://www.react.doctor)
directly: `npx react-doctor install`.

## License

MIT © Guilherme Comartins
