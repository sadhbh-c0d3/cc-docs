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
Margin requirement $R$ is calculated across all assets by summing up margin requirements for each asset $R_j$:

```math
R = \sum_{i=1}^{M} R_i
```

Assuming we have margin rate $r_i$ assigned to each asset we can calculate margin requirement $R_i$:

```math
R_i = | F_i | \times r_i
```

and then:

```math
R_i  = P_i \times \bigg| Q_1 \times f_1 + Q_2 \times f_2 + \dots + Q_N \times f_N \bigg| \times r_i
```

#### Example
1. Buy 10 ETH @ Limit 1 BTC Fee 0.1 BNB filled at 50%
2. Sell 5 ETH @ Limit 10000 USDT Fee 0.05 BNB filled at 75%

```math
F = \begin{pmatrix}
P_{BTC} \times -1 & P_{BTC} \times 0 \\
P_{ETH} \times 10 & P_{ETH} \times -5 \\
P_{USDT} \times 0 & P_{USDT} \times 10000 \\
P_{BNB} \times 0.1 & P_{BNB} \times 0.05
\end{pmatrix} \times \begin{pmatrix}
0.5 \\
0.75
\end{pmatrix}
```

and then:

```math
\begin{matrix}
R_{BTC} = P_{BTC} \times \bigg| -1 \times 0.5 \bigg| \times r_{BTC} \\
R_{ETH} = P_{ETH} \times \bigg| 10 \times 0.5 - 5 \times 0.75 \bigg| \times r_{ETH} \\
R_{USDT} = P_{USDT} \times \bigg| 10000 \times 0.75 \bigg| \times r_{USDT} \\
R_{BNB} = P_{BNB} \times \bigg| 0.1 \times 0.5 + 0.05 \times 0.75 \bigg| \times r_{BNB}
\end{matrix}
```

and so:

```math
R = R_{BTC} + R_{ETH} + R_{USDT} + R_{BNB}
```

```math
\begin{matrix}
R = P_{BTC} \times \bigg| -1 \times 0.5 \bigg| \times r_{BTC} +
    P_{ETH} \times \bigg| 10 \times 0.5 - 5 \times 0.75 \bigg| \times r_{ETH} + \\
    P_{USDT} \times \bigg| 10000 \times 0.75 \bigg| \times r_{USDT} +
    P_{BNB} \times \bigg| 0.1 \times 0.5 + 0.05 \times 0.75 \bigg| \times r_{BNB}
\end{matrix}
```

