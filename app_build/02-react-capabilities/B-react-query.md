---
description: ""
title: "react-query"
---
Here I am going to teach you a library called [@tanstack/react-query][react-query].

```bash
npm install @tanstack/react-query@4.10.1
```

> `@tanstack/react-query` is the same people who do `react-query`. This is just the newer version. They now support more than just React.

The idea behind React is that you want to cache most of what you fetch from a database. If you fetch the details of pet ID 1, you generally do not want to fetch it again if a user revisits that page: you'd like to cache it. This is what react-query is going to do for you: it's a built in caching layer for these async data stores that works really well within the constraints of React. Let's see how to make our Details page use it.

First thing, we need to wrap our app in a query client. In theory we could have multiple query clients for our app if we wanted multiple different caches to draw from (though I struggle to conceive of a reason to do that.) More concretely though is that it's using React context to pass our app's cache around. So let's go handle that.

In App.jsx

```javascript
// at top
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: Infinity,
      cacheTime: Infinity,
    },
  },
});

// inside <BrowserRouter> wrapping everything inside it
<QueryClientProvider client={queryClient}>
 […]
</QueryClientProvider>
```

This will wrap our app with the provider necessary to power react-query. We have to give it cache and stale times so that it will actually use its caching layer. Otherwise it'll fetch each time. Here we're saying "never invalidate" but in many apps you'd probably want to still fetch every few minutes or so.

Now make a file called fetchPet.js (or jsx):

```javascript
const fetchPet = async ({ queryKey }) => {
  const id = queryKey[1];
  const apiRes = await fetch(`http://pets-v2.dev-apis.com/pets?id=${id}`);

  if (!apiRes.ok) {
    throw new Error(`details/${id} fetch not ok`);
  }

  return apiRes.json();
};

export default fetchPet;
```

This is the function that will actually make the request to the API.

- Splitting it out allows it to be independently testable and reusable across our app
- Notice the `if` conditional. We need it to throw if there's an error and fetch wouldn't throw here if there's a 400 or a 500 error. We need it to.
- Notice we don't await the `json` response. Any async function is going to return a promise anyway so we don't need to await it in the function body. You could. It would do the same thing.

Okay, let's change Details.jsx now

```javascript
import { useParams } from "react-router-dom";
import { useQuery } from "@tanstack/react-query";
import fetchPet from "./fetchPet";

const Details = () => {
  const { id } = useParams();
  const results = useQuery(["details", id], fetchPet);

  if (results.isLoading) {
    return (
      <div className="loading-pane">
        <h2 className="loader">🌀</h2>
      </div>
    );
  }

  const pet = results.data.pets[0];

  return (
    <div className="details">
      <div>
        <h1>{pet.name}</h1>
        <h2>{`${pet.animal} — ${pet.breed} — ${pet.city}, ${pet.state}`}</h2>
        <button>Adopt {pet.name}</button>
        <p>{pet.description}</p>
      </div>
    </div>
  );
};

export default Details;
```

- useQuery will actually use the queryClient that we instantiated above via context (we have a whole section later on context.)
- The first thing you give to useQuery is the query key. It could be a string e.g. we could have done `details:1` as the key for details 1 (similar to a Redis strategy for key naming.) However I like the array methodology. You can give it an array of keys. So the first key is `details` and then a subkey of that is `1` and it has to match both. You can also do it with objects and we will momentarily.
- The results object has a lot of booleans on it for isLoading, isError, isFetching, isPaused, etc. In this case react-query will make it _start_ its first fetch (but not finish) and then continue rendering. Therefore we _must_ handle the `isLoading` case (in addition to that just being a good idea)

This should all work now! Notice if we navigate back and forth from a page, the first time it will load it and the second time it won't; it'll just pull it from the cache! Perfect! Exactly what we wanted.

Let's go do useBreedList.js. First, let's split out the fetch command into fetchBreedList.js

```javascript
async function fetchBreedList({ queryKey }) {
  const animal = queryKey[1];

  if (!animal) return [];

  const res = await fetch(
    `http://pets-v2.dev-apis.com/breeds?animal=${animal}`
  );

  if (!res.ok) {
    throw new Error(`breeds ${animal} fetch not ok`);
  }

  return res.json();
}

export default fetchBreedList;
```

And now refactor useBreedList.js

```javascript
import { useQuery } from "@tanstack/react-query";
import fetchBreedList from "./fetchBreedList";

export default function useBreedList(animal) {
  const results = useQuery(["breeds", animal], fetchBreedList);

  return [results?.data?.breeds ?? [], results.status];
}
```

