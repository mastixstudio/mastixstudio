<p align="left">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/mastix-logo.png?raw=true" alt="MASTIX Studio Logo" style="width: 20%;">
</p>

# Efficient P&L Explain and Value-at-Risk Attribution 

P&L explain, or P&L attribution, helps decipher daily changes portfolio value to reveal the impact of changes in risk factors. MASTIX Studio leverages efficient sensitivity calculations to deliver fast P&L explain with high precision.

### Benchmark sensitivities
Attribution of P&L can be divided into three parts: changes in portfolio composition, time effects, and changes in 
risk factors―typically market data.

The ideal approach to analyzing the impact of changes in market data is to use benchmark instruments as risk factors 
and the portfolio's sensitivities to these instruments. This results in a thorough P&L explain that is expressed in tradeable instruments.

Using the sensitivities, changes in portfolio value can be approximated through a first-order expansion:

$$\Delta PV = PV_{t_2}-PV_{t_1} \approx \sum_i \frac{dPV_{t_1}}{dR_i} \Delta R_i$$

where $\frac{dPV_{t_1}}{dR_i}$ denotes the sensitivity of the portfolio value to a benchmark instrument, and $\Delta R_i$ denotes the change in the 
benchmark instrument.

### Leveraging AAD for accurate P&L explain
Accurate P&L explain requires calculating sensitivities to many risk factors—ideally all of them. 
This can be computationally demanding, especially for complex portfolios with many risk factors.  

In this context, Adjoint Algorithmic Differentiation (AAD) becomes particularly valuable. 
AAD—a fundamental technique in the domain of machine learning and AI—provides an efficient and precise method 
for computing derivatives. This enables accurate determination of all risk factor sensitivities 
essential for precise P&L explain.

### Components of P&L explain

P&L explain can be structured into three main components:

1. Portfolio composition
   - Closed and matured trades
   - New trades
2. Time effects
   - Cash flows paid
   - Fixings set
   - Discounting effect on remaining cash flows
3. Market data
   - Harmonization of instruments―the cash flow structure of the benchmark instruments can vary over time due to calendar effects
   - Attribution of remaining P&L to changes in benchmark instruments
  
<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/portfolio-composition-time-effects.svg" alt="Basic operation" style="width: 45%;">
  <br/>
  <i>Figure 1: Portfolio composition and time effects.</i>
  <br/>
  <br/>
</div> 

<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/market-data.svg" alt="Basic operation" style="width: 45%;">
  <br/>
  <i>Figure 2: Updated market data and delta interpolation.</i>
  <br/>
  <br/>
</div> 

### Increasing accuracy with delta interpolation

Given that all sensitivities are readily available, the accuracy of P&L explain calculations can be enhanced by utilizing the sensitivities at both time points. The change portfolio value can be approximated using a second-degree polynomial in each risk factor, which is fitted using the sensitivities, leading to a second-order attribution in each risk factor.

### Value-at-Risk attribution

The methodology employed for Value-at-Risk (VaR) attribution parallels that used for P&L explain. However, for VaR attribution, the changes in portfolio value are driven by market data from VaR scenarios.

VaR attribution provides an understanding of how risk factors influence the portfolio under extreme market conditions. This can be used to identify vulnerabilities and evaluate strategies to enhance portfolio resilience.


### P&L explain in practice

With MASTIX Studio, all sensitivities are obtained through AAD without any additional configuration. Whenever a calculation is performed with two different sets of inputs, an attribution analysis can be obtained directly.
The attribution calculation can be called from the Excel add-in or from the API.

The output is a specification of the change of each input together with its contribution to the change in the calculated value. The table below is an example for the present value of a portfolio of interest rate swaps. (Only the first 14 benchmark instruments are shown.)

<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/pnl-explain-table.png?raw=true" alt="Basic operation" style="width: 70%;">
  <br/>
  <i>Figure 3: P&L explain for a swap portfolio.</i>
  <br/>
  <br/>
</div> 

### General attribution analysis with AAD

The method of utilizing sensitivities derived through AAD allows for attributing changes in other complex calculations―such as RWA or CVA―to variations in their inputs. In the same way as for P&L explain, this approach can provide detailed insights into how these calculations are influenced by changes in the underlying risk factors.


&copy; MASTIX 2023
