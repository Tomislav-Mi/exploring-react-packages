# Exploring-redux (React diary)
My coding diary >> Getting started with Redux.

## What I've learned
- REDUX
	- States.
	States in React can be divided by their reach.<br>
	First, we have the local state that belongs to a single component.[^1] A good example: 
	When we listen to a user input, and we use `useState()` to store that input with every keystroke
	in a state variable. Like for Berghain, the rule is: What happens in the component
	stays in the component.<br> 
	Then, we have the cross-component state. Yes, the name says it all: it's a state that affects multiple components.
	An example for that is a button that opens and closes a modal overlay. Such a modal component
	might affect multiple components. The trigger for opening the modal will probably not be inside
	of the modal. However, the modal might be closed by clicking a button that _is_ inside
	of that modal. You see, we have multiple components working together to display and hide
	a modal. For that, we have "props". To work together and to manage state together, components pass `props` between each other, 
	building a chain of props.<br>
	Finally, we have the app-wide state. These are states that affect most, if not all components in an app.
	A good example for that is the user authentication status. The user signs in, and the app changes: a new nav bar is displayed,
	more options to choose from are shown etc. To manage the status, we can, again, use a prop chain. That, however, can
	become cumbersome. 
	
	- React Context.
	"Context" is a build-in feature in React that makes managing cross-component or app-wide states easier. You can avoid prop chains
	by having a central space where you manage your states. 
	
	- Redux - An alternative to React Context.
	Redux solves the same problem. Yes, same problem. So why do we need Redux when we already have Context? Well, Context has
	_potential_ disadvantages for your app. They might not matter, and if they don't matter you don't need Redux. 
	The potential disadvantages are:
		- Complexity. React Context can become complex, esp. when you're building a large app (e. g. nested JSX).
		- Performance. The performance can suffer for changes that happen frequently. 
	
	- Core Redux concepts.
	Redux has one central state store. One store to rule them all.
	In there, you store authentication state, user input state... Whatever app wide states you have, 
	they belong in that one store. 
		- Subscriptions. 
		When a state in the central store changes we want to know about it and pass this information to our components.
		For that, a component sets up a "subscription" to our central store. After the subscription, the component gets a notification
		whenever a status changes. The component, then, gets the specific information of that change, for instance,
		the current authentication status. That's how we get data out of the store.
		- Now we also need a way to change the data in the store. But, first, here's an important rule for that: components should never 
		manipulate data in the store. We can and should have a subscription, but we must not have a data flow in the other direction.
		At least not a direct data flow. 
		- The Reducer Concept. To manipulate data in the store, we use a so called "Reducer Function".[^2] This function is responsible 
		for mutating the store data. It's a general concept of functions that take some input and then transform that input.
		- Okay. On one side, we have our component with a subscription to the central store that allows the component
		to know that a change occurred. On the other side, we have a reducer function which is capable of updating the central data store.
		How to connect these two elements? 
		Ultimately, it will be the component that should trigger some data change. Trigger is a good word for that. Because we have a third concept here.
		- Actions. Components dispatch "actions" to communicate with the Reduce function. An action is, essentially, a simple JavaScript object which describes the "action" the reducer should perform. 
		Redux forwards actions to the reducer, reads the description of that desired operation, and then this operation is performed by the reducer. 
	- To sum it up, components subscribe to the central data store. A status change occurs. The component describes what should be done by dspatching an action. Then, this action is forwarded to 
		the reducer. The reducer, in turn, does what the action instructs the reducer to do. Finally, the reducer provides a new state that will replace the old state in that central data store. And
		when this happens, subscripted components get notifed so that they are able to update their UI. That's how Redux works.
  - How does this look like in code?<br>
    I made the first careful steps outside of React (Redux also works outside of React).
    Here's a [video](https://youtu.be/H9nXFXTi3go) of me _trying_ to make sense of Redux.[^3] <br>
    One thing that I forgot to mention in the video is the reason why we have a second argument (`action`) in `counterReducer`. In the reducer, I can go into that action to check if `action.type === 'increment'`. If that is true, 
    then I can return the `{counter: state.counter + 1}` object. Otherwise, I return the initial state. Or I can even return other actions by, first, adding an action of type 'decrement', like so:

	```javascript
	store.dispatch({type:'decrement'});
	```
	I, then, add a second `if` check in `counterReducer`:
	
	```javascript
	const counterReducer = (state = {counter: 0}, action) => {
		if (action.type === 'increment') {
			return {
				counter:state.counter + 1,
			};
		}
		
		if (action.type === 'decrement') {
			return {
				counter:state.counter - 1,
			};
		}	
	}
	```

    

		
[^1]: State is data that changes and effects the UI.
[^2]: That has nothing to do with the useReducer( ) hook.
[^3]: Redux createStore( ) is deprecated. Kind of. https://stackoverflow.com/questions/71944111/redux-createstore-is-deprecated-cannot-get-state-from-getstate-in-redux-ac
