# Margin

## Margin Math

The value of the single asset in given quantity can be calculated as:
```math
V(A) = P(A) \times Q(A)

; \quad \text{where } P(A) \text{ and } Q(A) \text{ are a price and quantity of an asset respectively.}
```

Portfolio consists of many assets in varying quantities:
```math
V(A_i) = P(A_i) \times Q(A_i)

; \quad i \in \langle 0, N \rangle \text{ and } N \text{ is a number of assets in portfolio.} 
```

Order on market with traded pair consists of three assets:

 - delivered asset
 - received asset
 - fee asset

```math
V(A_i) = P(A_i) \times Q(A_i)

; \quad i \in \langle 0, 3 \rangle
```

### Order Matrix
We can place $V(A_i)$ into matrix and multiply by fill rate $f$:

```math
F = \begin{pmatrix}
P_1 \times Q_1 \\
P_2 \times Q_2 \\
P_3 \times Q_3
\end{pmatrix} \times f
```

#### Example
Buy 10 ETH @ Limit 1 BTC Fee 0.1 BNB filled at 50%:

```math
F = \begin{pmatrix}
P_{ETH} \times 10 \\
P_{BTC} \times -1 \\
P_{BNB} \times -0.1  
\end{pmatrix} \times 0.5
```

We can write multiple orders into matrix:

```math
F = \begin{pmatrix}
P_1 \times Q_{1,1} & P_1 \times Q_{1,2} & \cdots & P_1 \times Q_{1,N} \\
P_2 \times Q_{2,1} & P_2 \times Q_{2,2} & \cdots & P_2 \times Q_{2,N} \\
\vdots & \vdots & \ddots & \vdots \\
P_M \times Q_{M,1} & P_M \times Q_{M,2} & \cdots & P_M \times Q_{M,N}
\end{pmatrix} \times \begin{pmatrix}
f_1 \\
f_2 \\
\vdots \\
f_N
\end{pmatrix}

; \quad \begin{matrix}
M \text{ is number of assets,} \\
N \text{ is number of orders.} \end{matrix}

```

And we can derive filled value per asset across all orders $F_i$: 
```math
F_i  = P_i \times ( Q_1 \times f_1 + Q_2 \times f_2 + \dots + Q_N \times f_N )
```

### Margin Requirement
Margin requirement $R$ is calculated across all assets by summing up margin requirements $R_i$ for each asset:

```math
R = \sum_{i=1}^{M} R_i
```

Assuming we have margin rate $r_i$ assigned to each asset we can calculate margin requirement $R_i$:

```math
R_i = | F_i + I_i | \times r_i

; \quad I_i \text{ is value of the asset in portfolio.}
```

and then:

```math
R_i  = P_i \times \bigg| Q_1 \times f_1 + Q_2 \times f_2 + \dots + Q_N \times f_N + U_i \bigg| \times r_i

; \quad U_i \text{ is quantity of the asset in portfolio.}
```

**Note** The $R_i$ depends on the fill-rate for each of the orders.
The $R(f)$ is a function of the fill-rate, and if we include open orders, then 
we can minimise or maximise the function $R(f)$ to find minimum or maximum margin requirement, which
then can be subtracted from equity to provide available margin.

#### Example
1. 1 BTC in Portfolio
2. Buy 10 ETH @ Limit 1 BTC Fee 0.1 BNB filled at 50%
3. Sell 5 ETH @ Limit 10000 USDC Fee 0.05 BNB filled at 75%

```math
F = \begin{pmatrix}
P_{BTC} \times -1 & P_{BTC} \times 0 \\
P_{ETH} \times 10 & P_{ETH} \times -5 \\
P_{USDC} \times 0 & P_{USDC} \times 10000 \\
P_{BNB} \times 0.1 & P_{BNB} \times 0.05
\end{pmatrix} \times \begin{pmatrix}
0.5 \\
0.75
\end{pmatrix}
```

and then:

```math
\begin{matrix}
R_{BTC} = P_{BTC} \times \bigg| -1 \times 0.5 + 1 \bigg| \times r_{BTC} \\
R_{ETH} = P_{ETH} \times \bigg| 10 \times 0.5 - 5 \times 0.75 \bigg| \times r_{ETH} \\
R_{USDC} = P_{USDC} \times \bigg| 10000 \times 0.75 \bigg| \times r_{USDC} \\
R_{BNB} = P_{BNB} \times \bigg| 0.1 \times 0.5 + 0.05 \times 0.75 \bigg| \times r_{BNB}
\end{matrix}
```

and so:

```math
R = R_{BTC} + R_{ETH} + R_{USDC} + R_{BNB}
```

```math
\begin{matrix}
R = P_{BTC} \times \bigg| -1 \times 0.5 + 1 \bigg| \times r_{BTC} +
    P_{ETH} \times \bigg| 10 \times 0.5 - 5 \times 0.75 \bigg| \times r_{ETH} + \\
    P_{USDC} \times \bigg| 10000 \times 0.75 \bigg| \times r_{USDC} +
    P_{BNB} \times \bigg| 0.1 \times 0.5 + 0.05 \times 0.75 \bigg| \times r_{BNB}
\end{matrix}
```

### Available Margin
We calculate Available Margin by subtracting Margin Requirement $R$ from Equity:

```math
Available\ Margin = Equity - Margin\ Requirement

```

and Equity is defined as:

```math
Equity = Assets - Liabilities

```
so:

```math
Available\ Margin = I - R - Liabilities

; \quad I \text{ is } Assets
, \quad R \text{ is } Margin\ Requirement \text{.}
```

The $Liabilities$ are the loans, e.g. when utilized margin.

#### Example
Say you have 10000 USDC and you buy BTC worth of 50000 USDC, and that is possible when $r_{BTC} = 0.2$.
Then you need to borrow 40000 USDC in order to complete transaction, so initially we have:

```math
\begin{matrix}
I = 10000 \\
R = 10000 \times 0.0 = 0 \\
Liabilities = 0 \\
Equity = 10000 \\
Available\ Margin = Equity - R = 10000
\end{matrix}
```

and then, after BTC order is filled we get:

```math
\begin{matrix}
I = 50000 \\
R = 50000 \times 0.2 = 10000 \\
Liabilities = Borrowings = Assets - Own\ Capital = 50000 - 10000 = 40000 \\
Equity = I - Liabilities = 10000 \\
Available\ Margin = Equity - R = 10000 - 10000 = 0
\end{matrix}
```

then, if the price of BTC goes up, the portfolio value $I$ goes up, say to $55000$, and we'll get:

```math
\begin{matrix}
I = 55000 \\
R = 55000 \times 0.2 = 11000 \\
Liabilities = 40000 \\
Equity = I - Liabilities = 15000 \\
Available\ Margin = Equity - R = 15000 - 10000 = 5000
\end{matrix}
```

which means that you can now borrow more, since you have $Available\ Margin = 5000$.

On the other hand, if the price of BTC drops, and the portfolio value $I$ goes down, say to $45000$, then we'll get:

```math
\begin{matrix}
I = 45000 \\
R = 45000 \times 0.2 = 9000 \\
Liabilities = 40000 \\
Equity = I - Liabilities = 5000 \\
Available\ Margin = Equity - R = 5000 - 9000 = -4000
\end{matrix}
```
and we will go over the $Available\ Margin$, which may result in $Margin\ Call$.

