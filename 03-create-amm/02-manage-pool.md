## Create AMM

#### Manage Pool

---

To manage the pools, we need to be aware of the two tokens we deployed. `DAPP & USD`.
We need to `import './Token.sol'` into the `AMM contract`.
So we need to store these two in the `AMM` itself as `state variables`.
So `Token public token1` & `Token public token2`.
Now we must set the **variables** inside the `constructor`.

```solidity
contract AMM {
  Token public token1;
  Token public token2;

  constructor(Token _token1, Token token2) {
    token1 = _token1;
    token2 = _token2;
  }
}
```

`_` is just a naming convention used to tell the difference between the `constructor variable` & `state variable`.

Now go to `AMM.js`

```javascript
describe('AMM', () => {
  let accounts, deployer, token1, token2, amm

  beforeEach(async () => {
    accounts = await ethers.getSigners()
    deployer = accounts[0]

    const Token = await ethers.getContractFactory('Token')
    token1 = await Token.deploy('Dapp University', 'DAPP', '1000000')
    token2 = await Token.deploy('USD Token', 'USD', '1000000')

    const AMM = await ethers.getContractFactory('AMM')
    amm = await AMM.deploy(token1.address, token2.address)
  })

  describe('Deployment', () => {
    it('has an address', async () => {
      expect(amm.address).to.not.equal(0x0)
    })

    it('returns token1 address', async () => {
      expect(await amm.token1()).to.equal(token1.address)
    })

    it('returns token2 address', async () => {
      expect(await amm.token2()).to.equal(token2.address)
    })
  })
})
```

- Here we are just showing that we will be testing what gets passed into the `constructor`.
- Arguments of `token1.address & token2.address`.
- In the `describe` we are `expect` `token1` `to.equal` `token1.address`.
- Because `token1` is `public`, we can call it and it will return the `address`.
- Same rules apply for `token2`.
