## Navbar

---

`App.js`
Nothing really needs to happen here. Just import `navigation` and render it. No need to even pass props because Redux is taking care of this.
Each component has the same access level to the `store` (house analogy).

So `dispatch` adds to the redux `store`, but now we need to find a way to take things out of the `store`.
The answer is `useSelector()`.
It's just a way to select things from the `store`.

`Navigation.js`
Bring in `useSelector()`.
Inside the main function, create a variable that selects the account.
So now we will see the `address` in the UI, pulled directly from the store.
You need to first display the account with a ternary for how you want it displayed.
You will want to truncate this account for better presentation.
This can be done using the JS `slice()` method.
You slice out the first 5 indices, concatenate `...`, then concatenate indices 38 through 42.
Just a clever way to target what you want from a string.

### Button

The symbol is come from `Blockies`.
What you ideally want is a button to connect to the blockchain, not have it connect automatically.
Greg uses a bootstrap button here for improved styling.
Also creates a `connectHandler` helper function.
This helper function will use `loadAccount` that you import in.
You will also import in `useDispatch` and save it to a variable called `dispatch`.
Then use it like this inside the helper function `loadAccount(dispatch)`.
Greg also wants to bring in the balances here. So he calls `loadBalances` inside this same helper function (and imports it of course).
`loadBalances` gets numerous arguments. These all come from `loadBalances()` inside `interactions.js`.
`account` comes from the `loadAccount` that we simply store that into right above it.
`tokens` we create above as well, and this comes from the `store` that we pull in via `useSelector()`.
`balances` should now be populated inside the redux dev tools, but it comes up as `BigNumber`. Not very helpful. This is within `tokens`.
Inside `interactions.js`->`loadBalances()`, on the `dispatch`, you can call `ethers.utils.formatUnits`.
You format it to a string rather than a BigNumber.

`App.js`
We force a refresh when you switch accounts on MetaMask because the old account address will still show in the UI if you don't.

```js
// Fetch current network's chainId (e.g. hardhat: 31337, kovan: 42)
const chainId = await loadNetwork(provider, dispatch)

// Fetch current account from Metamask when changed
window.ethereum.on('accountsChanged', async () => {
  await loadAccount(dispatch)
})
```

`Navigation.js`
We want to have a dropdown of different networks to select. So a form is what we need.

```js
<Form.Select
  aria-label="Network Selector"
  value={config[chainId] ? `0x${chainId.toString(16)}` : `0`}
  onChange={networkHandler}
  style={{ maxWidth: '200px', marginRight: '20px' }}
>
  <option value="0" disabled>
    Select Network
  </option>
  <option value="0x7A69">Localhost</option>
  <option value="0x5">Goerli</option>
</Form.Select>
```

This points to `networkHandler()`, a helper function.

```js
const networkHandler = async (e) => {
  await window.ethereum.request({
    method: 'wallet_switchEthereumChain',
    params: [{ chainId: e.target.value }]
  })
}
```

This will lead to needing a `chainId` from the `store`.

```js
const chainId = useSelector((state) => state.provider.chainId)
```

So now you want to refresh the page again if the `chainId` has changed.

`App.js`

```js
// Reload page when network changes
window.ethereum.on('chainChanged', () => {
  window.location.reload()
})
```

`interactions.js`
We add `shares` which is what allows the `amm` portion.

`reducers/amm.js`
Now we add `sharesLoaded`. Which then needs to be imported to `interactions.js`.

`navigation.js`
`amm` can now be passed into `connectHandler` helper function.
