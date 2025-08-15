# Zustand Immer Snippets

A snippet for creating customizable Zustand stores with Immer middleware in JavaScript and TypeScript, with fixed `States` and `Actions` interfaces.

## Features
- Generates a Zustand store with Immer middleware.
- Customizable store name (e.g., `useTodoStore`, `useUserStore`).
- Fixed `interface States`, `interface Actions`, and `const initialStates` for consistency.
- Includes a basic counter state with `onIncrease` and `onReset` actions.

## Usage
1. Install the extension from the VS Code Marketplace.
2. In a JavaScript or TypeScript file, type `zus` and press `Tab`.
3. Enter a custom store name (e.g., `Todo`, `User`) when prompted.
4. The snippet will generate a store with the specified name, using fixed `States` and `Actions` interfaces.

|Command|Description|
|----|----|
| `zus`     | Creates a single Zustand store with Immer middleware and a customizable name (e.g., `useTodoStore`), including a counter with `onIncrease` and `onReset` actions. |
| `zuspst`| Creates a persistent Zustand store with Immer middleware and a customizable name (e.g., `useTodoStore`), including a counter with `onIncrease` and `onReset` actions.|
| `zusfact` | Creates a Zustand store factory with Immer middleware, exporting multiple stores with customizable names (e.g., `useTodoStore1`, `useTodoStore2`) and distinct initial counter values. |
| `usectx`  | Creates a React Context for with a customizable name (e.g., `useTodoContext`).|
| `userdctx`| Creates a React Context with `useReducer` for a counter with a customizable name (e.g., `useTodoContext`), including async `onFetchData` and `onResetAll` actions.|


## Example

- Type `zus`, enter `Todo`, and get:

```typescript
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'

interface States {
  count: number
}

const initialStates: States = {
  count: 0,
}

interface Actions {
  onIncrease: () => void
  onReset: () => void
}

const useTodoStore = create<States & Actions>()(
  immer((set, get) => ({
    ...initialStates,
    onIncrease: () => {
      set((state) => {
        state.count += 1
      })
    },
    onReset() {
      set({ ...initialStates })
    },
  }))
)

export default useTodoStore
```

- Type `userdctx`, enter `Todo`, and get:

```
"use client"
import React, { createContext, useCallback, useContext, useMemo, useReducer } from "react"

interface States {
  count: number
  status: 'idle' | 'loading' | 'success' | 'error'
  error: string | null
}

interface Actions {
  onFetchData: () => Promise<void>
  onResetAll: () => void
}

type Action =
  | { type: 'INCREMENT_COUNT'; payload: number }
  | { type: 'RESET' }
  | { type: 'SET_STATUS'; payload: States['status'] }
  | { type: 'SET_ERROR'; payload: string | null }

type TodoContextType = States & Actions

const initialStates: States = {
  count: 0,
  status: 'idle',
  error: null,
}

const reducer = (state: States, action: Action) => {
  switch (action.type) {
    case "INCREMENT_COUNT":
      return { ...state, count: state.count + action.payload }
    case "RESET":
      return { ...initialStates }
    case "SET_STATUS":
      return { ...state, status: action.payload }
    case "SET_ERROR":
      return { ...state, error: action.payload }
  }
}

const TodoContext = createContext<TodoContextType | undefined>(undefined)

export const TodoProvider = ({ children }: { children: React.ReactNode }) => {
  const [state, dispatch] = useReducer(reducer, initialStates)

  const onFetchData = useCallback(async () => {
    dispatch({ type: 'SET_STATUS', payload: 'loading' })
    try {
      // simulate
      await new Promise((r) => setTimeout(r, 1500))
      dispatch({ type: 'INCREMENT_COUNT', payload: 20 })
      dispatch({ type: 'SET_STATUS', payload: 'success' })
    } catch {
      dispatch({ type: 'SET_STATUS', payload: 'error' })
      dispatch({ type: 'SET_ERROR', payload: 'failed to fetch data' })
    }
  }, [])

  const onResetAll = useCallback(() => {
    dispatch({ type: 'RESET' })
  }, [])

  const value = useMemo(() => (
    {
      ...state,
      onFetchData,
      onResetAll,
    }
  ), [state, onFetchData, onResetAll])

  return <TodoContext.Provider value={value}>{children}</TodoContext.Provider>
}

export const useTodoContext = () => {
  const context = useContext(TodoContext)
  if (!context) {
    throw new Error("useTodoContext must be used within an TodoProvider")
  }
  return context
}
```