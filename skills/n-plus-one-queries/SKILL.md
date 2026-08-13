---
name: n-plus-one-queries
description: Find N+1 query patterns and other avoidable database round-trips in ORM code. Use when the user reports a slow endpoint or page, asks about database performance, or asks to review ORM/query code.
---

N+1 queries are the most common cause of an endpoint that's fast in development and slow in production — the pattern is invisible at ten rows and fatal at ten thousand.

1. Identify the ORM/query layer in use, since the fix differs (`select_related`/`prefetch_related` in Django, `includes`/`preload` in ActiveRecord, `joinedload`/`selectinload` in SQLAlchemy, `include` in Prisma, eager-loading config in Hibernate).

2. **Find the pattern:** a query returning a collection, followed by a loop (or a `.map`, a serializer, or a template render) that accesses a related object on each item. The related access issues one query per row. Look especially in:
   - Serializers and API response builders, where a nested field triggers a lazy load per record.
   - Template/view loops accessing `item.author.name`-style chains.
   - Computed properties and model methods that query internally — these hide the round-trip behind attribute access, making the loop look innocent.
   - Authorization checks run per-item inside a loop.

3. **Also flag related waste** once you're reading the query layer: unbounded queries with no `LIMIT` on a table that grows, `SELECT *` where a couple of columns are used and the row is wide, counting by loading a full collection into memory rather than a `COUNT` query, and queries inside a transaction that don't need to be there.

4. **Verify rather than assume.** Whether an access triggers a query depends on the ORM's lazy/eager configuration for that specific relation. Check the model definition before calling something an N+1 — a relation already configured to eager-load isn't one. If you can't determine it statically, say so and name the query-log or profiler check that would confirm it.

5. Don't blanket-recommend eager-loading everything: over-fetching deep relation trees on an endpoint that uses one field trades N+1 for one enormous slow query. Match the fix to what the code actually reads.

6. Output a table: location, the triggering access, estimated queries (`1 + N`), and the specific fix for that ORM. Order by likely row count at the call site — an N+1 over a bounded list of five is not worth the same attention as one over an unbounded table.
