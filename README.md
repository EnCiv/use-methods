# useMethods( functionThatReturnsObjectOfMethods, initialState, dependencies )

There are many implementations of useMethods - this differes from some of them because 
the function that declares the methods can be declared within the react component, and it can use props without
causing unnecessary rerendering or unexpected firing of methods.

## functionThatReturnsObjectOfMethods( state, dispatch )
### state
the object representing the current state
### dispatch (new state)
the function to call with the new state keys.  No need to do (...state, key: 'value') becuase the ...state will be taken care of in the dispatch
```
function (state,dispatch){
    return {
        method1(p1,p2,...) { 
            dispatch({key: 'value'})
        },
        method2() {
            dispatch({key2: 'value'})
        }
    }
}
```
## dependencies
An Array of variables, if one of these variable changes, the functionThatReturnsObjectOfMethods will be re-memoize (with the new values of the variables)
By default this is [], meaning never re-memoize the function

## returns [state, methods]
use-methods returns an array containing state, and methods. The array returned will change whenever there is a change within state or methods. 

- `state`  can not be used to determine if there is a change within state from a previous value of state. If you want to pass something as a prop to a child function and cause a rerender on changes in state, pass the array.  Methods don't have to be pure functions, it's up to the implementation. An example would be a method that saves a value in state, and makes an api call to write it to a database. 

```js
    // bad - DisplaySomething will never re-render on a state change
    const [state,methods]=useMethods((state,dispatch)=>{...}, {key: value}, [])
    return <DisplaySomething state={state} ... >
```
```js
    // this works
    const countOM=useMethods((state,dispatch)=>{...}, {key: value}, [])
    return <DisplaySomething countOM={countOM} ... >
    
```
And typically, if you are passing state to a child component you will also want to pass methods so the child can operate on the state.

- `methods` will only change if there is a change in `deps`

## Example
```js
import useMethods from 'use-methods'

function DisplayCount(props){
    const [state, methods] = useMethods(
        (dispatch,state)=>({
            increment(){
                dispatch({count: state.count+props.incrementAmount})
            }
        }), 
        {count: 0}, 
        [props.incrementAmount]
    )
    return (
        <div>
            <div>{state.count}</div>
            <button onClick={()=>methods.increment()}>Increment</button>
        </div>
    )
}

ReactDom.render(<DisplayCount incrementAmount={2}>, getElementById('root'))
```
In the above example, `functionThatReturnsObjectOfMethods` depends on a prop, and so it has to be delcared within DisplayCount, and still works efficiently. But if it doesn't depend on a prop, then it would be fine, and more efficieint to delcare it outside the function.

```js
const countMethods= (dispatch,state)=>({
    increment(){
        dispatch({count: state.count+2})
    }
})

function DisplayCount(props){
    const [state, methods] = useMethods(
        countMethods,
        {count: 0}
    )
    return (
        <div>
            <div>{state.count}</div>
            <button onClick={()=>methods.increment()}>Increment</button>
        </div>
    )
}

ReactDom.render(<DisplayCount>, getElementById('root'))
```
