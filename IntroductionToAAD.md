# Introduction to Adjoint Algorithmic Differentiation (AAD)

Adjoint Algorithmic Differentiation (AAD)[^1] has emerged as a game-changing tool in risk management, driving unprecedented efficiencies in risk calculations.
However, while the advantages are transformative, implementing AAD for large-scale application is challenging. Simple or straightforward implementations of AAD are not sufficient for bank-wide usage. And modifying calculations within legacy systems to support AAD is not a feasible workaround, as most legacy architecture inherently prevents the integration of AAD.


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
  Central to AAD is decomposing a complex function into a series of basic operations for which sensitivities are already known. The function is represented using a computational graph where nodes signify operations or variables, and edges depict the flow or dependencies of these operations.
  
- **Forward sweep**
  During the forward pass, the function is evaluated at a specific point, moving from input to output nodes. As the function progresses, each intermediate result is     sequentially recorded on a tape—a linear data structure. This tape chronologically logs every operation and provides a step-by-step history of operations for the upcoming   reverse pass.
  
- **Reverse sweep**
  In the reverse pass, the computational graph is retraced using the tape, starting from the output and working backward to the inputs. Using the chain rule and the sequence of operations recorded on the tape, sensitivities for the nodes are combined to calculate the sensitivities of the function.

The sensitivities derived from AAD represent how the function's output changes in response to variations in each input, but it's important to understand that these sensitivities are calculated at a specific set of input values. These sensitivities, therefore, offer a snapshot of the function's behavior at that particular input point.



## Efficient AAD in MASTIX Studio

While the foundational principles of Adjoint Algorithmic Differentiation (AAD) are powerful, a straightforward implementation might not deliver the optimal performance required for high-frequency, large-scale computations. MASTIX Studio pioneers a cutting-edge approach to enhance efficiency and offer more versatile analytics.

- **High-Performance Graph Construction**: Beginning with the computational graph, MASTIX Studio identifies and isolates sub-graphs that can be processed more efficiently than by the conventional reverse pass. Certain sub-graphs, for instance, lend themselves better to forward-mode AD computation. This selective optimization results in a sparser, more streamlined graph, which we term the "high-performance graph." Given that the high-performance graph has selectively "hidden" certain nodes during optimization, and that the nodes that remain on this graph are traversed during the reverse pass, these nodes are aptly named "outer reverses."

- **Efficiency in Handling Independent Variables**: To boost efficiency, independent variables are excluded from the tape. Nodes performing rudimentary operations on these independent variables, termed as "interior nodes," are also kept outside both the tape and the high-performance graph. 

## Summary of concepts


- **Computational Graph**: The steps of a calculation represented as nodes in a graph. Within this graph:
  - **Node**: Represents an operation (e.g., addition, multiplication).
  - **Edge**: Represents an intermediate variable or result.



- **Forward Sweep**: The initial pass where the function is evaluated and the operations are recorded on the tape.
- **Reverse Sweep**: After computing the function's value in a forward pass, AAD performs a reverse sweep.
  This step backpropagates sensitivities from the output to the inputs, using the chain rule in reverse at each node.
- **Tape**: A data structure that records operations performed during the forward computation, allowing the reverse sweep to retrace the computation.
  

| Term | Meaning |
|---|---|
| **Computational Graph** |The steps of a calculation represented as nodes in a graph. Within this graph:
| **Node** | Represents an operation (e.g., addition, multiplication).|
| **Edge** | Represents an intermediate variable or result.|
|**Tape/Trace/Wengert list**| A data structure that records operations performed during the forward computation, allowing the reverse sweep to retrace the computation.|
| **Forward Sweep** | The initial pass where the function is evaluated and the operations are recorded on the tape.|
| **Reverse Sweep** | After computing the function's value in a forward pass, AAD performs a reverse sweep. This step backpropagates sensitivities from the output to the inputs, using the chain rule in reverse at each node. |
| **Outer reverse** | A node in the optimized "outer" graph that will be traversed in the reverse sweep.|
| **Interior node** | A node in the "outer" graph not recorded on the tape.|
| **Constant** | An input to the calculation, which is constant for a particular evaluation of the function.|
  
The calculation tree in MASTIX Studio is highly optimized. There are several types of nodes in the calculation tree.
The nodes in MASTIX Studio are called *reverses* since a reverse sweep of the nodes determines the derivatives. 
The computation graph consists of *outer reverses* which are nodes that will be traversed by the reverse sweep, and *inner reverses* which are nodes that 
are calculated before the reverse sweep, for performance optimization. 
A typical example is a sub-graph with one input and one output:

This graph can be calculated more efficiently with forward mode algorithmic differentiation, i.e. using only a forward sweep.


[^1]: Algorithmic Differentiation is also known as Automatic Differentiation or *autodiff*. Sometimes, it is referred to using the names of specific implementations, such as *autograd*.
