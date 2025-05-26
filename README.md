# Reproduction of issue with Notion `SupportedFetch` types

Release [v3.1.0][] of `@notionhq/client` changed the definition of the
`SupportedFetch` type such that a type error is raised when working with a
browser environment rather than a Node environment.

Specifically, `SupportedRequestInit['body']` was [changed][diff] to use the
`Readable` type from the `node:stream` module, and this is incompatible with the
type of `body` in the DOM types library.

[v3.1.0]: https://github.com/makenotion/notion-sdk-js/releases/tag/v3.1.0
[diff]: https://github.com/makenotion/notion-sdk-js/compare/v3.0.1...v3.1.0#diff-a570c00994d666ae078f11f697662d819f448fd464a194410559e690c374a066L20-R21

## Reproduction

This repository contains a minimal example of the issue, creating a `Client`
instance and passing the global `fetch` function for the `fetch` parameter:

```ts
import { Client } from "@notionhq/client";

const client = new Client({ fetch: fetch });
```

The `src/browser/` directory contains a `tsconfig.json` that is configured for a
browser environment, whereas the `src/node/` directory is configured for a Node
environment.

To run the TypeScript compiler for both directories:

    npm ci
    npm run typecheck

## Verify working state

To confirm the issue was introduced in v3.1.0, run the following to see that
v3.0.1 does not raise a type error:

    npm i @notionhq/client@3.0.1
    npm run typecheck
