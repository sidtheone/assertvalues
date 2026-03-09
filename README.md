# The AssertValues Manifesto

## We Assert

**1. Values before specifications.**
A system built on "we never surprise the user" produces better code than one built on "validate all inputs on fields 1–47." Specs describe the *what*. Values describe the *why*. The why survives contact with reality. The what doesn't. This isn't either/or — specs still matter when precision matters. But values come first, because they shape which specs you need.

**2. Philosophy is an input, not a decoration.**
Values aren't something you frame on the wall after the code ships. They are the primary artifact you hand to the AI. They come first. They are the source code.

**3. Propagation over enforcement.**
Rules are brittle. A value, properly declared, shapes decisions no linter can reach — architecture, error handling, naming, defaults. You don't enforce "security-first." You assert it, and it flows into every layer without a checklist.

**4. Humans declare, machines derive.**
The human's job is to know what matters. The machine's job is to turn that into working software. This is the correct division of labor for the age we're entering.

---

## What This Replaces

The current loop: you prompt, you get code, you eyeball it, you prompt again. The feedback is structural — *move this here, rename that, add error handling.* You're iterating on *what the code should do* when you should be declaring *what the code should care about*. AssertValues breaks that loop. Be clear about what matters, and the implementation follows.

There's a deeper problem this solves: **framework drift**. When you build fast with AI, the AI doesn't just write what you asked for — it writes what it thinks you'll need next. Service layers, adapter patterns, event buses, generic components. Each one is defensible in isolation. Together, they turn a weekend project into an enterprise scaffold that nobody asked for. Without values, there's no force pushing back. The complexity ratchet only turns one way. Values are the counter-force — they give the AI a reason to say *no* to its own instincts.

---

## How Values Propagate Into Code

A spec is pushing a single brick — it moves that brick and nothing else. A value is a lever at the top of the wall — one pull rearranges everything below it.

**You declare a value** → the AI treats it as a constraint that applies everywhere, not a rule that applies once. When you assert "we value transparency," it chooses inspectable data structures. It writes errors that explain *why*. It avoids abstractions that obscure behavior. One value, dozens of coherent decisions.

> A spec is a point. A value is a vector — it has direction, and it applies force across the entire codebase.

---

## Writing Values That Propagate

Not all values are equal. A platitude biases nothing. A sharp value kills ten bad ideas before they start.

| Platitude | Sharp Value |
|:---|:---|
| "We value quality" | "We never ship what we haven't tested" |
| "Keep it simple" | "Three similar lines beat a premature abstraction" |
| "Be user-friendly" | "Never surprise the user" |
| "We care about performance" | "Every page loads in under 2 seconds on 3G" |
| "Security matters" | "Assume every input is hostile" |

**What makes a value propagate:**

- **It constrains.** If it doesn't make you say no to something, it's not a value — it's a wish.
- **It's specific enough to decide with.** "Never surprise the user" tells you what to do at a fork in the road. "Be user-friendly" doesn't.
- **It's general enough to apply everywhere.** If it only matters in one file, it's a spec, not a value.
- **Few beat many.** Three sharp values outperform fifteen that conflict. If your values need a priority matrix, you have too many.

Writing sharp values is a skill. It requires knowing what trade-offs exist before you can choose a side. If you're not sure where to start, there's a [Claude Code skill](https://github.com/sidtheone/assertvalues-skill) that walks you through it — product values first, then engineering, UX, DevOps, whatever layers your project needs.

---

## A Worked Example

You're building a marketplace. Sellers list products, buyers browse and purchase, reviews, payments, search, admin dashboard. Give a capable AI this prompt:

```
Build a marketplace app. Sellers list products, buyers browse
and purchase, with reviews. Next.js, TypeScript, Stripe,
PostgreSQL with Drizzle ORM, Tailwind. Include auth, search,
and an admin dashboard.
```

A good AI will scaffold the full picture: `ProductService`, `OrderService`, `ReviewSystem` with moderation queue. A generic `DataTable` with sorting, filtering, and pagination abstracted upfront. An event bus for order-placed → send-email → update-inventory. Repository patterns wrapping Drizzle behind another layer. Auth middleware chains. A component library with 30 primitives. **60+ files** before a single user flow works end-to-end. The skeleton looks impressive. The gap between scaffolded and shipped is where the real work lives.

Now the same prompt, with five values loaded:

```
VALUES.md

- "The best code is no code at all." — Jeff Atwood
  Every line you write is a line you must maintain, debug,
  and explain. Delete what you can.
- "Make each program do one thing well." — Doug McIlroy
  Small, focused tools composed together are more powerful
  than monoliths.
- "Duplication is far cheaper than the wrong abstraction."
  — Sandi Metz
  Tolerate repetition until the pattern is clear.
- "Perfect is the enemy of good." — Voltaire
  Ship the working version. Improve it with real feedback.
- "Never surprise the user."
  Every interaction should behave exactly as the user expects.
```

**~15 files.** Deployable in one session. Stripe Checkout redirect instead of a custom payment flow — because "never surprise the user" means using the flow buyers already trust. SQL `ILIKE` for search instead of Elasticsearch — because Voltaire says ship it, upgrade when someone complains. Reviews inline on the product page, not a `ReviewAggregator` with sentiment analysis — because Atwood says every file must earn its existence. No repository layer wrapping Drizzle — because Metz says don't abstract until the pattern is clear. No generic `DataTable` — duplicate the table markup in admin and product list until a real shared shape emerges.

Each value kills specific decisions:

| Value | What it kills |
|:---|:---|
| "Ship the working version" | Analytics dashboard, seller insights |
| "Every line is a line you must maintain" | Event bus, email system |
| "Tolerate repetition" | Generic `DataTable` component |
| "Never surprise the user" | Custom checkout flow |
| "Do one thing well" | Middleware chains, plugin architecture |

60 files vs. 15. Both handle the same prompt. One scaffolds for a future that may never arrive. The other ships for the present and evolves with real feedback.

---

## Where This Breaks

Levers trade precision for reach. AssertValues is not universal. It breaks in specific, predictable ways:

**Regulatory compliance.** You can assert "patient safety" but the FDA needs field-level audit trails, specific error codes, and exact data retention windows. When a regulator is the audience — not an AI — specs aren't optional. They're the deliverable.

**Protocol and integration work.** Implementing OAuth2, matching an API contract, parsing a wire format. The spec *is* the product. The bytes either match or they don't. There's nothing to philosophize about.

**Performance-critical systems.** "We value responsiveness" won't produce a specific cache invalidation strategy or memory allocation pattern. When the problem is microseconds, you need specs, not values.

**Debugging.** "The value propagated incorrectly" is harder to trace than "line 47 doesn't match the spec." Values make the causal chain longer and more opaque. This is a real cost, not a solvable problem.

**Value conflicts at scale.** With three values, it's elegant. With fifteen across a large team, conflicts multiply. Kindness and transparency collide — the kind thing is to hide a scary error, the transparent thing is to show it. Resolving those collisions is the human's real job in this model, and it's hard.

AssertValues is sharpest in small teams with high autonomy — solo developers, early-stage products, weekend projects that need to ship. The method may have a natural ceiling. Finding that ceiling is part of why we need engineers to test it.

---

## Try It

AssertValues needs proof, not persuasion. Build something real this way — declare values first, and see how they shape what the AI produces.

If AI-assisted development feels powerful but aimless, this is the missing piece: *aim*.

**Assert your values. Let the code flow.**

---

If this resonates, [star the repo on GitHub](https://github.com/sidtheone/assertvalues) and try it on your next build. There's also an [AssertValues skill for Claude Code](https://github.com/sidtheone/assertvalues-skill) that helps you write sharp values step by step.

*Built by humans and AI, together.*
