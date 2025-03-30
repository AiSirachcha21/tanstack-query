---
id: installation
title: Installation
---

You can install Tanstack Query via [npm](https://www.npmjs.com/package/@tanstack/query-core) or the usual `<script>` tag via [ESM.sh](https://esm.sh/@tanstack/query-core)

## NPM
```bash
npm install @tanstack/query-core
```

or

```bash
yarn install @tanstack/query-core
```

or

```bash
pnpm install @tanstack/query-core
```

or

```bash
bun install @tanstack/query-core
```

## CDN
You can use the following script tag to install Tanstack Query:
```html
<script type="module">
  import { QueryClient } from 'https://esm.sh/@tanstack/query-core'
</script>
```

## Requirements
Since Tanstack Query is optimized for modern browsers, it is compatible with the following browsers:
```
Chrome >= 91
Firefox >= 90
Edge >= 91
Safari >= 15
iOS >= 15
Opera >= 77
```

>Depending on your environment, you might need to add polyfills. If you want to support older browsers, you need to transpile the library from node_modules yourselves. 
