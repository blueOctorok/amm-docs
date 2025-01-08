## Create AMM

---

We will be using the same `Token.sol` `contract` that we used in previous projects.
We will now create a new `contract`. `AMM.sol`.

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

import 'hardhat/console.sol';

contract AMM {

}
```

This will of course receive a companion test `AMM.js`.
First order of business is to create a **smoke test**
This `AMM contract` should be the entire cryptocurrency exchange.
It will be like **Uniswap**, but with only one trading pair.
It will use `DApp token` and a mimic **USDC token**.

---

```javascript
const { expect } = require('chai')
const { ethers } = require('hardhat')

const token = (n) => {
  return ethers.utils.parseUnits(n.toString(), 'ether')
}

const ether = tokens

describe('AMM', () => {
  let accounts, deployer, token1, token2, amm

  beforeEach(async () => {
    accounts = await ethers.getSigners()
    deployer = accounts[0]

    const Token = await ethers.getContractFactory('Token')
    token1 = await Token.deploy('Dapp University', 'DAPP', '1000000')
    token2 = await Token.deploy('USD Token', 'USD', '1000000')

    const AMM = await ethers.getContractFactory('AMM')
    amm = await AMM.deploy()
  })

  describe('Deployment', () => {
    it('has an address', async () => {
      expect(amm.address).to.not.equal(0x0)
    })
  })
})
```

- Need variables of `accounts, deployer, token1, token2, amm`.
- Need to `getContractFactory` the `Token & AMM contracts` to the blockchain.
- Have two `tokens` because you need a **pair** for an exchange to swap.
- We then `deploy` these tokens with a **name**, **ticker**, and set the **supply**.
- The `describe` here is just checking for the presence of an `address`.
