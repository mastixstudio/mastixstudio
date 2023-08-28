# Introduction to Adjoint Algorithmic Differentiation (AAD)

Adjoint Algorithmic Differentiation (AAD)[^1] has emerged as a game-changing tool in risk management, driving unprecedented efficiencies in risk calculations.

However, while the advantages are transformative, implementing AAD for large-scale application is challenging. Simple or straightforward implementations of AAD are not sufficient for bank-wide usage. 

And modifying calculations within legacy systems to support AAD is not a feasible workaround, as most legacy architecture inherently prevents the integration of AAD.


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

In finance, assessing risk is deeply tied to understanding sensitivities — how financial outcomes shift in response to parameter variations. Traditional methods to compute sensitivities are computationally intensive and often intractable for real-world financial calculations. Adjoint Algorithmic Differentiation (AAD) offers a way to calculate these sensitivities with exceptional efficiency. With AAD, financial institutions can increase transparency and enhance processes: from detailed stress testing to deploying advanced AI techniques.



## Key Concepts

AAD computes the sensitivities of a function by breaking its operations down into basic steps, which are then traversed through forward and reverse sweeps. This intricate process enables AAD to efficiently capture the sensitivity of a function's output to its input variables. 


The process can be broken down conceptually in a few steps:

- **Decomposing the calculation**
  Central to AAD is decomposing a complex function into a series of basic operations. 
- **Forward sweep**
  A computational graph is constructed during the forward sweep. This graph is a visual representation where nodes represent operations or variables, and edges depict the flow or dependencies of these operations. For each basic operation in the forward sweep, the function is evaluated and the intermediate results are stored. This construction and storing process is often likened to "recording on a tape" due to its meticulous documentation of every operation and outcome.
- **Reverse sweep**
  After the forward pass, the reverse sweep begins by traversing the computational graph backward, from output to input. During this phase, AAD uses the chain rule of calculus to compute the derivatives (or adjoints). The stored intermediate values from the forward sweep are reused, eliminating any redundant calculations and enhancing efficiency.

The sensitivities derived from AAD represent how the function's output changes in response to variations in each input, but it's important to understand that these sensitivities are calculated at a specific set of input values. These sensitivities, therefore, offer a snapshot of the function's behavior at that particular input point.

The first step is to create a computational graph representing the the differents steps of the calculation.

- **Computational Graph**: The steps of a calculation represented as nodes in a graph. Within this graph:
  - **Node**: Represents an operation (e.g., addition, multiplication).
  - **Edge**: Represents an intermediate variable or result.



- **Forward Sweep**: The initial pass where the function is evaluated and the operations are recorded on the tape.
- **Reverse Sweep**: After computing the function's value in a forward pass, AAD performs a reverse sweep.
  This step backpropagates sensitivities from the output to the inputs, using the chain rule in reverse at each node.
- **Tape**: A data structure that records operations performed during the forward computation, allowing the reverse sweep to retrace the computation.
  
## Terminology in MASTIX Studio

| Term | Meaning |
|---|---|
| **Forward Sweep** |The initial pass where the function is evaluated and the operations are recorded on the tape.|
| **Reverse Sweep** | After computing the function's value in a forward pass, AAD performs a reverse sweep. This step backpropagates sensitivities from the output to the inputs, using the chain rule in reverse at each node. |
  
The calculation tree in MASTIX Studio is highly optimized. There are several types of nodes in the calculation tree.
The nodes in MASTIX Studio are called *reverses* since a reverse sweep of the nodes determines the derivatives. 
The computation graph consists of *outer reverses* which are nodes that will be traversed by the reverse sweep, and *inner reverses* which are nodes that 
are calculated before the reverse sweep, for performance optimization. 
A typical example is a sub-graph with one input and one output:

This graph can be calculated more efficiently with forward mode algorithmic differentiation, i.e. using only a forward sweep.


[^1]: Algorithmic Differentiation is also known as Automatic Differentiation or *autodiff*. Sometimes, it is referred to using the names of specific implementations, such as *autograd*.
