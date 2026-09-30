---
"@opennextjs/cloudflare": patch
---

fix: resolve an installed `@opentelemetry/api` to its traced CommonJS build in the Node.js middleware

When an app installed `@opentelemetry/api` itself, bundling the Node.js middleware (`proxy.ts`) failed
with `Could not resolve "@opentelemetry/api"` from `next/dist/server/lib/trace/tracer.js`. The Next.js
trace only records the CommonJS build of the package, so that is all `copyTracedFiles` copies, but the
middleware bundle prefers the `module` entry, which points to an ESM build that was never copied.

The package is now resolved the way `require()` resolves it, landing on the traced copy. Apps that do
not install `@opentelemetry/api` keep using the copy compiled into Next.js.
