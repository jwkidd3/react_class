---
description: " wire up your app to work with users interacting with the site."
---

We've seen one way to handle async code in React: with effects. This is most useful when you need to be reactive to your data changing or when you're setting up or tearing down a component. Sometimes you just need to respond to someone pressing a button. This isn't hard to accomplish either. Let's make it so whenever someone either hits enter or clicks the button it searches for animals. We can do this by listening for submit events on the form. Let's go do that now. In SearchParams.js:

```javascript
// replace <form>
<form
  onSubmit={e => {
    e.preventDefault();
    requestPets();
  }}
>
```
