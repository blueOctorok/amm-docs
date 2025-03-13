## Swapping Tokens

---

`AMM.sol`
Create a function for each swap. This will actually consist of four functions.
`calculateToken1Swap` & `swapToken1` (and same for `token2`).
This function will calculate `token2` amount, do the swap, and `emit` an event.
`K` will not change when we do the trade. This is the **constant**.
We are trying to calculate how many tokens will be in the pool **after** we trade.
Slippage comes into play here. When you swap a large amount of tokens, the price will go down.

So we are trying to figure out how to configure `swapToken1` here.
To keep this function a bit cleaner we move some calculation logic into a helper function. `calculateToken1Swap`.
`calculateToken1Swap` is `public` because we want to call from within & outside the app. The UI will have knowledge of it.
We also must protect it from going to zero (someone swapping all the tokens).
Also must protect against swapping from more that exist in the pool. `require` will revert the transaction.

```solidity
// Don't let the pool go to 0
        if (token2Amount == token2Balance) {
            token2Amount--;
        }

        require(token2Amount < token2Balance, "swap amount too large");
```

- Transfer `token1` out of user wallet to contract
- Update the `token1` balance in the contract
- Update the `token2` balance in the contract
- Transfer `token2` from contract to wallet

`transfer()` is from the user who is calling this. We cannot use this because the tokens are in the user wallet already. We need `transferFrom()` because this contains `_from`, `_to`, and `_value` parameters.
Then we can update `token1` by saying `token1Balance += _token1Amount`
Then we can update `token2` by saying `token2Balance -= _token2Amount`
Because we don't need to approve this next transfer, we can use `token2.transfer(msg.sender, token2Amount)`

`AMM.js`
We now need to add some tests to our

```js
it('facilitates swaps', async () => {
```

You will need to add some variables the main `describe('AMM')` & inside the `beforeEach()` set up some new `accounts`.
We will add an `investors` section.
Here `investor1` sends the token for the swap & `investor2` does it in the opposite direction.

```js
transaction = await token1
  .connect(deployer)
  .transfer(investor1.address, tokens(100000))
await transaction.wait()

transaction = await token2
  .connect(deployer)
  .transfer(investor2.address, tokens(100000))
await transaction.wait()
```

Normally you don't want to approve all the tokens for swapping, just the amount you need. But for testing purposes we will go ahead and approve them all.

```js
// Investor1 approves all tokens
transaction = await token1
  .connect(investor1)
  .approve(amm.address, tokens(100000))
await transaction.wait()
```

From here we will

- Check investor1 balance before swap
- Estimate amount of tokens investor1 will receive after swapping token1: include slippage
- Investor1 swaps 1 token1
- Check investor1 balance after swap, make sure it's the same. The console log will show this.

Next - Check AMM token balances are in sync
What we are doing is making sure the `state variables`, `token1Balance` & `token2Balance` are the same as what the token smart contract says.
Because we are manipulating these values with calculations, sending tokens around to accounts, they can get out of sync.
Smart contracts hold the tokens but also cache the values.

`AMM.sol`

Now we will create an `event`.
We do this because `event` logs details of a token swap, emitted when a user exchanges one token for another.
`user`: The address of the person initiating the swap.
`tokenGive`: The token they’re giving up.
`tokenGiveAmount`: How much of that token they gave.
`tokenGet`: The token they’re receiving.
`tokenGetAmount`: How much of that token they got.
`token1Balance`: The updated balance of the first token in the pool after the swap.
`token2Balance`: The updated balance of the second token in the pool after the swap.
`timestamp`: When the swap happened (e.g., block.timestamp).

Provides analytics without querying state

Then you want to `emit` the `event`.
`emit` means to trigger an event, logging its data to the blockchains transaction logs. You are broadcasting something happened, with details you specify, so external systems can react or record it.

`AMM.js`
\*Check swap event.
You can test for the exact arguments you think it should return, and these will match the `event` arguments.

So we will test for that to observe the change by logging it to the console.
\*Check price before swapping.
\*Check price after swapping.
The logs will show the price will go down.
\*Swap some more tokens to see what happens.
*Estimate amount of tokens investor1 will receive after swapping token1: includes slippage.
*Investor swaps 1 token.
\*Check investor1 balance after swap.
Prices keep going down because you are dividing `token1Balance` by `token2Balance` every time you swap.

Now lets discuss how big of a price impact occurs when you do a large swap. This causes a bigger shift in the pool's balance.
\*Check investor balance before swap.
The tests will remain 99% the same, just changing the values swapped from `1` to `100`.

So we did `facilitate swaps`, but only one way, so now its time to do it in reverse order.

`AMM.sol`
All the logic for this swap of `token2` to `token1` remains the same, just reverse it.

`AMM.js`
But now to swap `token2`, we need to do this from the perspective of a second investor.
Where you establish variables, you will see `investor1` & `investor2`.
`investor1` owns `token1` and swaps for `token2`.
`investor2` owns `token2` and swaps for `token1`.
Again, same logic, just reverse it.
\*Investor2 approves all tokens.
\*Check investor2 balance before swap.
\*Estimate amount of tokens investor2 will receive after swapping token2: includes slippage.
\*Investor2 swaps 1 token.
\*Check swap event.
\*Check investor2 balance after swap.
Greg checks - *Check AMM token balances are in sync & *Check price after swapping in the video, but it's not in the final code. (only for investor 1 in final code)
