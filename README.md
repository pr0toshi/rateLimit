# rateLimit
Limits asset outflows from contracts within customisable timeframes.

# Usage

Simply import, inherit and swap publicly accessible transfer fns (both ETH and tokens) with the transferL fn. When using transferL the params required are the destination address, the amount in raw value (1*10*18=1 ETH // 1*10****decimals f**** for tokens), as well as the token contract address (supply address(0) / 0x0000000000000000000000000000000000000000 for ETH).

You can also set the timeframe in which to track outflows and the % limit on the outflows relative to total balance with the updateLimits fn which takes the rateLimit as a uint16 (1 = 00.01% // 100 = 01% // basis points) and the timeLimit which is the previous n seconds (3600=1 hour) used to limit and track outflows within
Sample Contract

```
pragma solidity ^0.8;

import "./rateLimit.sol";

contract limitedContract is rateLimit{

address[] public depositors;
uint256[] public amountsETH;
uint256[] public amountsToken;
IERC20 public Token;

constructor(uint16 _rateLimit, uint256 _timeLimit) {
        updateLimits(_rateLimit, _timeLimit);
    }

function deposit(uint256 amount) public {
    //doStuff
}

    function withdrawETH(
        address to,
        uint256 idAuth,
        uint256 amount
    ) public {
        require(msg.sender==depositors[idAuth]);
        amountsETH[idAuth]-= amount;
    transferL(to,amount,address(0));
}

function withdrawToken(
        address to,
        uint256 idAuth,
        uint256 amount
    ) public {
        require(msg.sender==depositors[idAuth]);
        amountsToken[idAuth]-= amount;
    transferL(to,amount,address(Token));
}

}
```
