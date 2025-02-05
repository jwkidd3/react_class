---
description: "react-router is phenomenal tool is that allows you to manage browser navigation state in a very React way."
---

React Router is by far the most popular client side router in the React community. It is mature, being used by big companies, and battle tested at large scales. It also has a lot of really cool capabilities, some of which we'll examine here.

What we want to do now is to add a second page to our application: a Details page where you can out more about each animal.

Let's quickly make a second page so we can switch between the two. Make a file called Details.jsx.

```javascript
const Details = () => {
  return <h2>hi!</h2>;
};

export default Details;
```

Now the Results page is its own component. This makes it easy to bring in the router to be able to switch pages. Run `npm install react-router-dom@6.4.1`.

Now we have two pages and the router available. Let's go make it ready to switch between the two. In `App.jsx`:

```javascript
// at top
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Details from "./Details";

// replace <SearchParams /> and <h1>Adopt Me!</h1>
<BrowserRouter>
  <h1>Adopt Me!</h1>
  <Routes>
    <Route path="/details/:id" element={<Details />} />
    <Route path="/" element={<SearchParams />} />
  </Routes>
</BrowserRouter>
```


So now let's make the two pages link to each other. Go to Pet.jsx.

```javascript
// at top
import { Link } from "react-router-dom";

// change wrapping <a>
<Link to={`/details/${id}`} className="pet">
  […]
</Link>
```

Why did we change this? Didn't the `<a>` work? It did but with a flaw: every link you clicked would end up in the browser navigating to a whole new page which means React would totally reload your entire app all over again. With `<Link>` it can intercept this and just handle that all client-side. Much faster and a better user experience.

Now each result is a link to a details page! And that id is being passed as a prop to Details. Try replacing the return of Details with:

```javascript
import { useParams } from "react-router-dom";

const Details = () => {
  const { id } = useParams();
  return <h2>{id}</h2>;
};

export default Details;
```

The `useParams` hook is how you get params from React Router. It used to be through the props but now they prefer this API.

Let's make the Adopt Me! header clickable too in App.jsx:

```javascript
// import Link too
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

// replace h1
<header>
  <Link to="/">Adopt Me!</Link>
</header>
```

> If you're getting a useHref error, make sure your `header` is _inside_ `<BrowserRouter>`

Now if you click the header, it'll take you back to the Results page. Cool. Now let's round out the Details page.
