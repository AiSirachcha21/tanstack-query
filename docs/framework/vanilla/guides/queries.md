---
id: queries
title: Queries
---

## Query Basics

A query is a declarative dependency on an asynchronous source of data that is tied to a **unique key**. A query can be used with any Promise based method (including GET and POST methods) to fetch data from a server. If your method modifies data on the server, we recommend using [Mutations](./mutations.md) instead.

To subscribe to a query in your components or custom hooks, call the `queryClient.fetchQuery` method with at least:

- A **unique key for the query**
- A function that returns a promise that:
  - Resolves the data, or
  - Throws an error

[//]: # 'Example'

```tsx
import { QueryClient } from '@tanstack/query-core'

function App() {
  const queryClient = new QueryClient()
  const info = queryClient.fetchQuery({ queryKey: ['todos'], queryFn: fetchTodoList })
}
```

[//]: # 'Example'

The **unique key** you provide is used internally for refetching, caching, and sharing your queries throughout your application.

The query result returned by `queryClient.fetchQuery` contains all of the information about the query that you'll need for templating and any other usage of the data:

[//]: # 'Example2'

```tsx
const result = queryClient.fetchQuery({ queryKey: ['todos'], queryFn: fetchTodoList })
```

[//]: # 'Example2'

The `result` object contains a few very important states you'll need to be aware of to be productive. A query can only be in one of the following states at any given moment:

- `isPending` or `status === 'pending'` - The query has no data yet
- `isError` or `status === 'error'` - The query encountered an error
- `isSuccess` or `status === 'success'` - The query was successful and data is available

Beyond those primary states, more information is available depending on the state of the query:

- `error` - If the query is in an `isError` state, the error is available via the `error` property.
- `data` - If the query is in an `isSuccess` state, the data is available via the `data` property.
- `isFetching` - In any state, if the query is fetching at any time (including background refetching) `isFetching` will be `true`.

For **most** queries, it's usually sufficient to check for the `isPending` state, then the `isError` state, then finally, assume that the data is available and render the successful state:

[//]: # 'Example3'

```js
function getTodos() {
  const queryClient = new QueryClient()
  const app = document.getElementById('app')
  
  const todoQuery = queryClient.fetchQuery({
    queryKey: ['todos'],
    queryFn: fetchTodoList,
  })

  // Using QueryObserver to track the query state
  const observer = new QueryObserver(queryClient, {
    queryKey: ['todos'],
    queryFn: fetchTodoList,
  })
  
  const unsubscribe = observer.subscribe((result) => {
    if (result.isPending) {
      // Handle loading state
      renderLoading()
    } else if (result.isError) {
      // Handle error state
      renderError(result.error)
    } else {
      // Handle success state
      renderData(result.data)
    }
  })
  
  // Don't forget to unsubscribe when done
  // unsubscribe()
  
  function renderLoading() {
    app.innerHTML = '<span>Loading...</span>'
  }
  
  function renderError(error) {
    app.innerHTML = `<span>Error: ${error.message}</span>`
  }

  function renderData(data) {
    app.innerHTML = `<ul>${data.map((todo) => `<li key=${todo.id}>${todo.title}</li>`).join('')}</ul>`
  }
}
```

[//]: # 'Example3'

If booleans aren't your thing, you can always use the `status` state as well:

[//]: # 'Example4'

```js
function getTodos() {
  const queryClient = new QueryClient()
  const app = document.getElementById('app')

  const { status, data, error } = queryClient.fetchQuery({
    queryKey: ['todos'],
    queryFn: fetchTodoList,
  })

  const observer = new QueryObserver(queryClient, {
    queryKey: ['todos'],
    queryFn: fetchTodoList,
  })
  
  const unsubscribe = observer.subscribe((result) => {
    if (result.status === 'pending') {
      app.innerHTML = '<span>Loading...</span>'
    } else if (result.status === 'error') {
      app.innerHTML = `<span>Error: ${result.error.message}</span>`
    } else {
      app.innerHTML = `<ul>${result.data.map((todo) => `<li key=${todo.id}>${todo.title}</li>`).join('')}</ul>`
    }
  })
  
  // Don't forget to unsubscribe when done
  // unsubscribe()
}
```

[//]: # 'Example4'

TypeScript will also narrow the type of `data` correctly if you've checked for `pending` and `error` before accessing it.

### FetchStatus

In addition to the `status` field, you will also get an additional `fetchStatus` property with the following options:

- `fetchStatus === 'fetching'` - The query is currently fetching.
- `fetchStatus === 'paused'` - The query wanted to fetch, but it is paused. Read more about this in the [Network Mode](./network-mode.md) guide.
- `fetchStatus === 'idle'` - The query is not doing anything at the moment.

### Why two different states?

Background refetches and stale-while-revalidate logic make all combinations for `status` and `fetchStatus` possible. For example:

- a query in `success` status will usually be in `idle` fetchStatus, but it could also be in `fetching` if a background refetch is happening.
- a query that mounts and has no data will usually be in `pending` status and `fetching` fetchStatus, but it could also be `paused` if there is no network connection.

So keep in mind that a query can be in `pending` state without actually fetching data. As a rule of thumb:

- The `status` gives information about the `data`: Do we have any or not?
- The `fetchStatus` gives information about the `queryFn`: Is it running or not?

[//]: # 'Materials'

## Further Reading

For an alternative way of performing status checks, have a look at the [Community Resources](../community/tkdodos-blog.md#4-status-checks-in-react-query).

[//]: # 'Materials'
