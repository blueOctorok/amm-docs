## Add Liquidity

---

`AMM.sol`
Create a new function `addLiquiduty()`.
This will take two different token amounts.
This will be an `external` function, which means it can only be called from outside the `AMM contract`, not internally from other functions.

- We use `external` for **Gas Optimization**. `public` functions are more expensive to call. They don't copy `calldata` into memory unless needed.
- Prevents internal functions from accidentally calling it, ensuring only deliberate external actions. Reduces attacks.
- Design clarity. Signals `addLiquidity()` is an entry point for interaction with the contract, not a utility for internal logic.

Since this function is `external`, it reaches out to `Token.sol` for the `transferFrom()`, which is ERC-20.
Both `token1` & `token2` get their own `transferFrom()` call, and both require `msg.sender` (which represents the address of the person who called the function).

`AMM.js`
Make a `describe('Swapping Tokens')`.
In the `beforeEach()` above this, you need to set some things up.
More variables & send tokens to liquidity provider.
To send tokens, this will require the `transfer()` function. This will need to be done on both `token1` & `token2`.
What we are trying to do here is use the `transferFrom()` function to take tokens out of a wallet and into the smart contract, but we need to approve the tokens first to do that.
Then you need to chain some functions together to test them properly. `connect()`.`addLiquidity()` and they must get equal amounts of both tokens.
Then you need to tell the `transaction()` to `wait()`, (this might have changed in an update).
Then check to see if `AMM` received the tokens. `expect()` both `token1` & `token2` to `equal(amount)`.

`AMM.sol`
Our `addLiquidity()` function needs a bit more checking, so add a `require()`. This looks for an expression of true or false, and it can also provide a message.
The message is helpful because it can tell you where the error is happening. `transferFrom()` sort of provides this already, but this makes things a bit more clear.
What you're doing here is moving those `transferFrom()` statements into the `require()` for this added bonus.

**Managing the Pool**
Now we need to manage the token balances. These are the pools the DEX will hold.
It will use a formula of `X * Y = K`.
`K` is a constant that doesn't change, that we keep in our smart contract.
We need to figure out what `X` & `Y` is. So we will create some `state variables` to track this.
`uint256 public token1Balance` & `uint256 public token2Balance` & `uint256 public K`.
Now these will be used in the `addLiquidity()` function.
This is pretty simple and just looks like `token1Balance += _token1Amount`. Do the same for `token2`.
Then this is where our formula kicks in. `K = token1Balance * token2Balance`. `K` is a constant during trading, not during development.

`AMM.js`
Time to test for these inside `it('facilitates swaps')`.
`expect(await amm.token1Balance()).to.equal(amount)`. Same exact thing for `token2`.

**Issuing Shares**
`AMM.sol`
We need to calculate the shares of the pool. In this case 10%.
When someone calls `addLiquidity()`, we want to determine how many tokens they are deposting relative to all the tokens that exist in this pool already, and give them their share.
We start with another `state variable`.
`uint256 public totalShares`.
This will keep track of how many shares exist in the **entire** contract.
Then we will have a `mapping()` of how individual shares each person has.
`mapping(address => uint256) public shares`. `address` is the **key**, and `uint256` is the **value**/number of shares each person has.
Now we need to use this `mapping`, and of course that means it will go inside of `addLiquidity()`.
The first time someone adds liquidity, the `totalShares` will be 0 (the other `state variable`, not the `mapping`).
So you will make two cases. One for the first time and the second for every other time.
This will require an `if() else` statement, and you check if it's equal to zero.
Inside this function, you create a `local variable`, `uint256 share`, right above the `if()`.

```solidity
uint256 share;

if (totalShares == 0) {
  share = 100 * PRECISION;
} else {
  uint266 share1 = (totalShares * _token1Amount) / token1Balance;
  uint266 share2 = (totalShares * _token2Amount) / token2Balance;
  require(
    (share1 / 1 * 10**3) == (share2 / 1 * 10**3),
    'Must provide equal token amounts'
    );
  share = share1;
  }
```

Because you cannot use decimals in **Solidity**, and you don't want a million zeros, it's best to store this number inside yet another `state variable`.
You can also use math to get your result, but it's cleaner to not have this in your function.
`uint256 constant PRECISION  = 10**18`.
Now you have to increase the `totalShares` by the share amount.
`totalShares += share`.
Then the next time someone calls this function, the `totalShares` will no longer be zero, so we need to account for that logic below what we just wrote.
It must update the `mapping` to reflect how many `shares` the individual user has.
`shares[msg.sender] += share`. (Remember `msg.sender` is the person who called the function)

`AMM.js`
We want to check if deployer has 100 shares.
`expect(await amm.shares(deployer.address)).to.equal(tokens(100))`.
We have a tokens helper function at the top of the file for easier writing of tests.
Remember `shares` is a `mapping` that is `public`. This gives you a `reader` function for free, and the `deployer.address` is the **key** when you look back at the `state variable`.
You'll also want to check for 100 total shares.
`expect(await amm.totalShares()).to.equal(tokens(100))`.

`AMM.sol`
So if someone is adding liquidity again, we can now code out the `else` statement.
We need to figure out how many tokens they have deposited, then calculate how much we will give them based on the percentage (10%).
We will do this on a per token basis. The formula is `totalShares * token1Amount / token1Balance`. (As seen in code snippet above).
The complicated math for `share1` & `share2` is for really small decimal values and we are checking for equality, and it can sometimes be off by one way. This rounds them both out.

`AMM.js`
We need to have liquidity pool add more liquidity.
`token1.connect(liquidityProvider).approve(amm.address, amount)`
`token2.connect(liquidityProvider).approve(amm.address, amount)`
Then to actually add the liquidity.
`connect(liquidityProvider).addLiquidity(amount, amount)`
LP should have 50 shares.
`expect(await amm.shares(liquidityProvider.address)).to.equal(tokens(50))`
We still want to make sure the deployer still has 100 shares
`expect(await amm.shares(deployer.address)).to.equal(tokens(100))`
Now we have 150 total shares.
`expect(await amm.totalShares()).to.equal(tokens(150))`
47:09
