## Add Liquidity

#### Manage Pool Feature

---

`AMM.sol` let's create two new `state variables`.
`uint256 public token1Balance`
`uint256 public token2Balance`
We also want to be make a **constant** `state variable` because of `X * Y = K`
`uint256 public K`
This constant is what ultimately determines the price.

Now put these `state variables` to work in the `addLiquidity()`
And `K` needs to use that formula here:

```solidity
token1Balance += _token1Amount;
token2Balance += _token2Amount;
K = token1Balance * token2Balance;
```

- `K` is a constant, but only during trading

---

#### Testing AMM receives tokens

```javascript
expect(await token1.balanceOf(amm.address)).to.equal(amount)
expect(await token2.balanceOf(amm.address)).to.equal(amount)

expect(await amm.token1Balance()).to.equal(amount)
expect(await amm.token2Balance()).to.equal(amount)
```

---

#### Issuing Shares

`AMM.sol` add `uint256 public totalShares` **state variable**
This is for the amount of tokens in the **entire** contract.
Next we will create `mapping(address => uint256) public shares`.
This `mapping` is for each individual user.

This will now be used in `addLiquidity()`.
Also add `uint256 constant PRECISION = 10**18` `state var` to use in this function.

```solidity
uint256 share;

if(totalShares == 0) {
  share = 100 * PRECISION;
} else {
    uint256 share1 = (totalShares * _token1Amount) / token1Balance;
    uint256 share2 = (totalShares * _token2Amount) / token2Balance;
    require(
        (share1 / 10**3) == (share2 / 10**3),
        "must provide equal token amounts"
    );
    share = share1;
}

totalShares += share;
shares[msg.sender] += share;
```

- This will handle two cases.
- First, when the first time someone calls this function. This will be the `if()`
- Second, when everyone else does. This will be the `else`
- `share` is a var we create in this function. `shares` is the `mapping()`. So share will get added to `mapping`.
