# react

## Install

```
asdf install nodejs 16.13.0
asdf local nodejs 16.13.0

asdf install yarn 1.22.10
asdf local yarn 1.22.10

npx create-react-app app --template typescript
# creates the app dir


```

## Virtual DOM

- react only re-renders elements which have changed

## Props

- read-only
- access via `props` param in functional components
- access via `this.props` in class components

```
class Hello extends React.Component {
  render() {
    return <p>Hello, {this.props.name}!</p>;
  }
}
```

## State

class components:

- state attribute is an object with attributes
- accessed via `this.state.{name}`
- use `this.setState({new-state-object})` for class components when changing state
  - triggers a re-render of affected component
- stateful components have state
- stateless components have no state

functional components:

- use hooks
- hooks can only be used in functional components

```
  const [name, setName] = useState("David");
```

## Lifecycle methods (class components)

- mount = rendered
- unmount = unrendered
- `componentDidMount` when rendered
  - can use to set initial state
- `componentDidUpdate()` when updated in the DOM

## Common events

Element attributes:

- `<button onClick={func}`
- `input type="text" onChange={func}`
  - value is `event.target.value`
- `form onSubmit={func}`
  - calling `event.preventDefault()` stops the form reloading the page when submitted
  - use state to store the form input fields

## sharing state

- use a parent component
- can only pass state down
- can also pass functions down

## Redux

- redux can be used with any front-end framework

## Hooks

### useEffect()

- `useEffect(callback, dependencies)`
- `dependencies` is an optional array:
  - no dependencies param
    - callback called after _every_ render (including the first)
  - empty array
    - callback called only after first render
    - same as `componentDidMount()`
  - array of values
    - callback called only when any of the values change
    - same as `componentDidUpdate()`
- callback may return a _cleanup_ function
  - TODO
- combines the following class component lifecycle methods:
  - `componentDidMount`
  - `componentDidUpdate`
  - `componentWillUnmount`

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
