---
description: "React manages view state through a mechanism called hooks. "
---

Now we want to make it so you can modify what your search parameters are. Let's make a new route called SearchParams.jsx and have it accept these search parameters.

```javascript
const SearchParams = () => {
  const location = "Seattle, WA";
  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          Location
          <input id="location" value={location} placeholder="Location" />
        </label>
        <button>Submit</button>
      </form>
    </div>
  );
};

export default SearchParams;
```

Now add it to your routes:

```javascript
// delete Pet import, and add SearchParams
import SearchParams from "./SearchParams";

// in App.jsx, replace all the Pets
<SearchParams />;
```

> 🚨 You'll have some errors in the console, that's okay.


```javascript
// in SearchParams.jsx
import { useState } from "react";

// replace location
const [location, setLocation] = useState("");

// replace input
<input
  id="location"
  value={location}
  placeholder="Location"
  onChange={(e) => setLocation(e.target.value)}
/>
```


> The order of extends isn't particularly important to us _except_ the Prettier one _must_ be last. That one serves to turn off rules the others ones enable.

Let's next make the animal drop down.

```javascript
// under the imports
const ANIMALS = ["bird", "cat", "dog", "rabbit", "reptile"];

// under location
const [animal, setAnimal] = useState("");

// under the location label
<label htmlFor="animal">
  Animal
  <select
    id="animal"
    value={animal}
    onChange={(e) => {
      setAnimal(e.target.value);
      setBreed("");
    }}
    onBlur={(e) => {
      setAnimal(e.target.value);
      setBreed("");
    }}
  >
    <option />
    {ANIMALS.map((animal) => (
      <option key={animal} value={animal}>
        {animal}
      </option>
    ))}
  </select>
</label>
```

Let's make a second dropdown so you can select a breed as well as an animal.

```javascript
// under your other state inside the component
  const [breed, setBreed] = useState("");
  const breeds = [];

// under the animal label
 <label htmlFor="breed">
          Breed
          <select
            disabled={!breeds.length}
            id="breed"
            value={breed}
            onChange={(e) => setBreed(e.target.value)}
            onBlur={(e) => setBreed(e.target.value)}
          >
            <option />
            {breeds.map((breed) => (
              <option key={breed} value={breed}>
                {breed}
              </option>
            ))}
          </select>
        </label>
```

