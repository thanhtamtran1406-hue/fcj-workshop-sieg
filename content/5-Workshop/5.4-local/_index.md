---
title: "Step 1 — Run and check locally"
date: 2026-11-26
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

Run the application on your own machine first, so that the code, the tests and the data model are known to work before anything is created on AWS.

```sh
pnpm install --frozen-lockfile
pnpm setup
pnpm dev
pnpm check && pnpm build && pnpm test:smoke
```
`pnpm setup` creates a local `.env.local`; do not commit it.

**Expected result:** `pnpm check` and `pnpm build` finish without errors, the smoke suite passes, and the site opens locally with the menu loaded.
