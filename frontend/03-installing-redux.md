## Installing Redux | Using and setting up Redux

---

Greg suggests grabbing the `package.json` from the completed project.

`App.js`
There lies a `loadBlockchainData()` function.
We will migration this to `redux` so we can see the `redux` way of doing it.
Going from `component` state to `redux` state.

Restart your blockchain.

```bash
npx hardhat node
```

Then run your deployment scripts.

```bash
npx hardhat run scripts/deploy.js --network localhost
```

Then the same thing for the seeding scripts.

```bash
npx hardhat run scripts/seed.js --network localhost
```

Then start your development server

```bash
npm run start
```

Make sure you have your `config.json` with your addresses.

`Metamask`
Make sure you're connected to `hardhat network`, on `hardhat#0`.
For good practive go to `settings->Advanced->Reset Account`. This is necessary when you restart your `hardhat blockchain` or you could get errors when you try to sign transactions.

Now we want to take this existing data and put it into our `store`.
We do NOT create `stores`, it's put into our browser by redux. We do create the `reducers` & `actions`.
We create a `reducer` that understands how to interpret `actions` and puts them in the `store`.

So lets first create a `reducer` & this will configure the `store`.

Create a new file called `provider.js`. Its path is `src/store/reducers/provider.js`.
Here we will create a special `reducer` for all the `provider` related actions, the `ethers.js provider`.
`Metamask` is injecting an Ethereum connection into the browser, we catch that with `ethers`.
This lets us speak with the blockchain.

We are going to use `createSlice`. This comes from the toolkit.
This creates that special reducer for our `provider`.

`provider.js`
Set up your `reducer` here. This is `JSON` looking key/value pairs you will see in the Redux Dev Tools.
End of this file you need a place to store all these actions.
You guessed it, its called `store.js`. Path is `src/store/store.js`.
This is what will configure the actual redux store.
You will use `configureStore` and this will just house & combine all the `reducers` you create.
So just make sure you put all your `reducers` in this file, from the `reducers` folder.
Greg doesn't explain the `middleware`, but says its recommended by redux to use. So use it!

`index.js`
When you import `{ Provider }`, it has nothing to do with the `provider` `createSlice` object you created in `provider.js`.
The store you create must be rendered using the `createRoot()`.
This `<Provider />` wraps `<App />`.
Then you bring the `store` in.
`store={store}` becomes a parameter of `<Provider />`.

You can now observe this in the browser.
The Redux Dev tools will be lit up and have the `provider` inside it.
The `store` and initial `reducer` are created. Now we need an `action`.
So we need to configure the `reducer` to handle this `action` so it can put the data intot the `store`.

`App.js`
We are going to remove the component state that handles the accounts, and have redux handle it.

`provider.js`
We are removing `setAccount useState` and moving it to `reducers` object.
It takes a function called `setAccount(state, action)`.
Its essentially a high order function where `action` is a function that fetches the data and updates the state.
You will see `state.account = action.payload`.
`createSlice` is going to create the actions for us because we have configured the actions inside of the `reducers` object.

`App.js`
You need to import `useDispatch`, because actions must be dispatched.
import `setAccount` from the `store`.
Remove the useState `[account, setAccount]` and replace it with `let account = 0x0...`
Create a `const dispatch = useDispatch()`.
Now `dispatch` needs to wrap `setAccount`.
`dispatch(setAccount(account))`.

`Redux Dev Tools`
You will now see data on the left side of Redux Dev Tools.
Evidence of an `action` was triggered.
On the right hand side, you will see what the reducer is doing.
Click the state tab and you can see the account.
Whats happening is when you said `state.account = action.payload`, you are making the `state` equal the `payload`.

`interactions.js`
This is a way to organize and clean up your project. It does complicate things though. The path is `src/store/interactions.js`.
Essentially we want to remove the `actions` from components and put them into a central repo.
So remove `setAccount` from `App.js` and import it here.
The logic that gets associated to `setAccount` gets removed as well and put here.
This really does help from letting your components get out of hand.
So the logic gets put into a function called `loadAccount`.
That must get imported into `App.js`.
Then when we go to use `loadAccount`, we give it `dispatch`.

```js
await loadAccount(dispatch)
```

We careated a

```js
const dispatch = useDispatch()
```

Right above and `loadAccount` receives that in the parens.
Because then inside `interactions.js` we can actually pass that to the `loadAccount` function as an argument.
We do the same thing then for `loadProvider`.
You also need to add the `setProvider` object to the `reducers` object.

`interactions.js`
We also create a `loadNetwork`.
This function takes both a `provider` & `dispatch`.
This is what will contain the `chainId`.

`provider.js`
This is where setNetwork gets created (`interactions.js` use this).

`App.js`
Now add

```js
const chainId = await loadNetwork(provider, dispatch)
```
