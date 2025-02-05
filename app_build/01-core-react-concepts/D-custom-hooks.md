---
description: "You can even make your own hooks! "
---

For now, we're going to make a custom hook of our own. Just like `useState` is a hook, there are a few others like `useEffect` (which we'll use in this lesson), `useReducer` (for doing Redux-like reducers), `useRefs` (for when you need to have programmatic access to a DOM node), and `useContext` (for using React's context which we'll do shortly as well.) But like React hooks, we can use these hooks to make our re-usable hooks.

We need a list of breeds based on which animal is selected. In general this would be nice to request _once_ and if a user returns later to the same animal, that we would have some cache of that. We could implement in the component (and in general I probably would, this is overengineering it for just one use) but let's make a custom hook for it.

Make a new file called `useBreedList.js` in src and put this in it.

> .js or .jsx here, doesn't matter. It doesn't technically need JSX but I'm also fine with the "I don't want to think about it" approach to it as well.

```javascript
import { useState, useEffect } from "react";

const localCache = {};

export default function useBreedList(animal) {
  const [breedList, setBreedList] = useState([]);
  const [status, setStatus] = useState("unloaded");

  useEffect(() => {
    if (!animal) {
      setBreedList([]);
    } else if (localCache[animal]) {
      setBreedList(localCache[animal]);
    } else {
      requestBreedList();
    }

    async function requestBreedList() {
      setBreedList([]);
      setStatus("loading");
      const res = await fetch(
        `http://pets-v2.dev-apis.com/breeds?animal=${animal}`
      );
      const json = await res.json();
      localCache[animal] = json.breeds || [];
      setBreedList(localCache[animal]);
      setStatus("loaded");
    }
  }, [animal]);

  return [breedList, status];
}
```

Head over to SearchParams.jsx and put this in there.

```javascript
import useBreedList from "./useBreedList";

// replace `const breeds = [];`
const [breeds] = useBreedList(animal);
```

That should be enough! Now you should have breeds being populated every time you change animal! (Do note we haven't implemented the submit button yet though.)

