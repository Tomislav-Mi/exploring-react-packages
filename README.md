# Exploring React Packages and Automated Tests
My coding diary >> Getting started with React packages

## REDUX
- *States.*<br>
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
	
- *React Context.* <br>
	"Context" is a build-in feature in React that makes managing cross-component or app-wide states easier. You can avoid prop chains
	by having a central space where you manage your states. 
	
- *Redux - an alternative to React Context.* <br>
	Redux solves the same problem. Yes, same problem. So why do we need Redux when we already have Context? Well, Context has
	_potential_ disadvantages for your app. They might not matter, and if they don't matter you don't need Redux. 
	The potential disadvantages are:
	- Complexity. React Context can become complex, esp. when you're building a large app (e. g. nested JSX).
	- Performance. The performance can suffer for changes that happen frequently. 
	
- *Core Redux concepts.* <br>
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
- To sum it up, components subscribe to the central data store. A status change occurs. The component describes what action type should be targeted by dispatching an action. Then, this action is forwarded to 
		the reducer. The reducer, in turn, hits the target which the action has specified. Finally, the reducer provides a new state that will replace the old state in that central data store. And
		when this happens, subscripted components get notified so that they are able to update their UI. That's how Redux works on the most basic level.
- How does this look like in code?<br>
    I made the first careful steps outside of React (Redux also works outside of React).
    Here's a [video](https://youtu.be/H9nXFXTi3go) of me _trying_ to make sense of Redux.[^3] <br>
    One thing that I forgot to mention in the video is the reason why we have a second parameter (`action`) in `counterReducer`. In the reducer, I can go into that action to check if the passed-in argument is `action.type === 'increment'`. If that is true, 
    then I can return the `{counter: state.counter + 1}` object. Otherwise, I return the initial state. Or I can even return another action by, first, adding an action of type `'decrement'` somewhere outside of `counterReducer`, like so:

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

## REACT ROUTER 
- Important to understand: Pages in React are not loaded like in traditional multi-page routing where you have one HTML file for every URL. In React, you have one initial HTML request & response. Again, one-page routing means that one initial HTML request & response is made. 
To handle page (URL) changes in React, we need some sort of client-side code. That way,
we can change the visible content without fetching a new HTML file.<br>
How do we handle different paths in React? To illustrate how React approaches this problem, lets looks at this pseudo code: 
```
If (new domain path) {
	return respective component
	};
```
- I chose this pseudo code, because we can compare react routing to conditionally rendering. In conditionally rendering, we use "state" to conditionally show a 
certain component. In routing, we want to do the same thing but for the paths(!)
in the URL. I believe, if we wanted to, we could write our own code
where we check what's in the URL, and then set our own state, and listen
to clicks, and prevent the browser default of sending a request, and then
change again the state to render something else. BUT, since there is the
"React Router" and the react-router-dom package, React will do this
for us.  <br><br>
Here's a [video](https://youtu.be/wn6ClcnDLh0) of me talking about the basics of React Router. <br><br>
- `Link` component from react-router-dom. The `Link` component allows us to add links that, when clicked, prevent React to make a new HTML request. With `<Link to='/welcome'>Click this link!</Link>`, we will stay on the initial HTML, but it will look like we landed on a new page. 
- `NavLink`. The same like Link, but allows you to use `activeClassName`. This will enable you to link it to a CSS class you have defined to mark the active navbar item. 
- Dynamic Routes with Params. Extract route params with the custom react-router hook `useParams`. This will return an object that will have key-value pairs where the keys are dynamic segments leading to that page.
	- `Switch`. ONLY v5!! (Use version 6). The `Switch` component, wrapped around your route components, makes sure that only one of your route components is active. The switch goes through your components and it stops, it switsches itself off, at the route whose path is matched first, and that one is then rendered. The additionally `exact` prop will match the exact path, i. e. the full path of a component.[^4]
```javascript
function App() {
  return (
    <div>
        <MainHeader />
        <main>
            <Switch>
                <Route path="/welcome">
                    <Welcome />
                </Route>
                <Route path="/cats" exact>
                    <Cats />
                </Route>
                <Route path="/cats/:catName">
                    <CatDetail />
                </Route>
            </Switch>
        </main>
    </div>
  );
```
- Nested routes. When you have conditional content, like for example on a welcome page that should change depending on whether the user is new or not, then you want to be able to render different routes. Like so:
`domain.com/welcome`, or with a new user: `domain.com/welcome/new-user`
For that, we use nested routes.
```javascript
const Welcome = () => {
    return (
        <section>
            <h1>Welcome!</h1>
            <Route path="/welcome/new-user">
                <p>Welcome, new user!</p>
            </Route>
        </section>
    )
};
```

- Redirecting the user. We can redirect the user. For that, we define another route and use another custome component provided by react-router-dom, the `Redirect`. In there, you can specify where the user should be redirected to. For example:
```javascript
<Route path="/" exact>
    <Redirect to="/welcome"></Redirect>
</Route>
```
When users find themselves on "our-domain.com/", they will be redirected to "our-domain.com/welcome". Please note that `exact` is quite important here, because otherwise every route would be triggered that starts with `/`, and we would have created an infinite loop. THAT IS ONLY TRUE FOR v5. Version 6 avoids this trap.

## Automated Tests
- Unit Tests <br>
	- Test the individual building blocks in isolation (functions, components)
	- Most common and most important kind of test
	
- Integration Tests <br>
	- Test the combi of multiple building blocks
	- Important
	
- End-to-End Tests (e2e) <br>
	- Test complete scenarios in an app
	- Like manual test, only automated
	
- What and How to Test <br>
	- What?
		- Test the different building blocks, i. e. unit tests
	- How?
		- Test success and erros cases
		- Test rare results
- Required Tools <br>
	- Jest: for running our tests and asserting the result
	- React Testing Library: For simulating (rendering) our React app
- Running the first test <br>
	- Let's take a look at the test that comes with a new React project:
	```Javascript
	test('renders learn react link', () => {
  	render(<App />);
  	const linkElement = screen.getByText(/learn react/i);
  	expect(linkElement).toBeInTheDocument();
	});
	
	```
	- The first argument is a description of the test: `'renders learn react link'`
	- The second argument is a an anonymous function which contains the actual test and code. This is the code that will be executed once I run the test. 
		- In this function, I `render( )` the `<App />` component with the imported fn from the `'@testing-library/react'`
		- Then, I get hold of an element on the virtual screen with `screen`
		- Lastly, I `expect` the element `toBeInTheDocument`. 
			- If it is not there, the test will fail.
	- To run the test, we `npm test` in the terminal.
	- Here's a [video](https://youtu.be/PG2LBFe8QCA) where I talk about setting up a first test. 
	
	
[^1]: State is data that changes and effects the UI.
[^2]: That has nothing to do with the useReducer( ) hook.
[^3]: Redux createStore( ) is deprecated. Kind of. https://stackoverflow.com/questions/71944111/redux-createstore-is-deprecated-cannot-get-state-from-getstate-in-redux-ac
[^4]: React router in combination with switch looks by default at the beginning of your path and renders the first item that matches what it was looking for. So if you have two path that start with "/cats" the first one will be rendered. The second one, however, will be ignored.
