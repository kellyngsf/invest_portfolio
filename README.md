# Optimizing an Investment Portfolio using Nonlinear Programming

## Introduction
When constructing a financial portfolio, an important and common question to investors is what weightage to assign to each stock they have decided to invest in. Despite it being a common question, it’s still a hard question to answer because it’s difficult to determine which stocks will return the most money and still account for their respective risks. One way to solve this problem is to use nonlinear programming to determine the most optimal weights for each stock to create a stable portfolio. 

Hence, the aim of this project will be to create a financial portfolio composed of 5 specific stocks and use nonlinear programming to determine the most optimal weights for each stock to create a stable portfolio. The objective function of this idea will be to maximise the portfolio return while minimizing the Conditional Value at Risk (CVaR), with a weightage factor to balance the trade-off between return and risk. The constraints will be to ensure the sum of the weights should all be equal to 1 and all weights should be non-negative (i.e., short-selling is not considered). To solve this nonlinear program, the R package ‘nloptr’ will be used to find the maximised objective value and the corresponding weights for each stock. Then, after finding the maximised value of the objective function and the corresponding weights for each stock, the result will be analyzed to explore what it means for the portfolio. Finally, the limitations of this project will be discussed to observe the effectiveness of nonlinear programming as a method to optimize an investment portfolio. 

## As a Nonlinear Programming Problem 
Nonlinear programming will be used to solve the objective function while satisfying the constraints for the stocks in the portfolio. 

The decision variables will be the set of weights of the stocks. In total, there will be 5 stocks that will be considered, Apple (AAPL), Microsoft (MSFT), Amazon (AMZN), NVIDIA (NVDA), and Alphabet Class A (GOOGL). These 5 stocks were chosen because they are among the top stocks of the S&P500 that have the highest market capitalization and are some of the most well-known companies in the market. The decision variable will be a quantitative variable, denoted by wi (where i = 1, 2, 3, 4, 5, as there are 5 different stocks being considered) and it will be the respective weights of each stock.

The objective function will be to maximise the portfolio return while minimising CVaR. To express this in R, a weighting factor (alpha, a value between 0 and 1) will be used to balance the trade-off between return and risk. The mean return value for each stock (for the past 10 years, between 31/03/2013 to 31/03/2023) will be used as the portfolio return value. For this project, the alpha will be 0.5 to have an equal trade-off/weighting factor between portfolio return and CVaR. However, when programming an objective function in R, it can not be chosen to maximise and minimize the objective function at the same time. To go around this issue, the CVaR will be multiplied by -1 to make it negative, hence, the entire objective function can be maximised, without losing its intended purpose. The objective function can thus be expressed as, 
```math
max z = α * mean-return- (1 - α) * CVaR
```

It’s important to note that the objective function is a nonlinear equation because the inclusion of CVaR means the function has to calculate the square root of the portfolio variance, which itself is a quadratic function of the weights of the stocks. Hence, the calculation of the objective function is not linear, and therefore, this is a nonlinear programming problem. 

There are three constraints for this project. The first constraint is a budget constraint, which will ensure that the entire budget has been used for investing in the 5 stocks. This will be written as the sum of all the weights being equal to 1 (equality constraint). 
```math
\sum_{i=1}^5 w_i = 1
```
The next constraint will be to make sure that every weight is non-negative, in other words, short-selling of the stocks will not be considered for this project. This is because short-selling indicates the investor is betting on the price of the stock to fall, and when it does fall, the investor makes a profit. However, this project will be solely focused on investment only in stocks that are expected to increase in value. This constraint can be shown as follows, 
```math
w_i  \geq 0 \quad \text{for all i}
```
Although the constraints are linear since the objective function is nonlinear, the entire project combining the nonlinear objective function and linear constraints makes this project a nonlinear programming problem. 

## Assumptions
One assumption of this project is assuming that the mean returns of the stocks are constant in trends. This means that the mean returns of all the stocks, and hence, the covariance matrix are constant over the optimization of the objective function. This will imply that the mean returns for the past 10 years and relationships between the stocks will be similar in the future. By using historical data from the past 10 years, it will thus be assumed that the behaviour of the stocks in the future and their correlations with each other will be similar in the long run. 

Another assumption is assuming that there are no transaction costs or taxes involved when investing or buying stocks. This is because there may be transaction costs such as commissions or taxes on the stocks bought. These extra costs will impact the profitability of your portfolio as it reduces the overall amount of money that is able to be made. Hence, it could influence the overall optimal portfolio allocation to each stock. Therefore, these costs will not be taken into account and this project will only focus on the money earned from investing in stocks. 

Finally, this project assumes that there is a direct trade-off between return and risk. The objective function uses alpha for a linear combination between mean returns and CVaR. The use of alpha means that it will be assumed that as return increases, risk will decrease, and vice versa. Furthermore, the value of alpha will be 0.5, which implies that the objective function will put equal weight on minimizing CVaR and maximizing return. This assumption is important because, in reality, the relationship between return and risk may not be a linear relationship and a direct trade-off. Therefore, to express the mean returns and CVaR in an objective function and use it as a nonlinear programming problem, an alpha will be used and the direct trade-off relationship between the two variables will be assumed. 

## Data Collection
```
# Set the date range for historical price data
start_date <- "2013-03-31"
end_date <- "2023-03-31"

# Fetch historical price data
aapl_prices <- getSymbols("AAPL", src = "yahoo", from = start_date, to = end_date, auto.assign = FALSE)
msft_prices <- getSymbols("MSFT", src = "yahoo", from = start_date, to = end_date, auto.assign = FALSE)
amzn_prices <- getSymbols("AMZN", src = "yahoo", from = start_date, to = end_date, auto.assign = FALSE)
nvda_prices <- getSymbols("NVDA", src = "yahoo", from = start_date, to = end_date, auto.assign = FALSE)
googl_prices <- getSymbols("GOOGL", src = "yahoo", from = start_date, to = end_date, auto.assign = FALSE)

# Calculate daily returns
aapl_returns <- dailyReturn(Ad(aapl_prices))
msft_returns <- dailyReturn(Ad(msft_prices))
amzn_returns <- dailyReturn(Ad(amzn_prices))
nvda_returns <- dailyReturn(Ad(nvda_prices))
googl_returns <- dailyReturn(Ad(googl_prices))

# Merge daily returns
all_returns <- merge(aapl_returns, msft_returns, amzn_returns, nvda_returns, googl_returns)

# Compute the covariance matrix
cov_matrix <- cov(all_returns)
cov_matrix

# Mean returns 
mean_returns <- colMeans(all_returns)
```
The data needed for this project includes the prices of each stock for the past 10 years. The rest of the financial indicators such as covariance, portfolio variance, etc., will be calculated based on the historical prices. The data will be collected from Yahoo Finance. Instead of collecting the data manually from Yahoo Finance for each stock, the R packages ‘quantmod’ and ‘tseries’ will be used to collect and analyse the price data for each stock for the past 10 years. 

Firstly, the data will be collected through the `getSymbols()` function from the ‘quantmod’ package. This function collects the historical prices for every stock (AAPL, MSFT, AMZN, NVDA, and GOOGL) from Yahoo Finance, between 31 March 2013 and 31 March 2023 (10 years). The source (Yahoo Finance) and start and end dates have been specified through the arguments in the `getSymbols()` function. 

Then, the data will be analysed through the `dailyReturn()` function which calculates the daily returns for each stock’s closing price. Within the `dailyReturn()` function, the `Ad()` function is used to select the adjusted closing price from the price data from Yahoo Finance. Thus, the daily returns for the past 10 years have been calculated and stored in the stocks’ respective variables. Every stock’s variable will then be merged into a data set called `all_returns`. Finally, the mean returns of each stock for the past 10 years will be stored in a variable called `mean_returns` by using the function `colMeans()` to calculate the mean of every column, where every column represents a stock. In other words, the value has been calculated by finding the average of the prices for the past 10 years. In this project, returns will be calculated from the prices of the stock because the price of a stock is what determines the amount of return you will get from an investment portfolio. 

The other financial indicators such as the covariance matrix, CVaR, portfolio variance, etc. have also been calculated in R by using base R functions and will be explained in the next section. 

##Analysis
After obtaining the data and organising it into a data set, the data will then be analysed through the objective function, constraint function, as well as a gradient objective function that is needed to run the `nplotr()` function in R. 

The objective function is defined as follows: 
```
objective_function <- function(weights, alpha, mean_returns, cov_matrix, confidence_level) {
  expected_return <- sum(mean_returns * weights)
  portfolio_variance <- as.numeric(t(weights) %*% cov_matrix %*% weights)
  portfolio_volatility <- sqrt(portfolio_variance)
  z_score <- qnorm(1 - confidence_level)
  cvar <- portfolio_volatility * z_score / (1 - confidence_level)
  objective_value <- alpha * expected_return - (1 - alpha) * cvar
  return(-objective_value)
}
```
The objective function takes 5 variables, `weights` (the decision variable and the portfolio weights for each stock), `alpha` (trade-off variable between return and CVaR), `mean_returns` (the mean returns of each stock, where we obtained the data for it earlier), `cov_matrix` (covariance matrix of returns of the stocks), and `confidence_level` (confidence level value needed for CVaR calculation). Then, the objective function is defined so that it calculates the variables it needs to calculate expected_return and CVaR, in order to find the `objective_value`, which is aimed to be maximised. The expected_return will be the mean returns for all stocks, and it will be calculated by the sum of the `mean_returns` * weights, where weights are the decision variables. The CVaR is calculated by -`expected_return` + `portfolio_volatility` * z_score, and the calculations for those variables are first calculated and stored in their respective variables. It’s also important to note that the objective value obtained will be the negation of what is obtained through the objective value, as this project’s nonlinear programming problem wants to maximise the objective function, but the function `nloptr` automatically (by default) minimizes the objective function. Hence, by taking the negation of the objective value, we can obtain the maximised objective value of the portfolio objective function. 

After analysing the data with the objective function, we also have to set the constraints, which have been programmed as follows, 
```
equality_constraint <- function(weights) {
  return(sum(weights) - 1)
}
```
The code ensures that the sum of the weights of all the stocks will be equal to 1 so that all the money allocated (budget) for the portfolio has been used. It’s coded as `sum(weights) - 1` because by subtracting 1, the equality will be forced to equal to 0. In other words, the sum of the weights will have to be equal to 1. The code has been written this way to comply with how the `nloptr` function is programmed by default. 
```
gradient_function <- function(weights, alpha, mean_returns, cov_matrix, confidence_level) {
  # Gradient of return
  grad_expected_return <- mean_returns
  
  # Gradient of CVaR
  portfolio_variance <- as.numeric(t(weights) %*% cov_matrix %*% weights)
  portfolio_volatility <- sqrt(portfolio_variance)
  z_score <- qnorm(1 - confidence_level)
  grad_cvar <- cov_matrix %*% weights * z_score / (portfolio_volatility)
  
  # Gradient of the objective function
  grad_objective_value <- alpha * grad_expected_return - (1 - alpha) * grad_cvar
  return(grad_objective_value)
}
```

A gradient function has to be programmed to be inputted into the `nloptr()` function later, as it is a requirement for the Sequential Least-Squares Quadratic Programming (SLSQP) algorithm, which is an algorithm to solve nonlinear problems and will be the algorithm used in `nloptr()` for this project. The gradient function is used to let the objective function know how to change the weights every iteration/cycle of the optimization process. Hence, it allows the algorithm to reach the optimal weights and optimal objective value. The gradient of the returns, CVaR and the objective function involve the same logic as in the objective function, however, they will be slightly different because we are now finding the gradient function. Hence, they will be modified to be calculated using the partial derivatives of the objective function with respect to the weights (of the stocks), which is the decision variable.

After defining the objective function, constraints, and gradient function, a wrapper function needs to be implemented to make the functions defined earlier to be usable in the `nloptr()` function. This is done by fixing some variables to the function and only allowing other certain variables to be used in the optimization algorithm. If the wrapper functions are not defined, the `nloptr()` function wouldn’t be able to run because there will be incorrect inputs of some variables. 
```
objective_function_wrapper <- function(alpha, mean_returns, cov_matrix, confidence_level) {
  function(weights) {
    objective_function(weights, alpha, mean_returns, cov_matrix, confidence_level)
  }
}

equality_constraint_wrapper <- function() {
  function(weights) {
    equality_constraint(weights)
  }
}

gradient_function_wrapper <- function(alpha, mean_returns, cov_matrix, confidence_level) {
  function(weights) {
    gradient_function(weights, alpha, mean_returns, cov_matrix, confidence_level)
  }
}
```

Before running the `nloptr()` function to get the final result, the initial weights need to be set so the `nloptr()` function knows what weights to start with and use them as a starting point to adjust accordingly to find the optimized value. 
```
set.seed(123)  # Set seed for reproducibility
initial_weights <- runif(n_assets)
initial_weights <- initial_weights / sum(initial_weights)  # Normalize so the sum of weights is 1
```

`set.seed(123)` sets a seed for reproducibility to ensure that every time the algorithm is run, the steps the algorithm takes is the same (e.g., the number of iterations will stay the same). Additionally, the last line, `initial_weights <- initial_weights/sum(initial_weights)` is to normalize the weights to make sure the sum of all the weights is 1. 

Finally, the `nloptr()` is used with all the functions defined above:
```
gradient_equality_constraint_wrapper <- function(x) {
  # Assuming x is the vector of decision variables (weights in this case)
  # And the constraint is sum(weights) = 1, the gradient is a vector of ones
  return(rep(1, length(x)))
}

result <- nloptr(x0 = initial_weights,
                 eval_f = objective_function_wrapper(alpha, mean_returns, cov_matrix, confidence_level),
                 eval_grad_f = gradient_function_wrapper(alpha, mean_returns, cov_matrix, confidence_level),
                 lb = rep(0, n_assets),  # All weights should be non-negative (no short-selling)
                 ub = rep(1, n_assets),  # All weights should be less than or equal to 1
                 eval_g_eq = equality_constraint_wrapper(),  # Ensure the sum of weights is equal to 1
                 eval_jac_g_eq = gradient_equality_constraint_wrapper,  # Gradient for equality constraint
                 opts = list(algorithm = "NLOPT_LD_SLSQP", maxeval = 1000, xtol_rel = 1e-6))
```
Something worth noting is the last argument, `xtol_rel = 1e-6`, this is to stop the algorithm when the relative change in the solution for the portfolio weights is smaller than 1e-6. This value was chosen because it’s a common choice for optimization problems such as nonlinear programming problems, as it offers a fair balance between accuracy for the solution and the efficiency of running in the algorithm. 

## Results
The result from the `nloptr()` function (as coded above) is: 
```
## Number of Iterations....: 25 
## Termination conditions:  maxeval: 1000   xtol_rel: 1e-06 
## Number of inequality constraints:  0 
## Number of equality constraints:    1 
## Optimal value of objective function:  -0.284026254390583 
## Optimal value of controls: 0.08692491 0.2382778 0.1236198 0.2669061 0.2842713
```
The number of interactions taken to reach the optimized solution was 25. And even though the “Number of inequality constraints” is 0, the constraint of ensuring all weights are greater than or equal to 0 has been specified in the `nloptr()` function when declaring `lb` (lower bound). And finally, the optimal objective value of the objective function is found to be -0.284 (3 s.f). This objective value indicates the level of balance between the return and CVaR of the stock for the investment portfolio with the optimized weights. In this case, since the objective function negates the original objective value (since `nloptr` by default minimizes the objective function, but this project requires the objective function to be maximised), a bigger negative value indicates a better balance between return and CVaR. Since it is a relatively small negative value of -0.284, it signifies the risk (CVaR) of the stocks with the optimized weights may be relatively higher. 

The weights for each stock were found as follows (from the “Optimal value of controls” line in the image above): 
1. 28.43% in GOOGL
2. 26.69% in NVDA
3. 23.83% in MSFT
4. 12.36% in AMZN
5. 8.69% in AAPL

The nonlinear programming output thus advises allocating the most money to GOOGL and the least to AAPL. It’s also worth noting that the weights for GOOGL, NVDA, and MSFT are around the same value, hence, showing that those 3 stocks are the best to invest in to have good returns and stable risk at the same time. 

## Limitations and Evaluations
The result of this project’s nonlinear program calculated an objective value and values for the weights of every stock. This all together successfully maximised the objective function. However, there are a few limitations to this method. 

One limitation derives from an earlier assumption that the data used (from the past 10 years) reflects the behaviour of the prices of the stocks in the future and their correlations with each other in the long run. Realistically, this may not be true as the stocks may release new products at different times and experience their unique difficulties, which make the prices of their stocks unpredictable. For instance, the recent pandemic has made all prices of stocks take a downward dive, and they are all recovering at their own pace. This would have been unpredictable if we only took data from before the pandemic. One method to combat this limitation is to take a larger data set, for instance, instead of using the past 10 years, the past 20 years could be taken into account to provide a larger view and understanding of the market. Another alternative method could be to use other financial indicators other than price (i.e., P/E ratio) to explore the different relationships between stocks and how they can create a well-balanced investment portfolio. 

Another limitation is how CVaR was calculated for this project. Most commonly, the CVaR calculation, including the one used in this project, is used based on an assumption that the returns are normally distributed. However, this may not be true in reality because the returns may be slightly skewed over the years and hence, deviate from the normal distribution assumption. Therefore, the CVaR calculations and results may not be extremely accurate when accounting for the balance between the return and risk of the stocks in the portfolio. To avoid this limitation, instead of using CVaR, other measurements of risk can be used, such as the Sharpe ratio, volatility, etc., to more accurately capture the risk of a particular stock. 

Overall, the limitations are targeted at the fact that a lot of the statistics and assumptions may go against reality because the market can sometimes behave in unpredictable ways, especially when events such as the Covid-19 pandemic have heavily impacted the markets. Furthermore, since the optimized objective value is -0.284, it may be an option to adjust the value of alpha to obtain a higher objective value, which could possibly cause a bigger focus on maximizing returns, rather than minimizing risk, as -0.284 is a relatively small negative value. 

## Extensions
As briefly discussed in the previous section, possible extensions of this project could be to have different indicators for return and risk and to use more data that covers a larger period of history to more accurately analyse the nature of the market and stock. Furthermore, since the stocks taken into account were mainly in the technology industry, it may be interesting to analyse what happens when we solve a nonlinear program for an investment portfolio that includes stocks in different industries. This is because stocks in different industries have different rates of growth and risk, therefore, when combining them all into one portfolio, it will be interesting to observe how a nonlinear program decides to assign appropriate weights to each stock. 
