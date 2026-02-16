Here is the **documentation-level breakdown of the `dispatch` function syntax in React `useReducer`**, explained step-by-step and deeply.

---

# 1️⃣ Basic Syntax of `dispatch`

```js
dispatch(action)
```

That’s it.

But internally, it is more structured than it looks.

---

# 2️⃣ Where Does `dispatch` Come From?

```js
const [state, dispatch] = useReducer(reducer, initialState);
```

`dispatch` is returned by `useReducer`.

React creates it internally and guarantees:

* Stable reference (does not change between renders)
* Safe to pass as prop
* Safe to use inside `useEffect`

---

# 3️⃣ Full Conceptual Signature

Although you only write:

```js
dispatch(action)
```

Conceptually it behaves like:

```ts
dispatch(action: ActionObject): void
```

It:

* Takes **one argument**
* That argument is usually an object
* Returns nothing (`void`)
* Triggers re-render if state changes

---

# 4️⃣ Action Object Syntax

Standard action format:

```js
{
  type: "ACTION_NAME"
}
```

More advanced:

```js
{
  type: "ACTION_NAME",
  payload: any
}
```

Even more structured:

```js
{
  type: "UPDATE_USER",
  payload: {
    id: 1,
    name: "Forhad"
  }
}
```

---

# 5️⃣ What Happens Internally When You Call dispatch?

When you do:

```js
dispatch({ type: "INCREMENT" });
```

React internally:

1. Gets current state
2. Calls:

   ```js
   reducer(currentState, action)
   ```
3. Gets returned newState
4. Compares old state vs new state
5. If different → schedules re-render

---

# 6️⃣ Important Rule: dispatch Accepts ONE Argument

❌ Wrong:

```js
dispatch("INCREMENT");
dispatch("INCREMENT", 5);
```

✅ Correct:

```js
dispatch({ type: "INCREMENT" });
```

If you need extra data:

```js
dispatch({
  type: "INCREMENT_BY",
  payload: 5
});
```

---

# 7️⃣ Dispatch With Payload Example

Reducer:

```js
function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT_BY":
      return { count: state.count + action.payload };

    default:
      return state;
  }
}
```

Dispatch:

```js
dispatch({ type: "INCREMENT_BY", payload: 10 });
```

---

# 8️⃣ Dispatch Inside Event Handler

```js
<button onClick={() => dispatch({ type: "RESET" })}>
  Reset
</button>
```

Here:

* Button clicked
* dispatch called
* Reducer runs
* UI updates

---

# 9️⃣ Dispatch With Dynamic Data

Example:

```js
<input
  onChange={(e) =>
    dispatch({
      type: "SET_NAME",
      payload: e.target.value
    })
  }
/>
```

Reducer:

```js
case "SET_NAME":
  return { ...state, name: action.payload };
```

---

# 🔟 Dispatch With Multiple Fields

```js
dispatch({
  type: "UPDATE_USER",
  payload: {
    name: "Forhad",
    email: "test@mail.com"
  }
});
```

Reducer:

```js
case "UPDATE_USER":
  return {
    ...state,
    ...action.payload
  };
```

---

# 1️⃣1️⃣ Dispatch in useEffect

```js
useEffect(() => {
  dispatch({ type: "FETCH_START" });
}, []);
```

Safe because:

* `dispatch` identity is stable.
* React guarantees it won’t change.

---

# 1️⃣2️⃣ Dispatch is NOT Async

Very important.

This:

```js
dispatch({ type: "INCREMENT" });
console.log(state);
```

Will log OLD state.

Why?

Because React schedules update.
State updates are asynchronous.

---

# 1️⃣3️⃣ Dispatch Does NOT Merge State

Unlike `setState` in class components:

```js
this.setState({ name: "Forhad" });
```

`useReducer` does NOT merge automatically.

You must manually merge:

```js
return {
  ...state,
  name: action.payload
};
```

---

# 1️⃣4️⃣ Dispatch Can Trigger Complex Transitions

Example:

```js
dispatch({ type: "LOGIN_SUCCESS", payload: userData });
```

Reducer:

```js
case "LOGIN_SUCCESS":
  return {
    ...state,
    user: action.payload,
    isLoggedIn: true,
    loading: false,
    error: null
  };
```

One dispatch → multiple state updates.

This is where useReducer becomes powerful.

---

# 1️⃣5️⃣ Dispatch Stability Guarantee

React guarantees:

```js
dispatch === previousDispatch
```

Always true.

That means:

* No need to wrap in `useCallback`
* Safe to pass deeply

---

# 1️⃣6️⃣ Advanced: Lazy Dispatch Pattern (Function Wrapper)

You cannot do:

```js
dispatch(() => { ... });
```

That is `useState` functional update pattern.

`useReducer` expects an action object, not a function.

If you need async logic:

Do this:

```js
const handleFetch = async () => {
  dispatch({ type: "FETCH_START" });

  const data = await fetchData();

  dispatch({ type: "FETCH_SUCCESS", payload: data });
};
```

---

# 1️⃣7️⃣ Dispatch Flow Mental Model

```
UI event
↓
dispatch(action)
↓
reducer(state, action)
↓
new state returned
↓
React compares states
↓
Re-render if changed
```

---

# 1️⃣8️⃣ Common Production Pattern (Action Creators)

Instead of writing:

```js
dispatch({ type: "INCREMENT" });
```

You can define:

```js
const increment = () => ({ type: "INCREMENT" });
```

Then:

```js
dispatch(increment());
```

This improves scalability in large apps.

---

# 1️⃣9️⃣ TypeScript Version (Professional Level)

```ts
type Action =
  | { type: "INCREMENT" }
  | { type: "DECREMENT" }
  | { type: "SET_NAME"; payload: string };

const [state, dispatch] = useReducer(reducer, initialState);
```

Now dispatch is strongly typed.
No wrong action types allowed.

---

# 2️⃣0️⃣ When dispatch Does NOT Re-render

If reducer returns same state reference:

```js
return state;
```

React does not re-render.

It uses Object.is comparison.

---

# Final Deep Understanding

`dispatch` is:

* A trigger
* A state transition initiator
* A scheduler
* A controlled entry point into reducer logic

It does NOT:

* Modify state directly
* Merge state
* Work like setState functional updater
* Execute async logic automatically

---

If you want next:

* How React internally implements dispatch
* How batching works with dispatch
* How to build Redux using useReducer
* How race conditions happen with dispatch

Tell me which direction you want.
