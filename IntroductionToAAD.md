# Introduction to Adjoint Algorithmic Differentiation (AAD)

Adjoint Algorithmic Differentiation (AAD)[^1] has emerged as a game-changing tool in risk management, delivering unparalleled precision and speed in risk calculations.
However, while the advantages are transformative, implementing AAD for large-scale application is challenging. Simple or straightforward implementations of AAD are not sufficient for bank-wide usage. And modifying calculations within legacy systems to support AAD is not a feasible workaround, as most legacy architecture inherently prevents the integration of AAD.


## Table of Contents
- [Motivation](#motivation)
- [Key Concepts](#key-concepts)
  - Decomposing the function
  - Forward sweep
  - Reverse sweep
- [Efficient AAD in MASTIX Studio](#efficient-aad-in-mastix-studio)
  - High-Performance Graph Construction
  - Efficient Handling of Independent Variables
- [Summary of Concepts](#summary-of-concepts)

## Motivation 

At the heart of financial risk assessment is the need to understand sensitivities: how changes in parameters—like interest rates, market volatilities, or credit spreads—affect financial metrics. Traditional methods to determine sensitivities are computationally intensive and often intractable for large-scale financial calculations. Adjoint Algorithmic Differentiation (AAD) offers a way to calculate sensitivities with exceptional efficiency. With AAD, financial institutions can increase transparency and enhance processes—from detailed stress testing to the deployment of advanced AI techniques.


## Key Concepts

AAD computes the sensitivities of a function by breaking its operations down into basic steps for which sensitivities are already known. These steps are then traversed in both forward and reverse direction through an intricate process that enables AAD to efficiently capture the sensitivity of the function's output to its input variables.

The methodology can be broken down conceptually in a few parts:

- **Decomposing the Function:**
  Central to AAD is decomposing a complex function into a series of basic operations for which sensitivities are already known. The function is represented using a computational graph where nodes signify basic operations or variables, and edges depict the flow or dependencies of these operations. Below is an illustration of how a basic operation is structured and how basic operations combine into a computational graph.

<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/basic-operation.png?raw=true" alt="Basic operation" style="width: 40%;">
  <br/>
  <i>Figure 1: Basic operation.</i>
  <br/>
  <br/>
</div> 

<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/basic-operation-constants.png?raw=true" alt="Basic operation" style="width: 40%;">
  <br/>
  <i>Figure 2: Basic operation on two constants.</i>
  <br/>
  <br/>
</div> 


<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/computation-graph.png?raw=true" alt="Computational graph" style="width: 40%;">
  <br/>
  <i>Figure 3: Combining basic operations yields a computational graph.</i>
  <br/>
  <br/>
</div>

- **Forward Sweep or Forward Pass:**
  During the forward sweep, the function is evaluated at a specific point, moving through the computational graph from input to output nodes. As the function progresses, each intermediate result is sequentially recorded on a tape—a linear data structure that chronologically logs every operation and provides a step-by-step history of operations.
  
  <div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/forward-sweep.png?raw=true" alt="Computational graph" style="width: 50%;">
  <br/>
  <i>Figure 4: Forward sweep.</i>
  <br/>
  <br/>
</div>

- **Reverse Sweep or Reverse Pass:**
  In the reverse sweep, the computational graph is retraced using the tape from the forward sweep, starting from the output and working backward to the inputs. Using the chain rule and the sequence of operations recorded on the tape, sensitivities for the nodes are combined to calculate the sensitivities of the function.
  
<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/reverse-sweep.png?raw=true" alt="Computational graph" style="width: 45%;">
  <br/>
  <i>Figure 5: Reverse sweep.</i>
  <br/>
  <br/>
</div>

The sensitivities derived from AAD represent how the function's output changes in response to variations in each input, but it's important to understand that the sensitivities are calculated at a specific set of input values. The sensitivities, therefore, offer a snapshot of the function's behavior at a particular input point.


## Efficient AAD in MASTIX Studio

While the foundational principles of Adjoint Algorithmic Differentiation (AAD) are powerful, a straightforward implementation might not deliver the performance required for high-frequency, large-scale computations. MASTIX Studio pioneers a cutting-edge approach to enhance efficiency and offer more versatile analytics.

- **High-Performance Graph Construction:** Beginning with the computational graph, MASTIX Studio identifies and isolates sub-graphs that can be processed more efficiently than by the conventional reverse pass. Certain sub-graphs, for instance, lend themselves better to forward-mode AD computation. This selective optimization results in a sparser, more streamlined graph, which we term the "high-performance graph." Given that the high-performance graph has selectively "hidden" certain nodes during optimization, and that the nodes that remain on the graph are traversed during the reverse pass, these nodes are named "outer reverses."
  A sub-graph with a single input and output is an example that can be efficiently optimized using forward mode AD, i.e. by calculating the sensitivity of the sub-graph directly during the forward sweep.

<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/computation-graph-subgraph-colored.png?raw=true" alt="Computational graph" style="width: 40%;">
  <br/>
  <i>Figure 6:  A sub-graph with a single input and output is an example that can be efficiently optimized using forward mode AD, i.e. by calculating the sensitivity of the sub-graph directly during the forward sweep.</i>
  <br/>
  <br/>
</div>

<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/computation-graph-subgraph-collapsed.png?raw=true" alt="Computational graph" style="width: 40%;">
  <br/>
  <i>Figure 7: Sub-graph collapsed into single node on the outer graph.</i>
  <br/>
  <br/>
</div>

- **Efficient Handling of Independent Variables:** To boost efficiency, independent variables are excluded from the tape. Nodes performing rudimentary operations on these independent variables, termed as "interior nodes," are also kept outside both the tape and the high-performance graph. 

## Summary of Concepts

| Term | Meaning |
|---|---|
| **Computation Graph** |The steps of a calculation represented as nodes in a graph. Within this graph:
| **Node** | Represents an operation (e.g., addition, multiplication).|
| **Edge** | Represents an intermediate variable or result.|
|**Tape/Trace/Wengert List**| A data structure that records operations performed during the forward sweep, allowing the reverse sweep to retrace the computation.|
| **Forward Sweep or Forward Pass** | The initial sweep where the function is evaluated and the operations are recorded on the tape.|
| **Reverse Sweep or Reverse Pass** | After computing the function's value in a forward sweep, AAD performs a reverse sweep using the tape from the forward sweep. The reverse sweep backpropagates sensitivities from the output to the inputs, using the chain rule in reverse at each node. |
| **Outer Reverse** | A node in the optimized "outer" graph that will be traversed in the reverse sweep.|
| **Interior Node** | A node in the "outer" graph not recorded on the tape.|
| **Constant** | A value of and independent variable used as input to the calculation.|
  



[^1]: Algorithmic Differentiation is also known as Automatic Differentiation or *autodiff*. Sometimes, it is referred to using the names of specific implementations, such as *autograd*.
