# rateLimit
## An EIP 5075 implementation resource

Limits asset outflows from contracts within customisable timeframes.
rateLimit = amount in basis points relative to totalasseets in the contract for that token type to allow to leave the contract within t timeLimit.
timeLimit = time in seconds to use as the window in which to track outflows to limit within. 3600 restricts outflows to 00.01% * rateLimit within the last 1 hour.

# Motivation
This contract was designed to stop single transactions or small bursts to steal funds from contracts through hacks. By changing all public withdraw and transfer fns to a simple new fn that tracks outflow rates, hacks can be limited in severity giving teams the time to freeze contract outflows and upgrade.
# Usage

Simply import, inherit and swap publicly accessible transfer fns (both ETH and tokens) with the transferL fn. When using transferL the params required are the destination address, the amount in raw value (1*10 * * 18=1 ETH // 1*10 * * decimals for tokens), as well as the token contract address (supply address(0) / 0x0000000000000000000000000000000000000000 for ETH).

You can also set the timeframe in which to track outflows and the % limit on the outflows relative to total balance with the updateLimits fn which takes the rateLimit as a uint16 (1 = 00.01% // 100 = 01% // basis points) and the timeLimit which is the previous n seconds (3600=1 hour) used to limit and track outflows within

# Sample Contract

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

# Whitelist
For a version with optional whitelist enabled for addresses to not be rate limited use rateLimitWhitelist.sol.

**Set authAddress in constructor or in an init fn!**

authAddress - Used to whitelist addresses to not be rate limited
whitelisted[address] - Used to allow addresses as msg.senders to not be rate limited for the transfer. Use with caution. uint8 to allow for updates later to allow for dynamic limits and limits based on address.

# Benchmarks
### rateLimit benchmarks for gas consumption
gas price gwei // token type // rateLimit or noLimit

44k ETH rL

32k ETH N

53k token rL

39.4k token N

Delta 12-13k

---

@100 gwei
gas price premium per use

0.000082 ETH

$0.24


Would you pay $0.24 to save $72,000,000?
