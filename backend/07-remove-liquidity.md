## Remove Liquidity

---

`AMM.sol`

So we want to do the opposite of `addLiquidity()`, we want to remove it.
In this situation we are going to act as LP and we are going to take it all out.
So we create a `removeLiquidity()` function.

You have to do some math to figure out how much you'll get when you do this stuff.
`token1` & `token2` both have balances already.
The LP Pool share = 50/150.

So that calculation ends up being.

```js
token1Amount = (_share * token1Balance) / totalShares
token2Amount = (_share * token2Balance) / totalShares
```

Instead of this math going into `removeLiquidity()`, it gets moved to its own helper function `calculateWithdrawAmount()`.
It really just to keep the `removeLiquidity()` more concise at a glance.
`calculateWithdrawAmount()` gets a `require`, so they cannot remove more than the pool has.
`removeLiquidity()` gets a `require` as well, but directed towards the person who is calling the function `msg.sender`.
Now `removeLiquidity()` gets the busniess logic for the actual removal of tokens.
We need to deduct their shares from the `mapping`.
Then right after, deduct the `totalShares`.
Then change the balances to change the state of the smart contract.
So you minus the balance from the amount for both token balances.
Then we need to update the `K` value. Again not a **constant** during development, but it is when in production.
`K = token1Balance * token2Balance`.
Then transfer tokens back to the user.
No need for `transferFrom()`. The smart contract is the sender here, because we have tokens in the smart contract. So `transfer()` will work just fine.

`AMM.js`
\*Check LP balance before removing tokens.
\*LP removes tokens from AMM pool. This is removing 50 shares, not 50 tokens. Shares use the PRECISION helper similar to the `tokens` helper at the top of the `AMM.js` file.
After running these tests, the console should log the differences.
\*LP should have 0 shares.
\*Deployer should have 100 shares.
\*AMM Pool has 100 total shares.
