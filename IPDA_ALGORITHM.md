## Interbank Price Delivery Algorithm (IPDA)

### 1. 4-Phase Price Delivery Cycle
This algorithm works in a repeating cycle to move prices from one point to another. Each phase provides clues about what the algorithm will do next:
* Consolidation (Accumulation): The algorithm holds prices within a narrow range to build order. This is the starting point of all major moves.
* Expansion: The algorithm quickly moves prices out of consolidation to seek liquidity or fill imbalances.
* Retracement: The algorithm returns prices to recently crossed areas (such as the Fair Value Gap) to provide efficiency for institutions that have not yet had time to enter.
* Reversal: The algorithm completely changes the direction of price delivery after reaching key liquidity targets.

### 2. "Look-Back" Mechanism (20, 40, 60 Days)
IPDA uses historical data as an institutional reference point to determine future price movement targets:
* 20-Day Range: Used to determine short-term targets and daily liquidity points.
* 40-Day Range: Serves as the basis for medium-term trends.
* 60-Day Range: Used to identify long-term market structure and the most significant liquidity pools.
The algorithm "remembers" the highest and lowest prices during this period and then "sweeps" them (liquidity raids) to move the market.

### 3. Imbalance Balancing and Efficiency
The algorithm's primary task, beyond manipulation, is balancing. When prices move too quickly (expansion), a gap or Fair Value Gap (FVG) will appear. The IPDA is programmed to return to this area to ensure that each price level has been fairly traded by buyers and sellers.

### 4. Seasonal and Quarterly Shifts
At the macro level, the IPDA sets the market's directional bias, which typically changes every three months (quarterly). These shifts ensure that major market trends do not occur randomly, but rather follow measurable economic cycles and institutional calendars.

### 5. Time Priority (Time is the Foundation)
IPDA prioritizes time over price. The algorithm will only make major moves or manipulations during specific, pre-programmed time windows, such as Kill Zones. Outside of these hours, the algorithm often simply performs consolidation or random, insignificant movements.
