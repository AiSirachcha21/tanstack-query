---
id: quick-start
title: Quick Start
---

This guide will help you get started with TanStack Query in a vanilla JavaScript project.

## Installation

To start, you can either add the `@tanstack/query-core` as a `<script>` tag to your HTML

```html
<script src="https://esm.sh/@tanstack/query-core"></script>
```

or

Import `QueryClient`, `QueryObserver` and `MutationObserver` from `@tanstack/query-core` via [ESM.sh](https://esm.sh/@tanstack/query-core).

```js
import {
  QueryClient,
  QueryObserver,
  MutationObserver,
} from "https://esm.sh/@tanstack/query-core";
```

> Alternatively, if you have webpack, vite or any other bundler configured, you can import the core libraries directly from your project via npm, yarn or your package manager of choice.

## Basic Usage
For the quick start, we will use the ESM build of `@tanstack/query-core`.

Here's a complete example using a todo app showing how to use TanStack Query Core in a vanilla JavaScript project:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>TanStack Query Core</title>
  </head>
  <body>
    <div id="app">
      <div>
          <h1>Todos</h1>
          <button id="refresh-todo-button" disabled>Refresh</button>
      </div>
      <div id="error-message"></div>
      <div id="todos"></div>
    </div>
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

const todosContainer = document.getElementById("todos");
const errorElement = document.getElementById("error-message");

const refreshButton = document.getElementById("refresh-todo-button");
if (refreshButton) {
  // Tell QueryClient that the 'todos' data is now stale and we need to refretch
  refreshButton.onclick = () => {
    queryClient.invalidateQueries({ queryKey: ["todos"] });
  }
}

// Create a query observer to watch for todos data changes
// QueryObserver manages the lifecycle of the query (loading, success, error)
const todoQueryObserver = new QueryObserver(queryClient, {
  // Unique key to identify this query
  queryKey: ["todos"],
  // The actual query function that fetches todos
  queryFn: () =>
    fetch("http://localhost:3001/custom-todos").then((res) => res.json()),
});

let unsubscribeFromTodoObserver;

// Subscribe to query state changes to update UI accordingly
unsubscribeFromTodoObserver = todoQueryObserver.subscribe((result) => {
  if (result.isLoading) {
    todosContainer.innerHTML = "<p>Loading...</p>";
  } else if (result.isError) {
    errorElement.textContent = result.error.message;
  } else {
    renderTodos(result.data);
  }
});

// Clean up observers when the page is about to unload
document.addEventListener("beforeunload", () => {
  // Unsubscribe from the todo query observer
  if (unsubscribeFromTodoObserver) {
    unsubscribeFromTodoObserver();
  }
});

function createTodo(todo) {
  const todoElement = document.createElement("div");
  todoElement.classList.add("todo");
  todoElement.dataset.id = todo.id;

  const todoText = document.createElement("p");
  todoText.textContent = todo.title;
  todoText.style.textDecoration = todo.completed ? "line-through" : "none";

  todoElement.appendChild(todoText);

  return todoElement;
}

function renderTodos(todos) {
  if (Array.isArray(todos)) {
    const todoElements = todos.map(createTodo);
    todosContainer.replaceChildren(...todoElements);
  } else {
    todosContainer.innerHTML = "<p>No todos found</p>";
  }
}
```

This example demonstrates:

1. Setting up TanStack Query Core
2. Creating a QueryClient instance
3. Defining a query function
4. Using `QueryObserver` to fetch data
5. Handling loading and error states via the `QueryObserver.subscribe` method
6. Rendering the data using vanilla JavaScript DOM manipulation

The example fetches todos from the JSONPlaceholder API and displays them in a list. It handles loading states and errors appropriately, and includes automatic background refetching every 30 seconds.

## Key Features Demonstrated

- Query Client initialization
- Data fetching with `fetchQuery`
- Loading and error states
- Automatic background refetching with `invalidateQueries`
- Cache management
- Vanilla JavaScript DOM manipulation

## Next Steps

- Learn about [Query Keys](/docs/guides/query-keys)
- Explore [Query Functions](/docs/guides/query-functions)
- Understand [Caching](/docs/guides/caching)
- Check out [Mutations](/docs/guides/mutations)

