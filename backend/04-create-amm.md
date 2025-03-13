## Create AMM

---

You will use the old `Token.sol` from the other projects. Put this in your `contracts` folder.
Now create a new file in `contracts`. `AMM.sol`. Copy the boilerplate, also a new `AMM.js` for testing.

```solidity
pragma solidity ^0.8.0;

import 'hardhat/console.sol';

contract AMM {}
```

The whole point of this project is it's a mini DEX. We are only doing one trading pair.

`AMM.js`
You need to keep track of two tokens. So you will need to make a variable for `token1` & `token2`.
`describe('AMM')`
You will have `const Token = await ethers.getContractFactory`.
`token1` & `token2` will both get `deploy()`, with different names, ticker, and amounts.
You will also need to do the same with `AMM contract`.
Call the `getContractFactory` for it & `deploy()`.
In the `describe('Deployment')` (this describe is within the `describe('AMM')`).
You will be checking if `AMM` has an `address`.

**Run a basic smoke test**

```bash
npx hardhat test test/AMM.js
```

`AMM.sol`
Import `Token.sol`.
Now you need two `state variables`.
`Token public token1` & `Token public token2`
You now need to set these in the `constructor`.

```solidity
constructor(Token _token1, Token _token2) {
  token1 = _token1;
  token2 = _token2;
}
```

`AMM.js`
Now that the `state variables` & `constructor` is built they can be tested.
Goes inside `deploy(token1.address, token2.address)`.
Then inside your `describe('Deployment')`.
`it('returns token1')` (do the same for token2).
