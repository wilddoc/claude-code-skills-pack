---
name: dockerfile-review
description: Review a Dockerfile for security issues, cache-busting layer order, and avoidable image bloat. Use when the user asks to review a Dockerfile, shrink an image, speed up docker builds, or harden a container.
---

1. Read the Dockerfile alongside `.dockerignore` and any compose file — build context and runtime config change what matters.

2. **Security:**
   - Secrets baked into layers. A secret passed via `ARG`, copied in and later deleted, or present in any intermediate layer remains extractable from the image history. `COPY . .` with a `.env` present and no `.dockerignore` entry is the common version of this. Flag it as critical.
   - Running as root when the process doesn't need it — check for a `USER` directive.
   - Unpinned base images (`:latest`, or a floating major tag) making builds non-reproducible and silently shifting the security baseline.
   - `curl … | sh` installs from URLs, which are unverifiable and mutable.

3. **Layer order and caching.** Docker invalidates every layer after the first changed one, so order matters more than most other optimizations:
   - Dependency manifests should be copied and installed *before* the application source. `COPY . .` followed by `npm install` reinstalls all dependencies on every source edit — usually the single biggest build-time win available.
   - Frequently-changing instructions should sit as late as possible.

4. **Size:**
   - No multi-stage build where build tooling (compilers, dev dependencies, test fixtures) ends up in the runtime image.
   - Package manager caches not cleaned in the same `RUN` layer that created them — cleaning in a later layer doesn't shrink the image.
   - Separate `RUN` commands that could be combined where the intermediate state has no value.
   - An overly heavy base image where a slim variant would work.

5. Don't reflexively recommend Alpine — glibc-dependent binaries and some runtimes break or perform worse on musl. Suggest it only when the workload is known to be compatible, and say why.

6. Output grouped by **security** → **caching/build time** → **image size**, each with the line, the concrete consequence, and the corrected instruction. Show the reordered Dockerfile if the layer order is the main finding.
