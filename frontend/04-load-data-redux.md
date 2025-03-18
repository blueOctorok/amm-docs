## Loading Data into Redux

---

We were actually doing this in the last video, so should be too new here.

`interactions.js`
Here we are loading the contracts.
Important to understand here. Just because blockchain is the backend, doesn't mean we cannot feed this data directly into Redux.
This isn't like a typical fullstack app, where you run two separate projects (frontend & backend) in one over all file then have to link them together.
We also pull in the ABIs.
`loadTokens` function needs to handle both `dapp` & `usd` tokens.
We do NOT bring the tokens functions into the `provider.js`. Both `contracts` get their own `reducer` file.

`reducers/token.js`
You want a different `reducer` for **each type** of action.
Always good to separate files and don't let one get too large. Also they will have different `initial state`.
They will also contain different `reducers`.

`interactions.js`
The `reducers/token.js` token `reducers` get imported here all the same.
We create a `loadBalances`, but it does not go into `App.js`.
I assume it goes into `AMM` instead and `loadAMM` gets loaded in.

`Redux Tool Kit`
Data will not show until you call `loadTokens` in `App.js`.
Once you do that, `setContracts` will be there on the left.

`interactions.js`
Creates a `loadAMM` function to load that contract.

`reducers/amm.js`
Gets its own logic and `reducers`.
A much larger file because `AMM.sol` has a lot more going on.
It will have similar `state` & `actions`, but they don't match up 1:1.
You first just want to `setContract`.
