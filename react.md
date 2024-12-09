# react

## Install

```
asdf install nodejs 16.13.0
asdf local nodejs 16.13.0

asdf install yarn 1.22.10
asdf local yarn 1.22.10

npx create-next-app@latest --typescript # answer questions
```

## Components & Elements

### Components

- a component is a function which returns a React element
- <Child/> is syntactic sugar for `React.createElement(Child, null, null)`
- takes props as input (params) and returns an element tree
- every instance of a component has its own state

### Elements

- an element is _NOT_ and instance of a component
- it's an object describing what you want to render (i.e. DOM nodes
  or other components)
- describes a subtree
  - can contain other elements in it's props (i.e. children)
- once created, the element is not mutated
- `console.log(Child)` shows an object:

      {
        type: Child
        props: {
          ...
        }
        ... // React stuff
      }

  - `type` can be a:
    - string (DOM element): e.g. `div`
    - function (component element): React calls this function to render it
  - DOM & component elements can be mixed within the same tree

## Props

- read-only
- can be accessed via `props` object param in functional components
- access via `this.props` in class components

## Rendering

- call root component function which returns element tree object
- for each element returned
  - if type string (DOM element), convert into DOM element
  - if function (component element), call function & recurse
- and so until there are no more functions, only DOM elements

## Re-rendering

Diff & reconcilliation.

- diff
  - shallow comparison of new object tree vs old
    - for objects it compares reference, not the values inside
  - if the same, skip re-render
  - if different (& type is the same), trigger re-render of the element
    and its children recursively
    - for children rendered in a parent via `<Child/>` the return value of
      `Child()` is an _new_ element object with a different object
      reference, therefore the shallow comparison will
      trigger a re-render of the child and its descendents

## Passing components as props

### To avoid re-renders

The following code will not re-render the child even when the parent's state
changes:

```JSX
  const Parent = ({child}) => {
    const [state, setState] = useState()
    return child
  }
```

- when state changes, `Parent()` is called to get its return element

- the `child` element was created above / outside the `Parent`, so its object reference is the same

- React shallow comparison sees no difference, so
  re-rendering of the child and its descendents is skipped

### The `children` prop

`children` is just another prop, but has a JSX syntax shortcut.

The following are equivalent:

```JSX
  <Parent>
    <Child1/>
    <Child2/>
  </Parent>
```

... is the same as:

```JSX
  <Parent
    children={
      <>
        Child1
        Child2
      </>
    }
  />
```

## Performance

### Avoiding re-renders

#### Move state down

In the following example, `VerySlowComponent`s will be re-rendered whenever
the button is clicked even if `App` they don't use the `isOpen` state.

```JSX
const App = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Open dialog</button>
      {isOpen && <Dialog onClose={() => setIsOpen(false)} />}
      <VerySlowComponent1>
      <VerySlowComponent2>
      ...
    </>
  )
}
```

Fix this by moving the state down into another component.

```JSX
const ModalWithDialog = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Open dialog</button>
      {isOpen && <Dialog onClose={() => setIsOpen(false)} />}
    </>
  )
}

const App = () => {
  return (
    <>
      <ModalWithDialog>
      <VerySlowComponent1>
      <VerySlowComponent2>
      ...
    </>
  )
}
```

Now only the `ModalWithDialog` has state and it the only component which
re-renders when the button is clicked.

## Hooks

- create state
  - useState()
    ```
      const [name, setName] = useState("David");
    ```
    - returns current value and a setter function
    - can pass a value or an **updater function** to the setter function
      - updater receives the current value as a param
      - useful if you want to update the value
        multiple times in the same event, e.g. increment a counter 3 times
  - useReducer()
- monitor state
  - useEffect()
    - for working with side effects outside of React, e.g. API call or timer
    - listen to changes in values in a dependency array
  - useMemo()
    - not the same as React.memo() - see below
    - cache a function _result_ between renders
    - re-runs function if anything in dependency array changes
    - not full memoisation: only caches previous value
  - useCallback()
    - caches a _function_ (not its result) between re-renders until
      dependency array changes
    - used to avoid re-rendering a child when a function is passed as props
      - an uncached function in the parent is new every time the parent renders
      - the child's props therefore change every time the parent renders
      - therefore the child re-renders
  - useRef()
    - to store but don't trigger re-render
    - examples:
      - focus form input field on the first render
      - context provider store where you implement a pub/sub model
        to avoid whole tree re-rendering when store values
        change

## React.memo()

- HOC wrapper
- wraps another component and only renders if props change
- shallow comparison only (immediate keys and values)
  - you can give your own comparison function
- not real memoisation:
  - only compares with previous value; does not cache historical results
  - previous value is cached per instance, not globally

## Preserving and resetting state

- state is tied to component's position in DOM tree
- React doesn't care about the code, only the tree structure
- state will be destroyed if another component is rendered at the same position
  - this applies to the state of the whole subtree !
- state is preserved if the same component type is put in the same position
  in the tree, even if props are different

## Component lifecycle

- mount: first render
  - create component instance for first time
  - initialises state
  - runs hooks
  - inserts elements into DOM
- unmount: unrendered
  - React detects component not needed any more
  - destroy instance & state
  - removes elements from DOM
- update: re-render
  - re-render starts with a state update somewhere (see below)
  - update instance with new data
  - faster than mount
  - all children are re-rendered recursively

## What causes re-rendering ?

- every re-render starts with a state update somewhere
- it's a MYTH that prop changes trigger re-renders
  - try updating props via a variable somewhere: nothing will happen
  - props only matter when using `React.memo()` or `useMemo()`
- custom hooks can "hide" the state, even if the state is used
  only internally in the hook or a chain of hooks
  - therefore moving state into a custom hook will not
    stop the component from re-rendering

###### Virtual DOM

- trigger / render / commit
- in memory copy of DOM used to compute & render changes
- commit - updates the DOM

###### Shadow DOM

- this is not React; it's a red-herring
- browser feature for scoping variables & CSS in web components

## Events

Event handlers are passed as props:

- `<button onClick={func}`
- `input type="text" onChange={func}`
  - value is `event.target.value`
- `form onSubmit={func}`
  - calling `event.preventDefault()` stops the form reloading the page when submitted
  - use state to store the form input fields

## State sharing options

- move into a parent component
  - pass changes down as props
- use context provider
  - re-renders everything `useContext()` if the context value changes, except
    - components wrapped in React.memo()
      - messy and not always possible
        (and all descendents), unless you:
    - use useRef() in the context provider and some kind of pub/sub
      model - i.e. implement your own state library (see Redux)
- use a 3rd party library, e.g. Redux
  - only relevent children will re-render

### Redux

- framework agnostic
- store
  - contains state pieces grouped into "slices"
- action
  - object defining what change you want to make to the state
- reducer
  - function which takes current state and action and returns new state
  - does NOT update the state directly (should be _pure_)
    - _except_ when using createSlice() where you modify state.value
      but under the covers it's a copy

## Hooks

### useEffect()

- `useEffect(setup, deps)`
- `deps` is optional dependencies array which affects when `setup()` is called:
  - no param
    - called after _every_ render (including the first)
  - empty array
    - called only after first render
  - array of values
    - called only when any of the values change
- `setup()` may return a `cleanup()` function:
  - passed the old deps values as params
  - before setup() on every render when dependencies change
  - one final time after removing the component from the DOM

## React Testing

### query methods

                   getBy    findBy   queryBy   getAllBy   findAllBy   queryAllBy
                ----------------------------------------------------------------
    no match   |   error    error    null      error      error       array
    1 match    |   return   return   return    array      array       array
    >1 match   |   error    error    error     array      array       array
    await      |   no       yes      no        no         yes         no

See https://testing-library.com/docs/queries/about/#types-of-queries

Try to use in this order (top to bottom, left to right):

- accessible to everyone: `getByRole`, `getByLabelText`, `getByPlaceholderText`, `getByText`, `getByDisplayValue`
- semantic: `getByAltText`, `getByTitle`
- test id: `getByTestId`

See https://testing-library.com/docs/queries/about/#priority

## Class Components

### State

- state attribute is an object with class attributes
- accessed via `this.state.{name}`
- use `this.setState({new-state-object})` for class components when changing state

### Lifecycle methods

- `componentDidMount` when rendered
  - can use to set initial state
- `componentDidUpdate()` when updated in the DOM

### comparison to useEffect()

Comparing the behaviour of useEffect(..., deps) dependency array param to lifecycle methods:

```
deps param               Class Lifecycle Methods
----------               -----------------------
no param                 componentDidMount() & componentDidUpdate()
[]                       componentDidMount()
[dep1, dep2, etc...]     componentDidUpdate()
```

# Patterns

## Render props

- TLDR: a function passed as a prop which returns a React element:
  - the prop function is defined in the parent and called by the child
- this _was_ a very common pattern and is used extensively in many codebases
- used rarely nowadays because has been mostly replaced by custom hooks
- still has some valid use cases

### Render props to share state (BAD)

- the child holds state and informs the parent
- hooks replace this pattern for 99% of use cases

```JSX
const ResizeDetector = ({children}) => {
  const [width, setWidth] = useState(...);
  useEffect(() => {
    const listener = () => setWidth(window.innerWidth);
    window.addEventListener('resize', listener);
  }, []);
  return children(width);
}

const App = () => {
  return
    <ResizeDetector>
      {(width) => width > 600 ? <Wide /> : <Narrow />}
    </ResizeDetector>
}

```

Use a custom hook instead (GOOD)

```JSX
const App = () => {
  const width = useResizeDetector()
  return width > 600 ? <Wide /> : <Narrow />
}
const useResizeDetector = () => {
  const [width, setWidth] = useState(...);
  useEffect(() => {
    const listener = () => setWidth(window.innerWidth);
    window.addEventListener('resize', listener);
  }, []);
  return width
}
```

### Render props for configuration flexibility (GOOD)

User of Button can override default props with their own:

```JSX
// A button which can render an icon
const Button = ({icon}) => {
  const defaultProps = { ... }
  const iconElement = icon(defaultProps)

  return <button>{iconElement}</button>
 <button {...props} />
}

<Button icon={(props) => <Icon {...props}} />
```

### Render props for state attached to the DOM (GOOD)

Tracking scroll inside an area to render different content.

```JSX
const ScrollDetector = ({children}) => {
  const [scroll, setScroll] = useState();
  return (
    <div onScroll={(e) => setScroll(e.target.scrollTop)}>
      {children(scroll)}
    </div>
  )
}
const App = () => {
  return (
    <ScrollDetector>
      {(scroll) => { return <>{scroll > 30 ? <SomeBlock /> : null}</>; }}
    </ScrollDetector>
  )
}
```

# Misc

- React Fiber - v16 reconcilliation engine
  - but there's a 3rd party library component react-error-boundary instead
- error boundary needs a class component with static getDerivedStateFromError method
- pure components render the same for the same state and props
- synthetic event - event object structured in a browser independent way
- uncontrolled components (vs controlled) - not every interaction stores state; you get their values from the DOM
- debounce - do something only once change activity stops (for a specified time)
- throttle - do something every x seconds during and after activity
