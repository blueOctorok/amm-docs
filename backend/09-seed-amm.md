## Seed AMM

---

You need data in your app for the front end to work with. Every time you close down the app, the blockchain shuts down (locally), and you would need to populate it all over again.
So we can create a seeding script to run every time we boot our project back up.
This will borrow heavily from `AMM.js` tests. Lots of the same data you can copy/paste over.
You will make a new file with this path `scripts/seed.js`.

`seed.js`
We need to import the config files.

```js
const config = require('../src/config.json')
```

We also bring in the helpers, like `tokens` helper function.

```js
const tokens = (n) => {
  return ethers.utils.parseUnits(n.toString(), 'ether')
}
```

Next we need to fetch some accounts. This will be the first thing that goes into the `main` function.
When you ran `npx hardhat node`, you were given 20 different accounts with an account **Address** & **Private Key**.
`getSigners()` returns all the accounts from `hardhat`.
So we create variables here to assing various accounts with the different addresses \* keys.

```js
const accounts = await ethers.getSigners()
const deployer = accounts[0]
const investor1 = accounts[1]
const investor2 = accounts[2]
const investor3 = accounts[3]
const investor4 = accounts[4]
```

Now you gotta get the token. Which would be tied to the chain ID.
We are running it locally, so `31337` in `config.json` will be set. This needs to change when you want to run it on an actual blockchain.

```js
const { chainId } = await ethers.provider.getNetwork()
```

Now get the token. You must use `getContractAt()` then drill throw the `json` in `config.json`.
Should look like `[chainID].dapp.address`.
Which is `31337->dapp->address` in the `config.json`
Do the samething for `USD` token.

Makure you blockchain is running.

```bash
npx hardhat node
```

Make sure the deployment script has run.

```bash
npx hardhat run scripts/deploy.js --network localhost
```

Now it's time to seed the project with your `accounts` & `tokens`.

```bash
npx hardhat run scripts/seed.js --network localhost
```

Verify the address match from the `config.json` to the console logs.

Now you will want to
\*Distribute Tokens to Investors.
\*Send dapp tokens to investor 1.
\*Send usd tokens to investor 2.
\*Send dapp tokens to investor 3.
\*Send usd tokens to investor 4.
These all look the same, just change the number.

Now we need to add some liquidity.
You need to make a variable for the tokens and its amount. Then
\*Fetch AMM.
Then have deployer approve both token amounts.
\*Deployer adds liquidity.
This actually adds the liquidity.

Now lets do some swaps do build out our pricing history.

Do this first as investor 1.
\*Investor approves all tokens.
\*Investor swaps 1 token.

Do the same for investor 2, 3 & 4.
You can use different amounts just to make things a little different and more clear who is doing what exactly.
