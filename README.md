# lime-academy
Lime Academy codes



## Tasks

### Intro to Blockchain

#### What is a transaction?

A transaction is a call to the distributed ledger that results in a change to the state of the ledger.

#### What is the approximate time of every Ethereum transaction?

It depends on a number of factors, mostly network congestion and gas fees you are willing to pay to incentivize miners to prioritize your transaction. With a standard fee, it should generally take a few seconds to a few minutes.

#### What is a node?

A node is one computer running the Ethereum software, receives and broadcasts changes to the ledger, tries to compute the next block, and generally takes part in the consensus process.


### Learning Solidity

#### What is delegatecall? Give example with code.

Delegatecall calls another contract's code, but instead of using the called contract's context, it uses the caller contract's context. In other words, it's as if the function called was part of the original contract, instead of in a different one.

Example, let's say we have these two contracts, A and B, and we call A's test function.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;



contract A {
    function test(address _contractB) external payable  returns(bytes memory) {
        (bool success, bytes memory data) = _contractB.delegatecall(
            abi.encodeWithSelector(B.log.selector)
        );
        
        if(success)
            return data;
    }
}

contract B {
    function log() external payable returns(address) {
        return msg.sender;
    }
}
```

Because we use delegatecall, msg.sender inside B's `log()` function will be that of the original caller, whereas if we used a simple call it would be the address of the A contract
