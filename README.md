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
An Array of variables, if one of these variable changes, the functionThatReturnsObjectOfMethods will be rememorized (with the new values of the variables)
By default this is [], meaning never rememoize the function

## returns [state, methods]
use-methods returns an array containing state, and methods. The array returned will change whenever there is a change within state or methods. 

- `state`  can not be used to determine if there is a change within state from a previous value of state. If you want to pass something as a prop to a child function and cause a rerender on changes in state, pass the array.   **Why?**: if state itself changes (is immutable), the methods have to be reinstantiated with the new value of state every time, causing extra load, and potentially causing side effects within the methods.  Methods don't have to be pure functions, it's up to the implementation.  

``` 
    // DisplaySomething will never re-render on a state change
    const [state,methods]=useMethods((stsate,dispatch)=>{...}, {key: value}, [])
    return <DisplaySomething state={state} ... >

    // this works
    const electionOM=useMethods((stsate,dispatch)=>{...}, {key: value}, [])
    return <DisplaySomething electionOM={electionOM} ... >
    
```
- `methods` will only change if there is a change in `deps`

## Example
```
import useMethods from 'use-methods'

function DisplayCount(props){
    const {incrementAmount=1}=props
    const [state, methods] = useMethods(
        (dispatch,state)=>{
            increment(){
                dispatch({count: state.count+incrementAmount})
            }
        }, 
        {count: 0}, 
        props.incrementAmount
    )
    return (
        <div>
            <div>{state.count}</div>
            <button onClick(()=>methods.increment())</button>
        </div>
    )
}
```
