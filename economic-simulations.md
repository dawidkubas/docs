<a name="form"></a>
# Form 
The simulation form serves as the foundation of the simulation process, guiding users through the configuration of conditions and parameters. Its flexibility allows users to create unlimited scenarios by providing data across a wide range of possibilities. Below are the detailed steps users follow to complete the form.

<a name="1_project_selection"></a>
### 1. Project Selection
The first decision users make is selecting the project to simulate. They can choose from two options:

- Existing Project: Select a project already in the TPRO Network database, which has been simulated previously. All allocation rounds and vesting configurations are pre-filled but can be modified.
- New Token: For a new token, users start by entering its name and unique symbol. The symbol must not exist in the database to avoid conflicts.

Next, users provide a unique simulation name and specify the simulation duration. Currently, the duration can range from 50 to 100 months.

<a name="2_liquidity_configuration"></a>
### 2. Liquidity Configuration
The liquidity block determines how the token will interact with exchanges. Users choose between:

- Decentralized Exchange (DEX): Based on an AMM engine.
- Centralized Exchange (CEX): Using an Order Book engine.

Once the type of exchange is selected, users input the number of tokens in liquidity and the corresponding reserve in stablecoin for the simulation. This step defines the initial market environment for the token.

<a name="3_allocation_rounds_and_vesting"></a>
### 3. Allocation Rounds and Vesting
Allocation rounds define how tokens are distributed across different categories. Users configure rounds by specifying:

Round Type:
- Team: Tokens allocated to team members.
- Ecosystem: Tokens reserved for system goals and project development.
- Investor: Tokens sold to raise funding for the project.

Round Details: 
- Name: A unique identifier for the round.
- Number of Tokens: Total tokens allocated to the round.
- Percentage Released at TGE: The percentage of tokens released at the Token Generation Event.
- Cliff Duration: Length of the cliff (in months).
- Vesting Duration: Length of vesting (in months).
- Selling Price (Investor rounds only): The token sale price, which influences sales strategies.

If users select an existing project, these details are pre-filled but remain editable to accommodate specific simulation needs.

<a name="4_supply_strategy_configuration"></a>
### 4. Supply Strategy Configuration
The next step involves setting supply assumptions for each allocation round. Users select from predefined sales strategies representing typical investor behaviors. Users can configure different strategies for each allocation round, enabling diverse scenarios that impact simulation outcomes. 

<a name="5_secondary_market_configuration"></a>
### 5. Secondary Market Configuration
This stage focuses on simulating secondary market dynamics. Users configure the following:

- Demand and Supply: Enter expected monthly demand for the token in the secondary market and its impact on secondary market supply.
- Types of agents: Selection of agents for simulation. Users define the maximum monthly budget for token purchases, which will only be used if the conditions of their strategy are met. This budget is divided equally among all agents.

Future updates will include the ability to specify demand and supply parameters in a non-linear fashion, enhancing the precision of simulations.

Once the form is completed, all data is saved to a config.json file. This file serves as the direct input for the simulation engine, triggering the process and defining all conditions for the simulation.

<a name="simulations"></a>
# Simulations
Based on the form data provided in the form of a config.json file, the economic simulation process for a given project is initialized. The simulation engine leverages RadCAD, a Python-based framework designed for modeling and simulating dynamic systems. User-supplied values are converted into simulation parameters and are enriched with parameters that are constant for each simulation and are stored in the system_params.  The order of agents' actions is defined based on config.json, and the result is a partial_state_update_blocks.  

<a name="simulation_step"></a>
### Simulation step
In the simulation engine, time is divided into discrete units called steps, which serve as the basis for modeling dynamic interactions in the system. Each step represents a specific time interval (month) and covers the progress of the system over that period. The steps are sequential, with each step building on the results of the previous one. This structure makes it possible to analyze emerging behavior and trends that evolve over time. 

At the beginning of each step, the state of the system is initialized based on conditions from the previous step or the initial configuration for the first step. The driving force behind the system's progress are agents - autonomous units that make decisions and interact with the environment. Agents operate according to predefined rules or strategies, often containing elements of randomness to reflect the unpredictability inherent in the real world, in particular the cryptocurrency market.

One step of the simulation consists of many substeps, and each of them is responsible for the action of one agent. Some agents act through more than one sub-step. The sequence in which agents act plays a key role in shaping the dynamics of the system. Actions occur sequentially, where one agent completes its turn before starting the next. This approach closely mirrors actual market behavior, where agents respond to the observable actions of others, adding strategic depth through decisions that are adjusted based on changing conditions.

The simulation stage proceeds in a structured manner, including the following key phases:

- Agent actions: Each agent performs actions according to its behavioral rules. For example, an agent may decide to buy, sell or hold assets based on its internal state and external signals, such as market trends or resource availability. These actions are defined by a set of rules specific to the agent's goals and constraints, ensuring diverse and realistic behavior in the system. This logic is implemented through policy functions.

- Status updates: When an agent takes an action, the system updates its global status. This includes recalculating shared variables such as token price, liquidity pool or order book states. This phase also takes into account changes triggered by smart contracts, such as token vesting schedules. States are changed via the state update function.

The policy function and the state update functions form a partial state update (PSUB) block. 

A key feature of the simulation engine is its ability to model the interdependencies between agents and the feedback loops that result from these interactions. Agents do not act in isolation; their actions and decisions create ripple effects throughout the system. For example, a sudden price increase caused by the actions of one agent can trigger a cascade of reactive decisions.

<a name="randomness_within_the_scenario"></a>
### Randomness within the scenario

A critical feature of simulation is its ability to balance randomness in agents' decisions with predefined scenario assumptions. The agents' actions are not completely deterministic; they include an element of randomness to mimic unpredictability in the real world. While randomness adds variability, the simulation is anchored by a set of defined values and constraints that represent the general conditions of the system. The interaction of randomness and assumptions allows the simulation to explore a variety of situations while remaining consistent with the agents' general operating assumptions. 

The simulation engine leverages Monte Carlo runs to capture the variability and uncertainty inherent in dynamic systems such as cryptocurrency markets. This approach involves conducting multiple simulation trials, each with unique conditions determined by the non-deterministic behavior of agents. By incorporating randomness in agent decisions, the simulation studies a wide range of possible market situations, offering insights into the system’s behavior under various scenarios.

<a name="dex"></a>
### DEX
The decentralized exchange model is built around the concept of an Automated Market Maker (AMM), reflecting the operational structure of platforms such as Uniswap V2. Unlike the real world with multiple liquidity pools supporting different token pairs, the simulation simplifies this structure by consolidating all trades into one large liquidity pool. This simplification is based on the assumption of the presence of a perfect arbitrageur that ensures consistent prices across the hypothetical smaller pools, equalizing prices immediately when discrepancies arise. 
The simulated tokens are paired with a stablcoin each time. This pairing eliminates the impact of reserve asset price volatility on token prices, allowing the model to focus solely on the relationship between supply and demand. Consequently, price movements are determined by a fixed-product formula in which the product of token reserves in the pool remains constant after each transaction. The price fluctuations of a stablecoin that is a pair to a simulated token are also not taken into account. 

<a name="cex"></a>
### CEX
The centralized exchange model is based on the order book trading mechanism commonly used by traditional exchanges. The model reflects the sequential execution of orders by users and market makers, illustrating how prices and liquidity evolve dynamically in a centralized trading environment. 
The model assumes that user orders are executed without a bid-ask spread, allowing orders to be matched immediately at a single price level. Once user orders are fully executed, a market maker steps in to provide additional liquidity by placing buy and sell orders at varying densities around the current market price. 
The highest concentration of orders is near the prevailing price, and fewer orders are placed as the price distance increases and a maximum spread of 2% is assumed. This approach mimics the behavior of market makers in real markets, ensuring stability and consistent price discovery. As with the DEX model, the simulation assumes the presence of a perfect arbitrageur, ensuring that prices on the CEX remain consistent with prices in other markets.

<a name="vesting"></a>
### Vesting
The simulation engine includes linear monthly vesting with cliff, reflecting the most commonly implemented vesting schedules in the cryptocurrency world. This approach is consistent with the engine's monthly time steps, eliminating the need for daily or weekly vesting mechanisms while maintaining sufficient granularity for accurate modeling.

The initial state of the system is always defined as a token generation event (TGE), serving as the starting point for all token allocations and subsequent releases. Tokens are distributed according to a user-defined token release schedule. This schedule defines the scheduled release of tokens over time, ensuring consistency and transparency in allocations.

Tokens go through the following states during the vesting process:

- Unvested State: Tokens are locked, and are waiting for releases in accordance with the release haromonogram.
- Vested State: Tokens are initially released to the vested state according to the release schedule. These tokens are allocated but are not yet available for use.
- Claimed State: Once allocated, tokens can be collected by users. Each agent takes a portion of the available tokens with a certain randomness interval reflecting the unpredictability of behavior in the real world. These tokens can be used to execute strategies, and when more than one strategy is used, they are divided evenly. 
- Sold: Tokens can be sold, in which case they go into sold status. This means that the agent no longer owns these tokens, and they have gone to other agents in the system or to the liquidity pool.

Each token state change is implemented by a separate partial state update block.

<a name="primary_market_strategies"></a>
### Primary Market Strategies
The simulation includes several strategies for selling tokens in the market. Each strategy reflects a different approach to token sales, focusing on different levels of predictability, profit realization and market behavior. These strategies allow for a nuanced exploration of token dynamics, capturing short-term fluctuations and long-term trends.
 
<a name="random_sale"></a>
#### Random Sale
This strategy models an unpredictable approach to token sales. Agents sell a random percentage of their holdings each month, ranging from 0% to 30%. Often, more tokens are sold immediately after they are claimed, with sales tapering off over time as fewer tokens remain. However, randomization may also lead to scenarios where fewer tokens are sold at the beginning and larger amounts are sold in later months. The Monte Carlo method ensures that all these variations are explored, capturing the broad range of potential behaviors and market impacts. 

<a name="aggressive_random_sale"></a>
#### Aggressive Random Sale
The aggressive random sale strategy increases the range of token sales, with agents selling between 10% and 50% of their holdings each month. This reflects more assertive selling patterns, with consistent and significant amounts of tokens entering the market. Randomization allows for variability in how quickly tokens are sold, from rapid liquidation to steady distribution over time. The Monte Carlo method is used here to explore diverse possibilities, including extreme cases of fast or slow token sales and their implications for the market.

<a name="incremental_doubling_sales"></a>
#### Incremental doubling Sales
This strategy ties token sales to specific price milestones. Tokens are sold whenever the token price doubles relative to the investors buy price, continuing until the total profit equals the initial investment. For example, if $1,000 was invested, tokens are sold at a doubling to recover $1,000, with additional sales occurring at each subsequent doubling (e.g., x4, x8). While most agents adhere to this systematic profit-taking approach, some consistently sell small amounts of tokens before reaching these milestones, driven by impatience or opportunistic behavior. This adds a steady trickle of token sales to the market, introducing variability into the otherwise structured strategy.

<a name="extended_profit_multiplier"></a>
#### Extended Profit Multiplier
Building on the incremental doubling approach, this strategy introduces larger price increases between sales. After the price doubles and the initial investment is recovered, further sales occur only at significant multipliers, such as sixfold (x6), thirtyfold (x30), and beyond. This strategy reflects a long-term perspective, with agents holding tokens for higher profits at major price milestones. However, not all agents strictly follow this approach. Some consistently sell small amounts of tokens earlier, adding steady liquidity to the market. These minor deviations create additional supply while maintaining the overall focus on significant profit-taking.

<a name="incremental_profit_doubling"></a>
#### Incremental Profit Doubling
This strategy combines the structured nature of incremental doubling with increasing profit margins. After recovering the initial investment at the first price doubling, agents sell tokens at each subsequent doubling (e.g., x4, x8), generating profits that exceed the initial investment by 20% (e.g., $1,000; $1,200; $1,400). Alongside this systematic approach, some agents introduce small token sales outside the doubling intervals, driven by varying levels of adherence to the strategy.

<a name="secondary_market_agents"></a>
### Secondary Market Agents

<a name="base_agent"></a>
#### Base agent

Demand Strategy: The agent commits to purchasing tokens every month, utilizing the entire budget allocated for the simulation step. This predictable buying behavior ensures a steady injection of demand into the market, independent of price trends or external factors. Randomness is added to the execution of purchases, creating variability that allows for the exploration of atypical and extreme situations in the simulation. By maintaining consistent purchases, the agent stabilizes market dynamics and reinforces the role of demand as the  driver of supply.

Supply Strategy: The agent sells a fixed percentage of their token holdings at every simulation step. However, a high degree of randomness is introduced to the sales process, resulting in significant variability in the timing and volume of tokens sold. This randomness reflects real-world unpredictability in investor behavior, where supply often shifts based on short-term market conditions, sentiment, or individual decisions. This approach reflects the really observable shift in supply over time, relative to demand.

<a name="low_advanced"></a>
#### Low advanced

Demand Strategy: The less advanced agent buys tokens during upward price trends, purchasing on average every 5% price increase. The value of each purchase is randomly selected from 75% to 100% of the dollars allocated for that month. After at least two purchases, subsequent purchases become progressively smaller, with each being about 7% less than the previous one. The buying pattern resets when the price drops by an average of 20% from the last purchase price. Additionally, the agent makes small purchases at every simulation step, regardless of market conditions, simulating impulsive behavior. This strategy reflects impulsive buying behavior influenced by optimism in rising markets, with reduced activity as price trends weaken.

Supply Strategy: This agent begins selling tokens when the price falls below 20% of their purchase price, selling an average of 5% of their holdings. Sales continue to increase as the price drops further, reflecting a reactionary approach to the down trend. In parallel, the agent sells a small percentage of tokens at every step, regardless of price trends, simulating the actions of impatient investors breaking from the strategy. The Monte Carlo method explores these deviations, capturing how small, random actions influence overall market outcomes. This approach highlights the agent's limited patience and tendency to sell under pressure, exacerbating downward trends.

<a name="moderately_advanced"></a>
#### Moderately Advanced

Demand Strategy: The moderately advanced agent starts buying tokens during upward price trends but requires a stronger signal, typically purchasing every 20% price increase. Purchases are randomly selected from 75% to 100% of the allocated dollars. After two consecutive purchases, the value of subsequent buys decreases by about 8%. The buying cycle resets when the price drops by an average of 30% from the last purchase. Moreover, this agent engages in minor, steady buying activity at every simulation step, reflecting occasional market participation beyond their primary strategy. This strategy captures a more calculated approach to buying during growth phases, with measured activity in volatile markets.

Supply Strategy: This agent begins selling tokens when the price drops by 35% from the entry price, disposing of an average of 8% of their holdings. As the price continues to fall, the agent slows its selling activity. Conversely, the agent also sells tokens when prices increase significantly, selling an average of 15% of their holdings when the price triples relative to their purchase price.  In addition, small token sales occur consistently at every step, representing minor deviations from their planned behavior. Monte Carlo simulations model these variances, exploring how strategic and steady selling impacts the market. This approach balances opportunistic profit-taking with loss management, reflecting moderate sophistication in decision-making.

<a name="very_advanced"></a>
#### Very advanced

Demand Strategy: Advanced agents focus on accumulation during market downturns, buying tokens after prices have dropped by roughly 30%. Each purchase is randomly valued between 80% and 100% of the funds allocated for that step. If two consecutive purchases are made, the value of the following transactions reduces by 10%. This buying cycle resets when prices decline further, by about 35% from the last purchase price. Alongside these calculated actions, this agent makes minor, consistent purchases at every step, reflecting opportunistic behavior to ensure consistent market engagement. This strategy embodies a disciplined, focused on accumulating assets during periods of value decline.

Supply Strategy: This strategy sells tokens only after their value doubles relative to the entry price, releasing an average of 15% of their holdings. During price declines, small portions of tokens are sold incrementally to manage risks without significantly reducing their positions. Additionally, consistent small sales occur during each simulation step, introducing slight variability in the agent's otherwise structured strategy. The Monte Carlo method captures the impact of these small deviations, providing a nuanced view of advanced market behavior. This approach reflects a long-term strategy focused on capitalizing on significant gains while mitigating potential losses.

<a name="speculator"></a>
#### Speculator

Demand Strategy: The speculator adopts a fast-paced buying strategy, acting whenever prices increase by 30%. Purchases are randomly selected within a range of 80% to 100% of the allocated budget for that step. This agent also enters the market after a downtrend reverses, buying tokens after a 10% drop from the peak and a subsequent 5% price increase. If multiple purchases are made, the value of each successive transaction decreases by 9%. Small, consistent purchases at every simulation step represent speculative behavior, reflecting opportunism even outside of their primary buying strategy. This strategy highlights the speculative mindset, focused on seizing opportunities during clear price trends and reversals.

Supply Strategy: The speculator sells tokens after a 20% price increase from the last purchase during an uptrend, disposing of approximately 6% of their holdings. This process continues with every additional 10% price increase until the trend reverses into a downtrend. At that point, speculative selling halts, awaiting the next upward cycle. Small token sales occur regularly at each step, mimicking the behavior of opportunistic traders taking advantage of minor price movements. This approach emphasizes short-term gains and rapid turnover, reflecting the behavior of profit-driven traders.

<a name="scoring_engine"></a>
# Scoring Engine

The scoring engine is a system designed to evaluate token performance using simulation-based metrics. This innovative approach, gives a deeper as well as accessible insight, into projects in the cryptocurrency market. It takes into account various aspects of token allocation, demand, availability and price behavior, drawing insights from Monte Carlo simulations. 

The final score is calculated as a weighted average of these components, providing a balanced representation of the token's overall potential. The final project score consists of all the scores of individual scenarios. A single scenario is scored according to the following criteria:

<a name="allocations"></a>
#### Allocations
This component evaluates how token allocations are distributed among key categories, including providing liquidity on exchanges and ownership of teams and investors. It tracks these allocations both during the Token Generation Event (TGE) and throughout the simulation period. A high score on this component indicates a balanced and fair distribution of tokens among stakeholders, suggesting lower centralization risk and a fair basis for future token investors.

<a name="demand"></a>
#### Demand
The demand metric measures the level of assumed demand generated for a token relative to the initial reserve in its liquidity pool. This indicator provides a fair assessment of projects based on their size. A high score suggests a high liquidity of the token, tuned to the demand expected from prospective investors.

<a name="availability"></a>
#### Availability
Availability examines the percentage of tokens in circulation compared to the total maximum supply four years after the start of the project. This component highlights how available a token is in the broader market. A high score suggests high liquidity, favoring ease of trading, while a lower score may indicate long-term lock-in and potential risk for long-term investors.

<a name="short_term_price"></a>
#### Short-term price
This component analyzes the behavior of the token price over the first six months, including percentage changes and the frequency of up and down price movements. Good performance on this metric suggests that the project has a well-designed tokenomic for the initial phase. 

<a name="long_term_price"></a>
#### Long-term price
Long-term price evaluates a token's performance after the first six months, measuring percentage changes and the intensity of upward and downward trends. A high score reflects a well-designed tokenomics, which can be an additional asset to convince long-term investors.

<a name="additional_metrics"></a>
### Additional metrics
These additional indicators enrich the scoring, offering deeper insights into token price behavior and market dynamics throughout the simulation:

Percentage of time above the listing price: Measures how often the token price has remained above the listing price. A higher percentage indicates correct pricing of the token and a well-designed tokenomic.

- Lowest price: Determines the minimum price observed during the simulation, providing insight into the potential risk of the token falling under adverse conditions.

- Highest price: Captures the peak price achieved, reflecting the token's potential for maximum growth in favorable scenarios.

- Percentage time of price increase: Shows the percentage of periods in which the token's price increased compared to the previous period. A higher percentage suggests a steady growth rate.

- Bottom quartile price: Represents the price below which 25% of observations occurred, offering a perspective on the token's risk profile and the behavior of the lower boundary.

- Median price: Highlights the midpoint of a token's price distribution, providing a clear picture of typical performance across samples.

- Longest rising price trend: Tracks the longest continuous period of price growth, indicating the token's ability to sustain growth phases and inspire investor confidence.

- Longest price decline trend: Measures the longest period of continuous price declines, shedding light on the token's vulnerability to prolonged declines.

<a name="detailed_results_of_the_simulation"></a>
### Detailed results of the simulation

Summary Mode: 
- Average Token Price Across All Scenarios: Displays the average token price in time, calculated from all Monte Carlo trials for each scenario.
- Average Score Across All Criteria: Shows the overall scoring average, aggregating all performance metrics across scenarios.
- Metrics Across All Scenarios: Summarizes key performance indicators, including price trends.
- List of All Tested Scenarios: Provides a comprehensive list of every tested scenario and its configuration.
- Compare Reality to Simulations: Experimental feature that compares real-world data (if available) to the outcomes of the simulated scenarios.
- Average Supply From Primary Market Over Time: Tracks the average token supply introduced from allocation rounds across time.
- Market Metrics: Highlights tokens sold and bought by secondary market users, along with Fully Diluted Valuation (FDV) and Market Cap averages.
- Vesting States for Each Allocation Round: Breaks down the tokens for each allocation round into four states: unvested, vested, claimed and sold.

Expert Mode:
- Average Token Price: Provides the average token price across Monte Carlo runs with additional details on the number of tokens sold and bought each month.
- Scoring of Single Scenario: Displays the score for an individual scenario, detailing its specific outcomes.
- Metrics of Single Scenario: Includes granular metrics for one scenario, such as lowest and highest price or longest upward trends .
- Compare Reality to Simulations: Experimental feature that compares real-world data (if available) to the outcomes of a single scenario.
- Primary Market Supply Over Time: Shows detailed supply data for all allocation rounds, month by month.
- Revenue Realized by Investors and Strategies: Tracks revenue generated by investors and the effectiveness of their chosen strategies.
- Tokens Sold and Bought by Agents: Breaks down secondary market transactions by agent type, providing a detailed view of market dynamics in time.
- State of Liquidity Pool/Market Maker: Provides metrics on liquidity pools or market makers, including reserve levels and token balances.
- Average Volume, FDV, and Market Cap: Displays the average trading volume, Fully Diluted Valuation, and Market Cap across all Monte Carlo trials.
- Minimum Demand to Sustain Listing Price: Calculates the demand required to maintain the listing price over time.
- State of Tokens for Each Allocation Round: Provides a detailed breakdown of tokens states, with a special focus on tokens sold during the simulation.

Expert Mode Additional Feature: Users can compare multiple scenarios in detail, enabling a side-by-side analysis of key metrics and performance outcomes.


