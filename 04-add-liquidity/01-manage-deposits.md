## Add Liquidity

#### Manage Deposits Feature

---

Start with adding a function for this.

```solidity
function addLiquidity(uint256 _token1Amount, uint256 _token2Amount) external {
  token1.transferFrom(msg.sender, address(this), _token1Amount);
  token2.transferFrom(msg.sender, address(this), _token2Amount);
}
```

- We make it `external` so other `contracts` can see it.
- It will `deposit` tokens.
- It will `issue shares`
- It will help `manage the pool`

#### Deposit

- `ERC-20` standard uses `transferFrom()` to pull money out of a wallet to add `liquidity`
- `transferFrom()` takes **three** arguments. `_from, _to, _value`
- `_from` is usually `msg.sender`. This is a **global utility** that tells you **who** is calling the function
- `_to` is the `AMM contract` in this scenario, and we get the address for this with `address(this)`
- `_value` in this case is `_token1Amount`
- `token2` has the same exact behavior. Just need to refrenece `token2` instead

---

#### Testing `transferFrom()`

```javascript
liquidityProvider = accounts[1]

describe('Swapping tokens', () => {
  it('facilitates swaps', async () => {})
})
```

Next we want to test for `transfering tokens`.
We need another person who is a `liquidity provider`
In a `describe('AMM')`, you'll have a new `let liquidityProvider`
Inside `beforeEach` `liquidityProvider = accounts[1]`

This person needs `tokens`.
In `Token.sol`, the `constructor` has `balanceOf[msg.sender] = totalSupply`.
We send all the tokens to the person deploying it.
Deployer has all the tokens, whenever the token goes to the blockchain.
Again inside `beforeEach` lives `deployer = accounts[0]`
Deployer is going to send to `liquidity provider`

```javascript
let transaction = await token1
  .connect(deployer)
  .transfer(liquidityProvider.address, tokens(100000))
await transaction.wait()

transaction = await token2
  .connect(deployer)
  .transfer(liquidityProvider.address, tokens(100000))
await transaction.wait()
```

Now we shift to `deployer` approving 100k tokens.
We use the `addLiquidity()`, with the `deployer` adding it.

```javascript
describe('Swapping tokens', () => {
  let amount, transaction, results

  it('facilitates swaps', async () => {
    amount = tokens(100000)
    transaction = await token1.connect(deployer).approve(amm.address, amount)
    await transaction.wait()

    transaction = await token2.connect(deployer).approve(amm.address, amount)
    await transaction.wait()

    transaction = await amm.connect(deployer).addLiquidity(amount, amount)
    await transaction.wait()

    expect(await token1.balanceOf(amm.address)).to.equal(amount)
    expect(await token2.balanceOf(amm.address)).to.equal(amount)
  })
})
```

- First thing to check for is to make sure the tokens were actually deposited.
- `addLiquidity()` has the `transferFrom()` embedded in it.
- So when that gets called, it's going to move the tokens into the account.

So now check `AMM` receives tokens:

```javascript
expect(await token1.balanceOf(amm.address)).to.equal(amount)
expect(await token2.balanceOf(amm.address)).to.equal(amount)
```

- This should now pass tests and be passing tokens into `AMM smart contract`

---

#### Adding Requirements

In `AMM.sol`.
`addLiquidity()` should get a `require()`.
It should get 2 arguments. `bool` & `message`.
If it evaluates to `true`, the `require` will run, `false`, it will show the `message`.

```solidity
 require(token1.transferFrom(msg.sender, address(this), _token1Amount),"failed to transfer token 1");
```

- `transferFrom()` will give you this behavior automatically (from `Token.sol`), but this will give a very clear message and see exactly in your code where it went wrong.

We do the same for `token2` inside `addLiquidity()`:

```solidity
 require(token2.transferFrom(msg.sender, address(this), _token2Amount),"failed to transfer token 2");
```
