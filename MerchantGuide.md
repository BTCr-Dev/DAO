# Merchant Guide for Minting and Burning BTCR
This manual assumes basic knowledge with Ethereum blockchain and in particular knowledge with how to make a contract call for an Ethereum smart contract.

## Initial setup
Before starting to interact with the BTCR system, the merchant should be aware of two important smart contract addresses, namely the *BTCR token* and the *Factory* contracts.
These addresses can be found [here](https://github.com/BTCr-Dev/DAO#btcr-important-addresses).
All the function calls we describe in this guide are done to the factory smart contract (with the exception of the BTCR allowance call, which is sent to the BTCR Token contract)

The first step a merchant should do after they are approved by the DAO is to set up their BTC deposit address.
When the merchant burns BTCR, the custodian will send BTC to this deposit address.
The merchant sets up this address by calling:
```
setMerchantBtcDepositAddress(string btcDepositAddress)
```
The merchant can read the address they set by calling
```
merchantBtcDepositAddress(address merchant)
```


## Minting BTCR
Minting is done by sending BTC to the custodian deposit address and by submitting a minting request.


### Reading custodian deposit address
The merchant can read the custodian deposit address by calling
```
custodianBtcDepositAddress(address merchant)
```
It should be noted that this address is unique per merchant and merchant should only send their BTC to this address.

### Sending BTC
The custodian will take fees from the mint operation. The exact fee percentage is determined by an off-chain agreement with the custodian.
The amount of BTCR that is expected to be minted after sending `X` BTC to the custodian deposit address is
```
(X * (100 - fee))/100
```
Where `fee` is percent unit. E.g., 0.2% is denoted by `fee = 0.2`.
The merchant should send `X` that corresponds to the amount of BTCR they wish to mint.

### Submitting a mint request
After sending BTC to the custodian deposit address the merchant submits an on-chain minting request by calling:
```
    function addMintRequest(
        uint amount,
        string btcTxid,
        string btcDepositAddress
    )
```
Where:
1. `amount` is the amount of BTCR token to mint *in Satoshi units*. i.e., `100000000` (`1e8`) denotes 1 BTCR. If `X` BTC Satoshi were sent, then `amount` should be set to `(X * (100 - fee))/100`.
2. `btcTxid` the txhash of the BTC transfer to the custodian address. The hash should *not* contain the `0x` prefix.
3. `btcDepositAddress` the destination address of the BTC transfer. It must be equal to the custodian deposit address.

### Mint completion
The mint is completed after the custodian on-chain approves the request, and the minted BTCR tokens are sent to the merchant's ethereum address.

### Mint cancellation
Prior to the confirmation of the custodian the merchant is entitled to cancel their mint request.
This should happen, e.g., if the merchant realizes that the input parameters given to `addMintRequest` were wrong.
It should be noted that the cancellation does not revert the BTC transfer, and it should be sent back by the custodian.

## Burning BTCR
Burning BTCR is done by giving BTCR allowance to the factory contract and submitting a burn request on-chain.
It should be noted that in order to burn BTCR, the merchant must hold them in the merchant Ethereum account. 

### BTCR allowance
BTCR is a standard ERC20 token and as such the factory contract can accept it from an address only if the merchant approved it.
This is done by calling
```
approve(address _spender, uint256 _value)
```
function in the *BTCR contract* where
1. `_spender` should be set to the Factory contract address.
2. `_value` should be set to the amount of BTCR the merchant wish to burn (in Satoshi).
This is the only transaction in this document that is sent to the BTCR Token contract address. The rest are sent to the Factory contract address.

### Burn request submission
Done by calling
```burn(uint amount)```
where the amount is in Satoshi.

When calling this function the merchant BTCR will be burned, and the custodian will later send equivalent amount of BTC (minus applicable fees) to the merchant BTC deposit address (that was set in the initial setup).

***DO NOT call `burn` in BTCR token contract, you will lose your btcr forever***
