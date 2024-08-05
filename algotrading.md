## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```{r load-data}
# Load data from CSV file
amd_df <- read.csv("~/Downloads/AMD.csv")

# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)

amd_df <- amd_df[, c("date", "close")]
```


##Plotting the Data
Plot the closing prices over time to visualize the price movement.
```{r plot}
plot(amd_df$date, amd_df$close,'l')
```


## Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```{r trading}
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- 0 # Corrected column name
amd_df$accumulated_shares <- 0 # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

# Iterate through all days
for (i in 1:nrow(amd_df)) {
  # Case for last entry, we sell all collected shares at the close price
  if (i == nrow(amd_df)) {
    amd_df$trade_type[nrow(amd_df)] <- 'sell'
    amd_df$costs_proceeds[nrow(amd_df)] <- accumulated_shares * amd_df$close[nrow(amd_df)]
  # Case for first entry, we always buy shares
  } else if (previous_price == 0) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size
    accumulated_shares <- accumulated_shares + share_size
  # When the current price is less than the previous day price we buy shares
  } else if (amd_df$close[i] < previous_price) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size
    accumulated_shares <- accumulated_shares + share_size
  }
  # Update accumulated shares vector
  amd_df$accumulated_shares[i] <- accumulated_shares
  # Update previous_price variable
  previous_price <- amd_df$close[i]
}
```


## Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```{r period}
# Initialize the start and end dates
start_date <- as.Date('2021-01-01')
end_date <- as.Date('2022-01-01')

amd_df <- amd_df[amd_df$date >= start_date & amd_df$date <= end_date, ]

# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- 0 # Corrected column name
amd_df$accumulated_shares <- 0 # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

# Iterate through all days in given time period
for (i in 1:nrow(amd_df)) {
  # Case for last entry, we sell all collected shares at the close price
  if (i == nrow(amd_df)) {
    amd_df$trade_type[nrow(amd_df)] <- 'sell'
    amd_df$costs_proceeds[nrow(amd_df)] <- accumulated_shares * amd_df$close[nrow(amd_df)]
  # Case for first entry, we always buy shares
  } else if (previous_price == 0) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size
    accumulated_shares <- accumulated_shares + share_size
  # When the current price is less than the previous day price we buy shares
  } else if (amd_df$close[i] < previous_price) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size
    accumulated_shares <- accumulated_shares + share_size
  }
  # Update accumulated shares vector
  amd_df$accumulated_shares[i] <- accumulated_shares
  # Update previous_price variable
  previous_price <- amd_df$close[i]
}
```


## Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```{r}
# Initialize variables to calculate profit/loss and invested_capital
profit_loss <- 0
invested_capital <- 0

# Iterate through all days in given time period
for (i in 1:nrow(amd_df)) {
  # Update profit_loss variable
  profit_loss <- profit_loss + amd_df$costs_proceeds[i]
  
  # Update investment capital except for last entry where we sell our shares instead of buying
  if (i != nrow(amd_df)) {
    invested_capital <- invested_capital - amd_df$costs_proceeds[i]
  }
}

# Calculate ROI using formula
return_on_investment <- profit_loss / invested_capital * 100
```

## Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```{r option}
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- 0 # Corrected column name
amd_df$accumulated_shares <- 0 # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

#Initialize variables for calculating average purchase price
total_purchase <- 0
average_purchase_price <- 0

# Iterate through all days in given time period
for (i in 1:nrow(amd_df)) {
  # Case for last entry, we sell all collected shares at the close price  
  if (i == nrow(amd_df)) {
    amd_df$trade_type[nrow(amd_df)] <- 'sell'
    amd_df$costs_proceeds[nrow(amd_df)] <- accumulated_shares * amd_df$close[nrow(amd_df)]
  # Case for first entry, we always buy shares
  } else if (previous_price == 0) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size
    accumulated_shares <- accumulated_shares + share_size
    total_purchase <- total_purchase + abs(amd_df$costs_proceeds[i])
    average_purchase_price <- total_purchase / accumulated_shares
  # When the current price is less than the previous day price we buy shares
  } else if (amd_df$close[i] < previous_price) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size
    accumulated_shares <- accumulated_shares + share_size
    total_purchase <- total_purchase + abs(amd_df$costs_proceeds[i])
    average_purchase_price <- total_purchase / accumulated_shares
  # Case where price has increased by 20% from average purchase price
  } else if (amd_df$close[i] >= 1.2 * average_purchase_price) {
    amd_df$trade_type[i] <- 'sell'
    # We sell half of our shares at the current close price
    amd_df$costs_proceeds[i] <- amd_df$close[i] * (accumulated_shares / 2)
    accumulated_shares <- accumulated_shares / 2
    total_purchase <- total_purchase / 2
  }
  # Update accumulated shares vector
  amd_df$accumulated_shares[i] <- accumulated_shares
  # Update previous price variable
  previous_price <- amd_df$close[i]
}
```


## Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```{r}
# Initialize variables to calculate profit/loss and invested_capital
profit_loss <- 0
invested_capital <- 0

# Iterate through all days in given time period
for (i in 1:nrow(amd_df)) {
  # Update profit_loss variable
  profit_loss <- profit_loss + amd_df$costs_proceeds[i]
  
  # If we bought shares (costs proceeds is negative) then we add that to the invested capital sum
  if (amd_df$costs_proceeds[i] < 0) {
    invested_capital <- invested_capital + abs(amd_df$costs_proceeds[i])
  }
}

# Calculate ROI
return_on_investment <- profit_loss / invested_capital * 100
```

Discussion:
In the year 2021, AMD's innovative developments in their Zen 3 and Zen 3-based Ryzen 5000 CPU series allowed them to compete significantly better against Intel and Nvidia in the technology industry. The rise in AMD shares in the later 2021 year showed that investors believed in the Ryzen CPU's upwards potential, given their product line had been able to outperform other products in the market with their more competitive pricing and higher quality. My first strategy earned $303432.92 more than the second strategy, therefore providing a better ROI. This is since with my first strategy, I was able to sell all my shares at a higher price point, whereas with the second strategy I sold more of my shares at lower price points before the share value increased to its peak.




