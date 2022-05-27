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

#### What is multicall? Give example with code.

Multicall is when we aggregate two or more separate calls to a contract's function, inside one function call, so that we can make sure those happen sequentially inside the same block, without any delay in between.

Based on the previous example, I've changed the call to delegatecall into 2 different calls to staticcall. As shown:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract A {
    function test(address _contractB) external payable  returns(bytes[] memory) {
        (bool success1, bytes memory data1) = _contractB.staticcall(
            abi.encodeWithSelector(B.log.selector)
        );
        

        (bool success2, bytes memory data2) = _contractB.staticcall(
            abi.encodeWithSelector(B.log.selector)
        );

        bytes[] memory retVal = new bytes[](2);

        if(success1 && success2){
            retVal[0] = data1;
            retVal[1] = data2;
        }
        return retVal;
    }
}

contract B {
    function log() external payable returns(address) {
        return msg.sender;
    }
}
```

What this returns this time is `0x0000000000000000000000009d7f74d0c41e726ec95884e0e97fa6129e3b5e99,0x0000000000000000000000009d7f74d0c41e726ec95884e0e97fa6129e3b5e99`, which is a bytes array of size 2, with the address of the calling contract.

#### What is time lock? Give example with code.

