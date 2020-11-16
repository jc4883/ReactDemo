This is a document to introduce you to React. It'll walk you through setting up a react project and deploying it on AWS S3.
We'll build a website that renders a random joke from a jokes API.



Ok, start a react app using Create React App. This is the officially recommended to start a hobby react project.
https://create-react-app.dev/


first, go to the directory where you want to hold your code:
$ cd ~/Desktop

then, run create-react-app:
$ npx create-react-app MY_APP_NAME
$ cd MY_APP_NAME


now, try running a development server on computer:
$ npm start


you should be able to see your app running on http://localhost:3000


Go to App.js and replace it with this:

```
import React from 'react';

class App extends React.Component {
  render = () => {
    return (
      <div>
        I am app
      </div>
    )
  }
}

export default App
```
We've replaced that function component with this App class component. Function components are getting more popular but class components are the original gangsters.
render is one of React's "lifecycle" methods. It's one of the very special functions that React treats differently from other functions that you write.

Now, check your app, it's going to look a lot more basic than before.

From here, let's setup a constructor function that is initially called when your component tries to load on the browser.
In the constructor, we'll setup some "state" that keeps track of the joke's 'setup', and 'delivery'. At first, they are both initialized to the empty string:
```import React from 'react';

class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      setup: '',
      delivery: ''
    }
  }
  
  render = () => {
    return (
      <div>
        I am app
      </div>
    )
  }
}

export default App
```
Don't worry about the props passed into constructor and super(props) for now. It's just boilerplate code you have to write for every class component.
Cool, now lets render our joke on the screen by updating our render function:
```
render = () => {
  return (
    <div>
      This is the joke's setup: {this.state.setup}
      <br/>
      The is the joke's delivery: {this.state.delivery}
    </div>
  )
}
```

Back in the browser, you'll see that there's nothing rendered after "This is the joke's setup" or "This is the joke's delivery".
This is because the this.state.setup and this.state.delivery are empty strings right now. If you wanted to, you could initialize them to some other strings,
and they would show up on screen.

Great, now let's write a function that retries data from the jokes API:


```
class App extends React.Component {
  constructor(props) {
    ...
  }

  handleClick = () => {
    fetch('https://sv443.net/jokeapi/v2/joke/Any')
      .then(res => res.json())
      .then(data => console.log('data', data))
  }
  
  render = () => {
    ...
  }
}
```

This function makes a GET request to that URL, gets the response and turns it into json, then prints out that data in the browser console.
Don't worry about that fetch call for now, it's also boilerplate that you use everytime you want to call an API.
What's important is that you asked for a resource from that URL and you got back some data. 

Now, upon inspection of the data in your console (right click in Chrome > inspect > console tab), we realize the data has a "setup" and "delivery" data.
Ok, now, let's update our state with those data.

```
...

handleClick = () => {
  fetch('https://sv443.net/jokeapi/v2/joke/Any')
    .then(res => res.json())
    .then(data => {
      const setup = data.setup;
      const delivery = data.delivery;

      this.setState({setup: setup, delivery: delivery})
    })
}

...
```

Here, instead of printing out the data, we grabbed the piece of data that we want, "setup" and "delivery", and used the `this.setState` function given to us by React.


Awesome! So now we need to actually call `handleClick` to update the our state with the joke from the jokes API.

Let's expose a button which will when pressed will trigger the `handleClick`

```
...
render = () => {
  return (
    <div>
      This is the joke's setup: {this.state.setup}
      <br/>
      The is the joke's delivery: {this.state.delivery}
      <br/>
      <button onClick={this.handleClick}>Give me a new joke!</button>
    </div>
  )
}
...
```




