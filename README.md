This is a document to introduce you to React. 
We'll build a website that renders a random joke from a jokes API. We'll then deploy to Amazon Web Services



Ok, start a new react app using Create React App. This is the officially recommended way to start a hobby react project.
```
$ cd ~/Desktop
$ npx create-react-app MY_APP_NAME
$ cd MY_APP_NAME
```

now, try running a development server on your computer:
```$ npm start```


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

This is a class component. 
Try learning class component first, then function components.

render is one of React's "lifecycle" methods. React looks at render to see what to show on the screen.


Now, check your app, it's going to look a lot more basic than before.

From here, let's setup a constructor function that is initially called when your component tries to load on the browser.
In the constructor, we'll setup some "state" that keeps track of the joke's 'setup', and 'delivery'. At first, they are both initialized to the empty string:
```import React from 'react';

class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      setup: '',
      punchline: ''
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
      The is the joke's punchline: {this.state.punchline}
    </div>
  )
}
```

Back in the browser, you'll see that there's nothing rendered after "This is the joke's setup" or "This is the joke's delivery".
This is because the `this.state.setup` and `this.state.punchline` are empty strings right now. If you wanted to, you could initialize them to some other strings,
and they would show up on screen.

Great, now let's write a function that retrieves data from the jokes API:


```
class App extends React.Component {
  constructor(props) {
    ...
  }
  
  handleClick = () => {
    fetch('https://official-joke-api.appspot.com/random_joke')
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



Now, let's expose a button which will when pressed will trigger the `handleClick`

```
...
render = () => {
  return (
    <div>
      This is the joke's setup: {this.state.setup}
      <br/>
      The is the joke's delivery: {this.state.punchline}
      <br/>
      <button onClick={this.handleClick}>Give me a new joke!</button>
    </div>
  )
}
...
```


Now, upon inspection of the data in your console (right click in Chrome > inspect > console tab), we realize the data has a "setup" and "delivery" data.
Ok, now, let's update our state with those data.

```
...

handleClick = () => {
  fetch('https://official-joke-api.appspot.com/random_joke')
    .then(res => res.json())
    .then(data => {
      const setup = data.setup;
      const delivery = data.delivery;

      this.setState({setup: setup, punchline: punchline})
    })
}

...
```

Here, instead of printing out the data, we grabbed the piece of data that we want, "setup" and "delivery", and used the `this.setState` function given to us by React.


Awesome! So now when we click on the button, we call `handleClick` to update our state, and our screen updates with the joke we fetched.


Great! Now test your app a few times.


Now, let's deploy to s3. 
1) Create a new account AWS
2) Search for S3 and click on the link.
3) Click Create Bucket
4) Name your bucket, such as joke-website
5) Scroll down and uncheck block all public access
6) Check the box for "I acknowledge that the current settings might result in this bucket and the objects within becoming public."
7) Click Create Bucket
8) Click on the name of the bucket you just created.
9) Now, go back to your terminal and compile your react app into a production ready 'build' folder by running `$ npm run build`
10) Navigate into that newly created build folder in finder.
11) Drag all the items in that build folder into S3. (The AWS tab on your browser)
12) Go to the permissions tab of your bucket
13) Scroll down to bucket policy, click edit
14) paste in this policy:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": "arn:aws:s3:::joke-website/*"
        }
    ]
}
```
Make sure to replace joke-website with the name of your bucket if it is not that

15) Save Changes.
16) Now click on the properties tab
17) Scroll all the way down to static website hosting and click edit
18) Click enable, specify index.html as both the Index Document and Error Document
19) Save changes
20) Your website is hosted under the Static Website Hosting > Bucket website endpoint !

You can now share this link with all of your friends!



