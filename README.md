# rateLimit
Limits asset outflows from contracts within customisable timeframes.

Usage

Simply import, inherit and swap publicly accessible transfer fns (both ETH and tokens) with the transferL fn. When using transferL the params required are the destination address, the amount in raw value (1*10*18=1 ETH // 1*10****decimals f**** for tokens), as well as the token contract address (supply address(0) / 0x0000000000000000000000000000000000000000 for ETH).

You can also set the timeframe in which to track outflows and the % limit on the outflows relative to total balance with the updateLimits fn which takes the rateLimit as a uint16 (1 = 00.01% // 100 = 01% // basis points) and the timeLimit which is the previous n seconds (3600=1 hour) used to limit and track outflows within.

