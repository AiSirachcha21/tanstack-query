---
id: overview
title: Overview
---

TanStack Query is often described as the missing data-fetching library for web applications, but in more technical terms, it makes fetching, caching, synchronizing and updating server state in your web applications a breeze.

TanStack Query is designed to be framework-agnostic, working seamlessly in any JavaScript application that runs in the browser - no matter the framework.

This guide is focused on the usage of the Tanstack Query Core libraries that are the foundation of the Tanstack Query wrappers used by [React](https://tanstack.com/query/latest/docs/framework/react/overview), [Vue](https://tanstack.com/query/latest/docs/framework/vue/overview), [Svelte](https://tanstack.com/query/latest/docs/framework/svelte/overview), [Angular](https://tanstack.com/query/latest/docs/framework/angular/overview), and [Solid](https://tanstack.com/query/latest/docs/framework/solid/overview).


## Motivation

Most core web frameworks **do not** come with an opinionated way of fetching or updating data in a holistic way. Because of this developers end up building either meta-frameworks which encapsulate strict opinions about data-fetching, or they invent their own ways of fetching data. This usually means cobbling together component-based state and side-effects, or using more general purpose state management libraries to store and provide asynchronous data throughout their apps.

While most traditional state management libraries are great for working with client state, they are **not so great at working with async or server state**. This is because **server state is totally different**. For starters, server state:

- Is persisted remotely in a location you may not control or own
- Requires asynchronous APIs for fetching and updating
- Implies shared ownership and can be changed by other people without your knowledge
- Can potentially become "out of date" in your applications if you're not careful

Once you grasp the nature of server state in your application, **even more challenges will arise** as you go, for example:

- Caching... (possibly the hardest thing to do in programming)
- Deduping multiple requests for the same data into a single request
- Updating "out of date" data in the background
- Knowing when data is "out of date"
- Reflecting updates to data as quickly as possible
- Performance optimizations like pagination and lazy loading data
- Managing memory and garbage collection of server state
- Memoizing query results with structural sharing

If you're not overwhelmed by that list, then that must mean that you've probably solved all of your server state problems already and deserve an award. However, if you are like a vast majority of people, you either have yet to tackle all or most of these challenges and we're only scratching the surface!

TanStack Query is hands down one of the _best_ libraries for managing server state. It works amazingly well **out-of-the-box, with zero-config, and can be customized** to your liking as your application grows.

TanStack Query allows you to defeat and overcome the tricky challenges and hurdles of _server state_ and control your app data before it starts to control you.

On a more technical note, TanStack Query will likely:

- Help you remove **many** lines of complicated and misunderstood code from your application and replace with just a handful of lines of React Query logic.
- Make your application more maintainable and easier to build new features without worrying about wiring up new server state data sources
- Have a direct impact on your end-users by making your application feel faster and more responsive than ever before.
- Potentially help you save on bandwidth and increase memory performance

[//]: # 'Example'

## Enough talk, show me some code already!

Here's a simple example showing how to use TanStack Query Core in a vanilla JavaScript application:


```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  </head>
  <body>
    <div id="stats"></div>
    <script type="module" src="/src/main.js"></script>
  </body>
</html>

```

```js
import {
  QueryClient,
  QueryObserver,
  MutationObserver,
} from "https://esm.sh/@tanstack/query-core";

// Initialize QueryClient - manages all queries and mutations
const queryClient = new QueryClient();

const stats = document.getElementById("stats");

// Create a query observer to watch for changes to the "repoData" query
// QueryObserver manages the lifecycle of the query (loading, success, error)
const repoQueryObserver = new QueryObserver(queryClient, {
  // Unique key to identify this query
  queryKey: ["repoData"],
  // The actual query function that fetches todos
  queryFn: () =>
    fetch("https://api.github.com/repos/TanStack/query").then((res) => res.json()),
});

let unsubscribeFromRepoObserver;

// Subscribe to query state changes to update UI accordingly
unsubscribeFromRepoObserver = repoQueryObserver.subscribe((result) => {
  if (result.isPending) {
    stats.innerHTML = "<p>Loading...</p>";
  } else if (result.isError) {
    stats.innerHTML = "<p>Error fetching data</p>";
  } else {
    stats.innerHTML = `
        <h1>${result.data.full_name}</h1>
        <p>${result.data.description}</p>
        <strong>👀 ${result.data.subscribers_count}</strong>
        <strong>✨ ${result.data.stargazers_count}</strong>
        <strong>🍴 ${result.data.forks_count}</strong>
    `;
  }
});

// Clean up observers when the page is about to unload
document.addEventListener("beforeunload", () => {
  // Unsubscribe from the repo query observer
  unsubscribeFromRepoObserver();
});
```