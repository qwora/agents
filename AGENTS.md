# AGENTS.md

How I build apps with AI agents. This file works with any coding agent (Claude Code, Codex, Cursor, Copilot). Read it before you touch a project, then read that project's `handoff.md` and `PROGRESS.md`.

I'm an indie developer. I ship native iOS apps (SwiftUI) and cross-platform apps (React Native + Expo), plus their landing pages. The agent writes most of the code. I own the product, the taste and the final audit.

---

## 1. The workflow

Each project goes through these stages in order. Don't skip ahead: start a prototype only once the references are locked, and start the build only once I've approved the prototype.

1. **Research.** Look into the idea, the products that already exist, what they charge, what reviewers complain about, and what the store rules say. Write down what we will do differently.
2. **References.** I collect references (apps, sites, screen recordings) and we narrow them to a final set. From that point the references are the design brief. Don't invent a new style.
3. **Break down the vibe.** For video references I share a link and the agent runs `/watch` to split it into frames. From the frames, write down the palette, type, spacing, layout rhythm, motion timing and easing, and how screens transition. These notes are the source for the design tokens.
4. **Prototype.** Build the design with `/design` (Claude Design), then a working clickable prototype served on `localhost` that I can use myself. The prototype should be stateful: real flows, real edge cases, a scenarios panel for jumping to tricky states. I use it to judge the idea and ask for changes before any real code exists.
5. **Handoff.** Write `handoff.md`: product, locked decisions, design tokens, data model, business rules, screens, phases with acceptance checks, store review checklist, placeholders, and what is out of scope. It must be complete enough that a brand-new agent with no chat history can build the app from it.
6. **Phased build.** Native Swift or React Native, one phase at a time (see section 3).
7. **My audit, then production.** I test it myself on a real device. Only after that does it go to the App Store, and I submit it.

---

## 2. Continuity: any agent can pick up the work

I work against usage limits. When one agent runs out, another (or the same one after its reset) continues from the repo, not from chat history.

- **Push every phase to the GitHub remote,** even though the files live on my Mac. The repo is the shared memory between agents.
- `handoff.md` is the spec. `PROGRESS.md` is the log. A new session reads both before writing code.
- After every phase, `PROGRESS.md` records: what was done, how it was tested, deviations from the handoff (and why), what needs me, and the next step.
- **If you are about to hit a limit:** stop at the nearest clean point, write exactly where you stopped in `PROGRESS.md`, commit and push. Never leave half-finished work uncommitted.
- Decisions in `handoff.md` are already settled with me. Don't reopen them. If something isn't covered, choose what fits the product's principles (for example local-first, no server) and record the choice in `PROGRESS.md`.

---

## 3. Phased build with an audit in every phase

Audit each phase as it lands so bugs never stack up across phases.

Every phase ends with these steps:
1. Meet the phase's acceptance checks, including running it in the simulator and looking at it.
2. Audit (below).
3. Update `PROGRESS.md`.
4. Commit `Phase N: <title>` and push.

**Audit in each phase**
- Typecheck, lint, and the unit tests for the area you changed.
- **Use small Python scripts for audits, not a manual read-through.** Grep for placeholders and `TODO`s, check colour contrast across every token pair, find unused files and dead code, check strings for both languages, check spec items against the code. A script costs a few tokens and can be run again; reading every file costs a lot and misses things.
- Remove anything the change made unused. No dead code.

**Test scope.** Per change, run only the tests that touch it. Run the full suite, accessibility audit and release build once per version, before archiving, or when chasing a real bug. Full runs are slow and time matters.

**Merge phases when it saves tokens.** If the remaining phases are small and related, combine them and say so in `PROGRESS.md`.

---

## 4. Design rules

The result must look like *this* product, never like a template.

- **Follow the theme.** Every colour, radius, font weight and duration comes from the project's design tokens, which come from the references. No colours or values made up per screen.
- **No emoji in UI.** Use icons only: one consistent stroke icon set per project, and every icon-only button has an accessibility label.
- **No random gradients.** No purple-to-blue washes, glow blobs or "AI startup" gradient backgrounds. Use a gradient only if a reference uses one, and only where it does.
- **No repeated layout templates.** Not every section is a hero + three cards + CTA. Vary the layout rhythm the way the references do.
- **No generic AI tropes:** left-border accent cards, glassmorphism everywhere, centred everything, stock SaaS copy ("streamline your workflow", "unlock your potential"). The copy is specific to the product and its users.
- **Web:** use [React Bits](https://reactbits.dev) components for motion and interaction where they fit the theme, styled with the project's tokens, never left at their defaults.
- **Graphics and marketing assets:** use the Canva MCP for social posts, banners and store graphics, using the same palette and type.
- **Motion matters.** Match the timing and easing taken from the references. Use real app animations in previews and promos, not HTML imitations. Respect Reduce Motion.
- **Accessibility is part of the design:** text contrast 4.5:1 or better, 44pt minimum touch targets, Dynamic Type that doesn't break layouts.

---

## 5. Product rules

- **Users can edit what they create.** Anything a user names or makes (accounts, categories, clients, trips, labels, document headings) can be renamed and customised later. Every created item has an Edit action.
- **Local-first by default.** Data stays on the device unless the product needs a server. No analytics, ad SDKs, crash reporters, logins or network calls unless I've agreed to them.
- **Don't market what isn't built.** Landing pages and store text describe only v1 features.
- **Ask before adding a dependency** the handoff doesn't list.
- **Leave placeholders as `[PLACEHOLDER]`.** Never make up business data, legal names, emails or prices.

---

## 6. Safety

- **Never submit to the App Store or publish to production yourself.** Prepare everything, including a step-by-step submission guide, and I press the button.
- **Deleting:** print the exact list of paths first (a dry run), check that the keep-list matches, then move the files to the Trash rather than using `rm -rf`. Match by glob or date, not typed names (Xcode archive names contain a hidden narrow no-break space).
- **Keep marketing out of the code repo.** Promo videos, screenshots and banners live in a separate folder. Store listing *text* can live in the repo so other agents can continue it.
- **Never commit secrets.** API keys go in git-ignored local config.
- **Kill any command stuck for more than 15 minutes.** Run simulator tests one at a time, not in parallel.

---

## 7. Stack notes

**iOS (SwiftUI):** SwiftData for storage, StoreKit 2 or RevenueCat for subscriptions, App Intents for Shortcuts and Siri. Verify on the simulator; take App Store screenshots from UI tests with demo data.

**React Native (Expo):** Expo changes every SDK release, so don't trust memory. Read the `expo` version in `package.json` and use the matching docs (`https://docs.expo.dev/llms.txt`). Use `npx expo install` for packages, Expo Router for navigation, and EAS for builds. Never edit generated `ios/` or `android/` folders by hand; use config plugins.

**Web:** static or Next.js, deployed to Vercel, with tokens shared with the app.
