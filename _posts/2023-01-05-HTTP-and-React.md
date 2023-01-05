---
title: HTTP and React
date: 2023-01-05
categories: [React]
tags: [react, http] # TAG names should always be lowercase

<author_id>:
  name: Benjamín Zambelli
  twitter: BenJokek
  url: this

img_path: /img/
---

## HTTP and React

When building web apps you more often than not have to reach out to the server to fetch some data o send some data based on user interaction. But React is a library to build UI and isn't concerned about HTTP. So, how do we make AJAX requests in React? How do we make API calls in React?

React just read props & state and render the UI therefore to use some data from the server you just have to get the data into your components props or state.

This is handled by a HTTP Library, in this case Axios is gonna be used. The fetch API is also a good consideration as it's more standardized but I'll stick to Axios.

Install the Axios package, make sure you're inside the project folder an then run:

```bash
npm install axios
```

### HTTP GET Request

Let's learn how to make a GET request using Axios and render the fetch data in our React component.

To fetch the data we need to have an API endpoint, for that I'll be making use of [JSONPlaceholder](!https://jsonplaceholder.typicode.com/) (free fake API for testing and prototyping).

If you scroll down to the routes section you can see the possible HTTP requests you can make. I'll be making a GET request to "/posts" which will fetch an array of posts to display in the UI.

* Steps:
1. Import Axios Library.
2. Create a state property which will store the list of posts.
3. Use Axios to make a GET request to the JSONPlaceholder API.

* Where do we place the code to make the GET request?

In the componentDidMount lifecycle method. This component will be executed when the component mounts for the first time and is only executed once during component lifetime.

```react
//PostList.js
import { Component } from "react";
import axios from "axios"; //step 1

class PostList extends Component {
  constructor(props) {
    super(props);

    this.state = {
      posts: [],
      errorMessage: "",
    };
  }

  componentDidMount() {
    axios
      .get("https://jsonplaceholder.typicode.com/posts")
      .then((response) => {
        console.log(response);
        //Extend the Object we received in the console and
        // then expand data to see the posts
        this.setState({ posts: response.data });
      })
      .catch((error) => {
        console.log(error);
        this.setState({ errorMessage: "Error retreiving data" });
      });
  }

  render() {
    const { posts, errorMessage } = this.state;
    return (
      <div>
        <h1>List of post titles:</h1>
        <ul>
          {posts.length
            ? posts.map((post) => <li key={post.id}>{post.title}</li>)
            : errorMessage}
            {/*try entering a wrong URL in get() to see the error*/}
        </ul>
      </div>
    );
  }
}

export default PostList;
```

### HTTP POST Request

Continuing, the "id" is self incrementing and doesn't have to be send as part of the POST request. So what we're going to do is create three input fields one each for "userId", "title" and "body". We'll store the data in the state object and make a post request on click of the submit button.

```react
//PostForm.js
import { Component } from "react";
import axios from "axios";

class PostForm extends Component {
  constructor(props) {
    super(props);

    this.state = {
      userId: "",
      title: "",
      body: "",
    };
  }

  changeHandler = (event) => {
    this.setState({ [event.target.name]: event.target.value });
  };

  submitHandler = (event) => {
    event.preventDefault();
    console.log(this.state);
    axios
      .post("https://jsonplaceholder.typicode.com/posts", this.state)
      .then((response) => {
        console.log(response);
      })
      .catch((error) => {
        console.log(error);
      });
  };

  render() {
    const { userId, title, body } = this.state;
    return (
      <div>
        <form onSubmit={this.submitHandler}>
          <div>
            <input
              type="text"
              name="userId"
              value={userId}
              onChange={this.changeHandler}
            />
          </div>
          <div>
            <input
              type="text"
              name="title"
              value={title}
              onChange={this.changeHandler}
            />
          </div>
          <div>
            <input
              type="text"
              name="body"
              value={body}
              onChange={this.changeHandler}
            />
          </div>
          <button type="submit">SUBMIT</button>
        </form>
      </div>
    );
  }
}

export default PostForm;
```
Posting data generally requires form validation and so on and you might take a look at a library [Formik](!https://formik.org/) which helps you with forms in React.

Making put or delete requests is pretty much the same.
