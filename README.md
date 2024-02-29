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
w_i  \geq 0 for  all  i
```
Although the constraints are linear since the objective function is nonlinear, the entire project combining the nonlinear objective function and linear constraints makes this project a nonlinear programming problem. 

## Assumptions
One assumption of this project is assuming that the mean returns of the stocks are constant in trends. This means that the mean returns of all the stocks, and hence, the covariance matrix are constant over the optimization of the objective function. This will imply that the mean returns for the past 10 years and relationships between the stocks will be similar in the future. By using historical data from the past 10 years, it will thus be assumed that the behaviour of the stocks in the future and their correlations with each other will be similar in the long run. 

Another assumption is assuming that there are no transaction costs or taxes involved when investing or buying stocks. This is because there may be transaction costs such as commissions or taxes on the stocks bought. These extra costs will impact the profitability of your portfolio as it reduces the overall amount of money that is able to be made. Hence, it could influence the overall optimal portfolio allocation to each stock. Therefore, these costs will not be taken into account and this project will only focus on the money earned from investing in stocks. 

Finally, this project assumes that there is a direct trade-off between return and risk. The objective function uses alpha for a linear combination between mean returns and CVaR. The use of alpha means that it will be assumed that as return increases, risk will decrease, and vice versa. Furthermore, the value of alpha will be 0.5, which implies that the objective function will put equal weight on minimizing CVaR and maximizing return. This assumption is important because, in reality, the relationship between return and risk may not be a linear relationship and a direct trade-off. Therefore, to express the mean returns and CVaR in an objective function and use it as a nonlinear programming problem, an alpha will be used and the direct trade-off relationship between the two variables will be assumed. 
