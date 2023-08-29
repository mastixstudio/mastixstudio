# Efficient P&L Explain and Value-at-Risk Attribution 

P&L explain, or P&L attribution, helps decipher daily changes portfolio value to reveal the impact of various risk 
factors. This paper showcases how MASTIX Studio leverages efficient sensitivity calculations to deliver fast P&L explain 
with high precision.

### Benchmark sensitivities
Attribution of P&L can be divided into three parts: changes in portfolio composition, time effects, and changes in 
risk factors―typically market data.

The ideal approach to analyzing the impact of changes in market data uses benchmark instruments as risk factors 
and the portfolio's sensitivities to these instruments. This results in a thorough P&L explain that is directly tied to tradeable instruments.

Using the sensitivities, changes in portfolio value can be approximated through a first-order expansion:

$$\Delta PV = PV_{t2}-PV_{t2} \approx \sum_i \frac{dPV_{t1}}{dR_i} \Delta R_i$$

where $\frac{dPV_{t1}}{dR_i}$ denotes the sensitivity of the portfolio value to a benchmark instrument, and $\Delta R_i$ denotes the change in the 
benchmark instrument.

### Leveraging AAD for accurate P&L explain
Accurate P&L explain requires calculating sensitivities to many risk factors—ideally all of them. 
This can be computationally demanding, especially for complex portfolios with many risk factors.  

In this context, Adjoint Algorithmic Differentiation (AAD) becomes particularly valuable. 
AAD—a fundamental technique in the domain of machine learning and AI—provides an efficient and precise method 
for computing derivatives. This enables accurate determination of all risk factor sensitivities 
essential for precise P&L explain.
