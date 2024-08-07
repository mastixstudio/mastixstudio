# Introduction to Algorithmic Differentiation (AD)

Algorithmic Differentiation (AD)[^1] has emerged as a game-changing tool in risk management, delivering unparalleled precision and speed in risk calculations.
However, while the advantages are transformative, implementing AD for large-scale application is challenging. Simple or straightforward implementations of AD are not sufficient for large scale problems. And modifying calculations within legacy systems to support AD is often not a feasible workaround, as most legacy architecture inherently prevents the integration of AD.


## Table of Contents
- [Motivation](#motivation)
- [Key Concepts](#key-concepts)
  - Decomposing the function
  - Forward sweep
  - Reverse sweep
- [Mixed-Mode AD: Efficient AD in MASTIX Studio](#mixed-mode-ad-flexible-and-efficient-ad-in-mastix-studio)
  - High-Performance Graph Construction
  - Efficient Handling of Independent Variables
- [Summary of Concepts](#summary-of-concepts)

## Motivation 

At the heart of financial risk assessment is the need to understand sensitivities: how changes in parameters—like interest rates, market volatilities, or credit spreads—affect financial metrics. Traditional methods to determine sensitivities are computationally intensive and often intractable for large-scale financial calculations. Algorithmic Differentiation (AD) offers a way to calculate sensitivities with exceptional efficiency. With AD, financial institutions can increase transparency and enhance processes—from detailed stress testing to the deployment of advanced AI techniques.


## Key Concepts

AD computes the sensitivities of a function by breaking down its operations into basic calculations for which sensitivities are already known. These calculations are then traversed in forward and reverse direction through an intricate process that enables captures the sensitivity of the function's output to its input variables. 

The methodology can be broken down conceptually in a few parts:

- **Decomposing the Function:**
  Central to AD is the decomposition of a complex function into a series of basic calculations for which sensitivities are already known. The function is represented using a computational graph where nodes signify basic calculations or variables, and edges depict the flow or dependencies of these calculations. Below is an illustration of how a basic calculation is structured and how calculations combine into a computational graph.

<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/images/basic-operation.svg" alt="Basic operation" style="width: 30%;">
  <br/>
  <i>Figure 1: Basic calculation.</i>
  <br/>
  <br/>
</div> 

<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/images/basic-operation-constants.svg" alt="Basic operation" style="width: 30%;">
  <br/>
  <i>Figure 2: Basic calculation on two constants.</i>
  <br/>
  <br/>
</div> 


<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/images/computation-graph.svg" alt="Computational graph" style="width: 40%;">
  <br/>
  <i>Figure 3: Combining basic operations yields a computational graph.</i>
  <br/>
  <br/>
</div>

The computational graph can then be traversed to capture the sensitivities.
First, the graph is traversed forward, i.e. from inputs to results. This is called the *forward sweep* or *forward pass*. For some problems, the sensitivities can be captured efficiently directly in the forward pass. This approach is called *forward mode AD*. Often, it is more efficient to capture the sensitivities by traversing the graph in reverse—*Adjoint AD* (AAD).

- **Forward Sweep or Forward Pass:**
  During the forward sweep, the function is evaluated at a specific point, moving through the computational graph from input to output nodes. As the graph is traversed progresses, each intermediate result is sequentially recorded on a tape—a linear data structure that chronologically logs every operation and provides a step-by-step history of calculations.
  
  <div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/images/forward-sweep.svg" alt="Forward sweep" style="width: 50%;">
  <br/>
  <i>Figure 4: Forward sweep.</i>
  <br/>
  <br/>
</div>

- **Reverse Sweep or Reverse Pass:**
  In the reverse sweep, the computational graph is retraced using the tape from the forward sweep, starting from the output and working backward to the inputs. Using the chain rule and the sequence of calculations recorded on the tape, sensitivities for the nodes are combined to calculate the sensitivities of the function.
  
<div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/images/reverse-sweep.svg" alt="Reverse sweep" style="width: 45%;">
  <br/>
  <i>Figure 5: Reverse sweep.</i>
  <br/>
  <br/>
</div>

The sensitivities derived from AD represent how the function's output changes in response to variations in each input, but it's important to understand that the sensitivities are calculated at a specific set of input values. The sensitivities, therefore, offer a snapshot of the function's behavior at a particular input point.


## Mixed-Mode AD: Flexible and Efficient AD in MASTIX Studio

While the foundational principles of AD are powerful, a straightforward implementation might not deliver the performance required for high-frequency, large-scale computations. MASTIX ALM Studio pioneers a cutting-edge approach that blends Forward Mode AD and AAD to enhance efficiency and offer more versatile analytics—*Mixed-Mode AD* (MMAD).

- **Flexible Handling of Rudimentary Operations:** Nodes performing rudimentary operations on independent variables, e.g. change of sign of the notional for a leg in an interest rate swap, are not recorded on the tape. This provides more flexibility in the handling of these nodes. The nodes are referred to as *interior nodes*, since they are interior nodes in the computational graph.
  <div align="center">
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/images/computation-graph-interior-nodes-v2.svg" alt="Computational graph" style="width: 40%;">
  <br/>
  <i>Figure 6: Interior nodes that perform some rudimentary operations are handled separately. <br/> The remaining nodes in gray form the basis for the high-performance graph.</i>
  <br/>
  <br/>
</div>

- **High-Performance Graph Construction:** The remaining interior nodes on the graph constitute the basis for the *high-performance graph*, which is a highly optimized graph where all operations are recorded on the tape. To optimize performance, MASTIX ALM Studio identifies and isolates sub-graphs that can be processed more efficiently than by the conventional reverse pass. Certain sub-graphs, for instance, lend themselves better to forward mode AD computation. This selective optimization results in a sparser, more streamlined graph—the high-performance graph. Given that the high-performance graph has selectively "hidden" certain nodes during the optimization, and that the nodes remaining on the graph are traversed during the reverse pass, these nodes are referred to as *outer reverses*.     <br/><br/>
A sub-graph with a single input and output is an example that can be efficiently optimized using forward mode AD, i.e. by calculating the sensitivity of the sub-graph directly during the forward sweep.
  <div align="center">
    <br/>
    <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/images/computation-graph-subgraph-colored-v2.svg" alt="Computational graph" style="width: 40%;">
    <br/>
    <i>Figure 7:  A sub-graph with a single input and output can be optimized using forward mode AD.</i>
    <br/>
    <br/>
  </div>
  <div align="center">
  <br/>
  <img src="https://github.com/mastixstudio/mastixstudio/blob/main/assets/images/computation-graph-subgraph-collapsed-v2.svg" alt="Computational graph" style="width: 40%;">
  <br/>
    <i>Figure 8: The sub-graph is collapsed into single node and is treated as an outer reverse on the high-performance graph.</i>
  <br/>
  <br/>
</div>


## Summary Of Concepts

| Term | Description |
|---|---
| **Adjoint AD** | AD where the computational graph is traversed in reverse to capture the sensitivities.|
| **Computation Graph** |The steps of a calculation represented as nodes in a graph. Within this graph:
| **Constant** | A value of and independent variable used as input to the calculation.|
| **Edge** | Represents an intermediate variable or result.|
| **Forward Mode AD** | AD where the sensitivities are captured directly in the forward pass.|
| **Forward Sweep or Forward Pass** | The initial sweep where the function is evaluated and the operations are recorded on the tape.|
| **Interior Node** | A node in the "outer" graph not recorded on the tape.|
| **Mixed-Mode AD** | A combination of forward mode AD and AAD where sub-graphs in the computational graph are handled using forward mode AD before the the reverse pass.|
| **Node** | Represents an operation (e.g., addition, multiplication).|
| **Outer Reverse** | A node in the optimized "outer" graph that will be traversed in the reverse sweep.|
| **Reverse Sweep or Reverse Pass** | After computing the function's value in a forward sweep, AAD performs a reverse sweep using the tape from the forward sweep. The reverse sweep backpropagates sensitivities from the output to the inputs, using the chain rule in reverse at each node. |
|**Tape/Trace/Wengert List**| A data structure that records operations performed during the forward sweep, allowing the reverse sweep to retrace the computation.|





  



[^1]: Algorithmic Differentiation is also known as Automatic Differentiation or *autodiff*. Sometimes, it is referred to using the names of specific implementations, such as *autograd*.

&copy; MASTIX 2024
