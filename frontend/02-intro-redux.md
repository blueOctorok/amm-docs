## Redux Intro

---

We need to take the data that we are storing on our blockchain and store it on the frontend.
`useState()` gets messy when you try to use it all across your app. You need a central place.
`Redux` can be thought of as a DB for your frontend app.
Think of it like a community of houses, and there is a house smack dab in the center.
This house in the center contains items that all the other houses need to run their daily lives.
Instead of random houses having the items they need and trying to find them, this house in the middle, everyone can go to easily due to its location, and they every collectively knows what they need will be there.

We will be using `redux toolkit`.
Seems like a more opinionated, batteries included, slimmed down version of `Redux`.

`Redux` has three main components.
`Actions`, `Reducer`, & `Store`.

- `Store` - is the front end DB. A key/value store. The actual state of the application. Redux dev tools displays this.
- `Reducer` - determines where information goes into the store.
- `Actions` - anytime something happens, this action dispatches it to the `reducer`. `Reducer` understands how to listen for any of these actions and what to do with it. Then it writes it to the `store`. `Actions` are also listed in the Redux dev tool.
