---
name: bundle-size-audit
description: Find what's bloating a frontend bundle — heavy dependencies, missing code splitting, imports that defeat tree shaking. Use when the user asks why their bundle is large, wants faster load times, or asks to reduce JavaScript size.
---

1. Identify the bundler and read its config plus `package.json`. If the project has a bundle analyzer or stats output available, use real numbers rather than guessing — a dependency's reputation for being heavy is not evidence about this build.

2. **Imports that defeat tree shaking** — usually the cheapest large win:
   - Namespace imports of large libraries (`import * as _ from 'lodash'`) where a few functions are used.
   - Importing from a package root when the package ships deep entry points for individual modules.
   - Barrel files (`index.ts` re-exporting everything) that pull an entire directory in when one symbol is used.
   - A `sideEffects` field missing or wrong in `package.json`, which stops the bundler from dropping unused modules.

3. **Heavy dependencies where a lighter path exists** — a full date library used for one `format` call, a charting library imported wholesale for one chart type, an icon package importing the whole set, polyfills for browsers the project's own browserslist doesn't target. Name the specific alternative and the realistic saving, not "consider a lighter library."

4. **Missing code splitting** — routes all in the initial chunk, heavy components (editors, charts, maps, video players) loaded eagerly when they render behind a click or below the fold, admin-only code shipped to every user.

5. **Assets** — uncompressed or unsized images, fonts loaded in formats/weights that aren't used, source maps shipped to production.

6. **Verify before claiming a saving.** Whether a dependency is actually duplicated, or actually tree-shaken, depends on this project's resolution and config. If you can't confirm statically, say so and name the analyzer command that would settle it, rather than reporting an estimated saving as fact.

7. Don't recommend micro-optimizations that complicate the code for a few kilobytes while a much larger item sits unaddressed. Order findings by estimated bytes saved, and be explicit about which numbers are measured versus inferred.
