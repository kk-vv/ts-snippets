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
| `zusfact` | Creates a Zustand store factory with Immer middleware, exporting multiple stores with customizable names (e.g., `useTodoStore1`, `useTodoStore2`) and distinct initial counter values. |
| `usectx`  | Creates a React Context for with a customizable name (e.g., `useTodoContext`). Restricted to `.tsx` files.|
| `userdctx`| Creates a React Context with `useReducer` for a counter with a customizable name (e.g., `useTodoContext`), including async `onFetchData` and `onResetAll` actions. Restricted to `.tsx` files. |


## Example
Type `zus`, enter `Todo`, and get:
```typescript
import { create } from 'zustand';
import { immer } from 'zustand/middleware/immer';

interface States {
  count: number;
}

const initialStates: States = {
  count: 0,
};

interface Actions {
  onIncrease: () => void;
  onReset: () => void;
}

const useTodoStore = create<States & Actions>()(
  immer((set, get) => ({
    ...initialStates,
    onIncrease: () => {
      set((state) => {
        state.count += 1;
      });
    },
    onReset() {
      set({ ...initialStates });
    },
  }))
);

export default useTodoStore;