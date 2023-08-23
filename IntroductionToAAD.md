# Introduction to Adjoint Algorithmic Differentiation (AAD)

Adjoint Algorithmic Differentiation (AAD)[^1] has become a widespread tool for efficient risk calculations. AAD presents game-changing efficiency benefits that revolutionize risk management. 
However, while the advantages are transformative, implementing AAD effectively is very challenging. Simple or straightforward implementations of AAD are often insufficient for bank-wide usage. 
And modifying calculations within legacy systems is often not a feasible workaround, as their architecture inherently prevents the integration of AAD.



## Table of Contents
- [Motivation](#motivation)
- [Key Concepts](#key-concepts)
  - Computational graph
  - Forward sweep
  - Reverse sweep
- [Terminology in MASTIX Studio](#terminology-in-mastix-studio)
  - Outer reverse & inner reverse
- [Benefits](#benefits)

## Motivation 

In finance, assessing risk is deeply tied to understanding sensitivities — how financial outcomes shift in response to parameter variations. Traditional methods to compute sensitivities have been computationally intensive and often intractable for bank-wide calculations. Adjoint Algorithmic Differentiation (AAD) offers a way to calculate these sensitivities with exceptional efficiency. With this capability, financial institutions can increase transparency and enhance processes: from detailed stress testing to deploying advanced AI techniques.

At its core, AAD computes the gradient of a function by breaking its operations down into basic steps, which are then traversed through dual forward and reverse sweeps. This intricate process enables AAD to efficiently capture the sensitivity of a function's output concerning its input variables. Its efficiency particularly shines in scenarios where the number of inputs vastly outweighs the number of outputs.


## Key Concepts

- **Computational Graph**: The steps of a calculation represented as nodes in a graph. Within this graph:
  - **Node**: Represents an operation (e.g., addition, multiplication).
  - **Edge**: Represents an intermediate variable or result.

- **Forward Sweep**: The initial pass where the function is evaluated and the operations are recorded on the tape.
- **Reverse Sweep**: After computing the function's value in a forward pass, AAD performs a reverse sweep.
  This step backpropagates sensitivities from the output to the inputs, using the chain rule in reverse at each node.
- **Tape**: A data structure that records operations performed during the forward computation, allowing the reverse sweep to retrace the computation.
  
## Terminology in MASTIX Studio

The calculation tree in MASTIX Studio is highly optimized. There are several types of nodes in the calculation tree.
The nodes in MASTIX Studio are called *reverses* since a reverse sweep of the nodes determines the derivatives. 
The computation graph consists of *outer reverses* which are nodes that will be traversed by the reverse sweep, and *inner reverses* which are nodes that 
are calculated before the reverse sweep, for performance optimization. 
A typical example is a sub-graph with one input and one output:

This graph can be calculated more efficiently with forward mode algorithmic differentiation, i.e. using only a forward sweep.


[^1]: Algorithmic Differentiation is also known as Automatic Differentiation or autodiff. Sometimes, it is referred to using the names of specific implementations, such as autograd.
