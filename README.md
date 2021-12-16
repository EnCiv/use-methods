# useMethods( functionThatReturnsObjectOfMethods, initialState, dependencies )

There are many implementations of useMethods - this differes from some of them because 
the function the declares the methods be delared within the react component, and it can use props without
causing unnecessary rerendering or unexpected firing of actions

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
### dependencies
An Array of variables, if one of these variable changes, the functionThatReturnsObjextOfMethods will be rememorized (with the new values of the variables)
By default this is [], meaning never rememoize the function

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