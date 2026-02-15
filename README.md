Below is a **documentation-level explanation** of the `useReducer` Hook in React, written in a clear, structured way.

---

# 1️⃣ What is `useReducer`?

`useReducer` is a React Hook used to manage **complex state logic** in functional components.

It is an alternative to `useState`.

You typically use it when:
- State has **multiple related values**
- State updates depend on the **previous state**
- You want **predictable, centralized state transitions**
- You want **Redux-like structure inside a component**

---

# 2️⃣ Official Syntax

```js
const [state, dispatch] = useReducer(reducer, initialArg, init?)
```

Let’s break down every part.

---

# 3️⃣ Import Syntax

```js
import { useReducer } from "react";
```

### What it does:
- Imports the `useReducer` Hook from React.
- Required before using it.

---

# 4️⃣ Full Structure Overview

There are 3 main parts:

1. **Reducer function**
2. **Initial state**
3. **useReducer call**
4. **Dispatching actions**

---

# 5️⃣ Reducer Function Syntax

```js
function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + 1 };

    case "DECREMENT":
      return { count: state.count - 1 };

    default:
      return state;
  }
}
```

### Syntax Breakdown

### `function reducer(state, action)`

- `reducer` → A pure function.
- `state` → Current state.
- `action` → Object describing what to do.

### Important Rule:
Reducer **must return new state**.
Never mutate state directly.

❌ Wrong:
```js
state.count++;
return state;
```

✅ Correct:
```js
return { count: state.count + 1 };
```

---

# 6️⃣ What is `action`?

An action is just an object:

```js
{
  type: "INCREMENT"
}
```

It can also carry data:

```js
{
  type: "SET_NAME",
  payload: "Forhad"
}
```

### Why object?
Because:
- It describes WHAT happened.
- It may carry extra information.

This pattern scales very well.

---

# 7️⃣ `switch(action.type)` Explanation

Why switch?

Because:
- You can handle multiple action types.
- It keeps logic organized.
- It makes state transitions predictable.

---

# 8️⃣ Initial State

```js
const initialState = {
  count: 0
};
```

This defines:
- The starting state of the component.

---

# 9️⃣ useReducer Basic Usage

```js
const [state, dispatch] = useReducer(reducer, initialState);
```

### What happens here?

React internally:
1. Stores `initialState`
2. Gives you current state as `state`
3. Gives you a function `dispatch`
4. When `dispatch` is called → reducer runs

---

# 🔟 `dispatch` Syntax

```js
dispatch({ type: "INCREMENT" });
```

### What happens internally?

1. React calls:
   ```
   reducer(currentState, action)
   ```
2. Reducer returns new state
3. React updates component
4. Component re-renders

---

# 1️⃣1️⃣ Full Working Example

```js
import { useReducer } from "react";

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + 1 };

    case "DECREMENT":
      return { count: state.count - 1 };

    case "RESET":
      return initialState;

    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      <p>{state.count}</p>

      <button onClick={() => dispatch({ type: "INCREMENT" })}>
        +
      </button>

      <button onClick={() => dispatch({ type: "DECREMENT" })}>
        -
      </button>

      <button onClick={() => dispatch({ type: "RESET" })}>
        Reset
      </button>
    </>
  );
}
```

---

# 1️⃣2️⃣ Third Argument (Lazy Initialization)

Advanced syntax:

```js
const [state, dispatch] = useReducer(reducer, initialArg, initFunction);
```

### Why this exists?

When initial state calculation is expensive.

---

### Example

```js
function init(initialValue) {
  return { count: initialValue };
}

const [state, dispatch] = useReducer(reducer, 10, init);
```

### What happens?

React runs:
```
init(10)
```

Only once — during first render.

Useful for:
- Large data setup
- Parsing localStorage
- Expensive calculations

---

# 1️⃣3️⃣ Rules of Reducer

Reducer must:

1. Be pure function
2. Not mutate state
3. Not perform side effects
4. Always return state

❌ No API calls
❌ No setTimeout
❌ No console side-effects ideally

---

# 1️⃣4️⃣ When to Use `useReducer` Instead of `useState`

### Use `useState` when:
- Simple state (boolean, number, input field)

Example:
```js
const [isOpen, setIsOpen] = useState(false);
```

---

### Use `useReducer` when:
- State has multiple fields
- One action changes many fields
- Complex logic
- Form handling
- Nested objects
- Business logic needs structure

---

# 1️⃣5️⃣ Real-World Example (Form)

```js
const initialState = {
  name: "",
  email: "",
  isSubmitting: false
};

function reducer(state, action) {
  switch (action.type) {
    case "SET_FIELD":
      return {
        ...state,
        [action.field]: action.value
      };

    case "SUBMIT_START":
      return { ...state, isSubmitting: true };

    case "SUBMIT_END":
      return { ...state, isSubmitting: false };

    default:
      return state;
  }
}
```

Why better than multiple useState?

Instead of:

```js
const [name, setName] = useState("");
const [email, setEmail] = useState("");
const [isSubmitting, setIsSubmitting] = useState(false);
```

You centralize everything.

Cleaner.  
More scalable.

---

# 1️⃣6️⃣ Internal Mental Model

Think of it like:

```
User clicks → dispatch(action)
↓
Reducer decides new state
↓
React updates UI
```

This is exactly how Redux works.

---

# 1️⃣7️⃣ Common Pattern in Production

```
state → UI
UI → dispatch(action)
action → reducer
reducer → new state
new state → UI update
```

Very predictable.

---

# 1️⃣8️⃣ Advantages

- Better for complex state
- Centralized logic
- Easier debugging
- Scales well
- Fewer bugs in large apps
- More maintainable

---

# 1️⃣9️⃣ Disadvantages

- More boilerplate
- Overkill for simple state
- Harder for beginners

---

# 2️⃣0️⃣ Industry Usage

In real production apps:

- Simple components → useState
- Complex forms → useReducer
- Large app global state → Redux / Zustand / Context + useReducer

---

# 2️⃣1️⃣ Key Interview Points

If interviewer asks:

👉 Why useReducer?

Answer:

- Centralizes state transitions
- Avoids scattered state updates
- Prevents accidental mutation
- More predictable than multiple useState
- Good for scaling

---

# Final Mental Model

If state is:

Simple → useState  
Complex → useReducer  
Global → Redux / Context  

---

If you want next, I can show:

- How bugs happen in useState but not in useReducer
- Advanced patterns (middleware style)
- How React internally schedules reducer updates
- How batching works
- Performance comparison

Just tell me which direction you want.
