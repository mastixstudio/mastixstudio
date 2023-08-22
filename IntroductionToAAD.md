# Adjoint Algorithmic Differentiation (AAD) Guide

Adjoint Algorithmic Differentiation (AAD) has become a widespread tool for efficient risk calculations. AAD presents game-changing efficiency benefits that revolutionize risk management. 
However, while the advantages are transformative, implementing AAD effectively is very challenging. Simple or straightforward implementations of AAD are often insufficient for bank-wide usage. 
And modifying calculations within legacy systems is often not a feasible workaround, as their architecture inherently prevents the integration of AAD.

## Table of Contents
- [Key Concepts](#key-concepts)
- [Terminology in MASTIX Studio](#terminology)
- [Benefits](#benefits)

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
