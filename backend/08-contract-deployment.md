## Contract Deployment

---

Now it's time to delopy this to the blockchain, but this will be done locally, using `hardhat`.

Run a test to make you didn't break anything upon development.

```bash
npx hardhat test
```

First you need the migration file to put it on the blockchain.
Second thing is you'll need a seed script.
Both of these files go into the `scripts` folder.

`deploy.js`
First you need to `deploy` both `tokens`.

```js
// Deploy Token 1
let dapp = await Token.deploy('Dapp Token', 'DAPP', '1000000') // 1 million tokens
await dapp.deployed()
console.log(`Dapp Token deployed to: ${dapp.address}\n`)

// Deploy Token 2
const usd = await Token.deploy('USD Token', 'USD', '1000000') // 1 million tokens
await usd.deployed()
console.log(`USD Token deployed to: ${usd.address}\n`)
```

Next you will need to deploy the `AMM`.

```js
// Deploy AMM
const AMM = await hre.ethers.getContractFactory('AMM')
const amm = await AMM.deploy(dapp.address, usd.address)

console.log(`AMM contract deployed to: ${amm.address}\n`)
```

Now start up your blockchain.

```bash
npx hardhat node
```

Then run the migration you just created.

```bash
npx hardhat run scripts/deploy.js --network localhost
```

You will not see.
`Dapp Token deployed` to an `address`.
`USD Token deployed` to another `address`.
`AMM contract deployed` to another `address`.

Now lets save the contracts to our config file.
`src/config.json`.
You need to copy the `addresses` you logged to the console into this file. Should look like this.

```json
{
  "31337": {
    "dapp": {
      "address": "0x5FbDB2315678afecb367f032d93F642f64180aa3"
    },
    "usd": {
      "address": "0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512"
    },
    "amm": {
      "address": "0x9fE46736679d2D9a65F0992F2272dE9f3c7fa6e0"
    }
  }
}
```

Next you take the `ABIs` and put them inside the `ABI` file.
Greg creates a new file called `AMM.json`, inside `src/abis/AMM.json`.
Then he goes to `artifacts/contracts/AMM.sol/AMM.json`, copies the `abi` array, pastes it into `src/abis/AMM.json`.
Note you will NOT have `artifacts/contracts/AMM.sol/AMM.json`, until you run your blockchain locally. It generates these folders/files.
