# DAO Member Guide for Verifying the deployment of BTCR contracts
The BTCR DAO code was audited by 3 independent audit firms, namely:
1. Chain Security [audit report](https://github.com/ChainSecurity/audits/raw/master/ChainSecurity_BTCR.pdf)
2. Solidified [audit report](https://github.com/solidified-platform/audits/blob/master/Audit%20Report%20-%20BTCR%20%5B11-19-2018%5D.pdf)
3. Coinspect [audit report](https://github.com/coinspect/publications/blob/master/BTCR2018v1113.pdf)

This manual explains how to verify that the deployed code matches the audited code, and how to verify the main contract configurations.
The verification process consist of two steps.
In the first we verify the smart contract code that resides in the github repository correspond to the three audits.
In the second step we verify the github repository smart contract code with the deployed smart contract code and that custodian BTC balance matches the total BTCR in supply.

## Verifying the github smart contract code
The BTCR smart contract repository is https://github.com/BTCr-Dev/bitcoin-token-smart-contracts/
and the relevant commit hash `0e8c140ab5fa006a17afa504dafcf311f56f81a2`.

As reported in the [first audit](https://github.com/ChainSecurity/audits/raw/master/ChainSecurity_BTCR.pdf), the review was done over commit hash `144311389d5c46af5999b7cceb2bad9fec2a9a5e`.
The audit does not cover the changes that follows the second and third audit, hence, comparing the two commits show some differences which the reader can see in [this comparison](https://github.com/BTCr-Dev/bitcoin-token-smart-contracts/compare/ae1fcd4..0e8c140a) (we note, that the reader should only care about changes in the solidity files).

The [second audit](https://github.com/solidified-platform/audits/blob/master/Audit%20Report%20-%20BTCR%20%5B11-19-2018%5D.pdf) was done on commit hash `099ac7be60205d04a342773ab4ee0a5d58dd1f3b` and on the changes that were introduced in commit hash `2f42b0ef95a4afa6d8f7cd41b0154a549379b852`.
[Comparison of the first hash](https://github.com/BTCr-Dev/bitcoin-token-smart-contracts/compare/099ac7b..0e8c140a) shows minor changes, and the [comparison of the second hash](https://github.com/BTCr-Dev/bitcoin-token-smart-contracts/compare/2f42b0ef..0e8c140a) show that the github code matches exactly to the audited one. 

The [third audit](https://github.com/coinspect/publications/blob/master/BTCR2018v1113.pdf) has yet to provide a commit hash.

## Verifying github code matches the deployed code and custodian BTC balance
We prepared a javascript code to compare the github code and the deployed one.
The deployed one is using two Ethereum public nodes, namely infura and MyCrypto public Ethereum nodes.
It locally compiles the smart contracts, compare their bytecode to the deployed contracts and print the current configuration.
The script can be executed as following:
```
git clone https://github.com/BTCr-Dev/bitcoin-token-smart-contracts.git
cd bitcoin-token-smart-contracts
git checkout 0e8c140ab5fa006a17afa504dafcf311f56f81a2
npm install
cd scripts
node verify.js
```

The DAO member should take special note to the following reports:
1. `btcr address` - should match the address of the token the member is familiar with.
2. `BTCR code ok`, `Controller code ok`, `Members code ok` and `Factory code ok`. These prints confirm that the deployed code matches the locally compiled code.
3. `Controller owner` and `members owner`. Both owners should be the BTCR DAO address.
4. `custodian` and `merchants`. These should match the list [here](https://github.com/BTCr-Dev/DAO/blob/master/README.md). And the reader should also verify the public announcement of the custodian and merchants to make sure the reported entity corresponds to the actual address.
5. `BTC in custody >= BTCR total supply, ok`. This print confirms that the custodian BTC balance matches BTCR total supply.
