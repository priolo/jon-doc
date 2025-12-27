# From Redux Fatigue to VUEX Dreams: How I Built Jon, a React State Manager That Actually Makes Sense

*TL;DR: Tired of Redux boilerplate and dreaming of VUEX's simplicity? I created Jon - a tiny React state manager inspired by VUEX that weighs less than 2KB and doesn't hide its magic.*

## The Problem: Redux Fatigue is Real

Picture this: You're deep into a React project. Your components are talking to each other through props like they're playing telephone across a crowded room. State is scattered everywhere. You know you need proper state management, but the thought of setting up Redux makes you want to take a nap.

Don't get me wrong - Redux is powerful. But let's be honest: for most projects, it feels like using a sledgehammer to crack a nut. Actions, reducers, middleware, connect functions, mapStateToProps... By the time you've set up a simple counter, you've written more boilerplate than actual business logic.

## The VUEX Envy

Meanwhile, Vue developers are living their best life with VUEX. Look at this beauty:

```js
const store = {
    state: { count: 0 },
    getters: { doubleCount: state => state.count * 2 },
    actions: { increment: (state, payload, store) => store.setCount(state.count + 1) },
    mutators: { setCount: (state, count) => ({ count }) }
}
```

Clean. Intuitive. Everything in one place. No ceremony, no fuss. Just a simple object that perfectly describes your application's state management needs.

But we're React developers, and we don't get nice things... or do we?

## Enter React 18's Hidden Gem

React 18 quietly introduced `useSyncExternalStore` - a hook that lets you connect to external state managers. Most developers haven't even heard of it, but it's a game-changer. It's what Redux Toolkit and Zustand use under the hood.

This hook got me thinking: What if I could bring VUEX's elegant API to React using this native hook?

## Jon is Born: VUEX Meets React

After countless hours of tinkering (and probably too much coffee), Jon was born. It's a state manager that feels like VUEX but works seamlessly with React.

Here's the same counter example with Jon:

```jsx
// Create your store
const counterStore = createStore({
    state: { count: 0 },
    getters: {
        doubleCount: state => state.count * 2
    },
    actions: {
        increment: (state, amount, store) => {
            store.setCount(state.count + amount)
        }
    },
    mutators: {
        setCount: (state, count) => ({ count })
    }
})

// Use it in your component
function Counter() {
    const state = useStore(counterStore)
    
    return (
        <div>
            <h1>{state.count}</h1>
            <h2>Double: {counterStore.doubleCount()}</h2>
            <button onClick={() => counterStore.increment(1)}>
                Increment
            </button>
        </div>
    )
}
```

That's it. No providers (unless you want them), no connect functions, no boilerplate. Just clean, readable code.

## Why Jon is Different

### 1. **No Magic, Pure Transparency**
You can literally copy Jon's source code (it's tiny!) and understand exactly how it works. No black boxes, no hidden optimizations that might bite you later.

### 2. **Familiar if You Know VUEX**
The API is intentionally similar to VUEX. If you've used Vue, you'll feel right at home. If you haven't, it's still intuitive.

### 3. **Tiny Bundle Size**
Jon weighs in at under 2KB minified. Your users' bandwidth will thank you.

### 4. **React Devtools Integration**
Since Jon uses React's native hooks, it plays beautifully with React DevTools. No additional extensions needed.

### 5. **No Dependencies**
Jon only depends on React. That's it. No lodash, no immer, no utility libraries that might conflict with your project.

## Real-World Example: A Todo App

Let me show you how Jon handles a real application. Here's a complete todo app:

```jsx
const todoStore = createStore({
    state: {
        todos: [],
        filter: 'all',
        newTodo: ''
    },
    getters: {
        filteredTodos: state => {
            switch(state.filter) {
                case 'active': return state.todos.filter(t => !t.completed)
                case 'completed': return state.todos.filter(t => t.completed)
                default: return state.todos
            }
        },
        remainingCount: state => state.todos.filter(t => !t.completed).length
    },
    actions: {
        addTodo: (state, _, store) => {
            if (!state.newTodo.trim()) return
            const todo = { 
                id: Date.now(), 
                text: state.newTodo, 
                completed: false 
            }
            store.setTodos([...state.todos, todo])
            store.setNewTodo('')
        },
        toggleTodo: (state, id, store) => {
            const todos = state.todos.map(todo =>
                todo.id === id ? { ...todo, completed: !todo.completed } : todo
            )
            store.setTodos(todos)
        }
    },
    mutators: {
        setTodos: (state, todos) => ({ todos }),
        setFilter: (state, filter) => ({ filter }),
        setNewTodo: (state, newTodo) => ({ newTodo })
    }
})
```

Look at that structure! Everything has its place:
- **State**: Your single source of truth
- **Getters**: Computed values (like React's useMemo, but for the store)
- **Actions**: Where side effects and complex logic live
- **Mutators**: The only functions that can change state

## The Philosophy: Small, Focused, Transparent

Jon follows the Unix philosophy: do one thing and do it well. It's not trying to be a full framework. It's not trying to solve routing, or forms, or animations. It's a state manager, period.

This focus means:
- **Easier to learn**: Less surface area to understand
- **Easier to debug**: When something goes wrong, you know where to look
- **Easier to replace**: If you outgrow Jon, migration is straightforward

## When NOT to Use Jon

Let's be honest about Jon's limitations:

- **Huge applications**: If you're building the next Facebook, you might need something more robust
- **Complex async patterns**: Redux Saga's power might be worth the complexity
- **Team with Redux expertise**: Don't fix what isn't broken
- **Need for middleware ecosystem**: Redux's plugin ecosystem is unmatched

Jon is perfect for:
- Small to medium applications
- Teams tired of Redux boilerplate
- Prototypes and MVPs
- Projects where bundle size matters
- Developers who value simplicity

## Getting Started

Ready to try Jon? It's as simple as:

```bash
npm install @priolo/jon
```

Or if you're feeling adventurous (and want zero dependencies), you can copy the 50 lines of source code directly into your project. Yes, that's really all it is!

## The Future

Jon isn't trying to take over the world. It's trying to solve a specific problem: giving React developers a simple, transparent way to manage state without drowning in boilerplate.

Will it replace Redux? Probably not. Will it make your next project more enjoyable to work on? I hope so.

The React ecosystem is rich with choices, and that's a good thing. Jon is just another tool in your toolkit - one that prioritizes simplicity, transparency, and developer experience over feature completeness.

## Try It Yourself

Don't take my word for it. Here's a [live demo](https://codesandbox.io/s/to-do-p24qhx?file=/src/store.js) you can play with right now.

Star the [GitHub repo](https://github.com/priolo/jon) if you find it useful, or better yet, try it in your next side project. 

Remember: the best state manager is the one your team actually understands and enjoys using. For some teams, that might be Redux. For others, it might be Zustand or Jotai. 

And maybe, just maybe, for some teams, it might be Jon.

---

*What do you think? Have you experienced Redux fatigue? What's your current go-to state management solution? Let me know in the comments!*

**Links:**
- [Jon on GitHub](https://github.com/priolo/jon)
- [Documentation](https://priolo.github.io/jon-doc/)
- [Live Examples on CodeSandbox](https://codesandbox.io/s/to-do-p24qhx)
- [Template for quick start](https://github.com/priolo/jon-template)
