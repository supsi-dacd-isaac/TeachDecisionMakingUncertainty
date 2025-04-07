# stochastic network design problem



# **Network Capacity Optimization Problem with Uncertain Loads**

### **Problem Statement:**

In this group work, you will act as a network operator. Your task is to design a new network segment connecting 4 energy demand points (e.g., four new households in a residential area). We need to decide how much capacity to install on three distribution lines ($y_1, y_2, y_3$), and how much generators capacity to install on three candidate locations ($x_2,x_3,x_4$).

----

### **Network components:**

A schematic diagram of the network is provided (See the figure). There are 4 nodes in the network, indexed by $i=1,2,3,4$. The key elments in the network are defined as follows:

- **Energy Demand:** Non-negative energy demand is represented by the vector  
$$\xi = [\xi_1,\xi_2,\xi_3,\xi_4] \in \mathbb{R}^{4,+} ,$$ with one value for each node.



- **Distribution Lines:** The nodes are connected in a radial topology where all nodes connect only via node 1. The power capacities of the three distribution lines are defined as:
$$y = [y_1,y_2,y_3] \in \mathbb{R}^{3,+},$$
where $ y_1 $ is the capacity between Node 1 and Node 2, $ y_2 $ is the capacity between Node 1 and Node 3, and $ y_3 $ is the line capacity between Node 1 and Node 4.




- **Generators:** There are 3 generators in the network, and their capacity (maximum power that can be produced) is defined by the non-negative vector
$$x = [x_2,x_3,x_4]\in \mathbb{R}^{3,+}.$$
Generation capacity can only be installed at nodes 2, 3, and 4.


----

###**Objective function:**
Your goal is to minimize the total cost of the investment while ensuring that the network capacity constraints are sattisfied. The cost of the investment can be quantified via a linear function $$\sum_{i=1}^3 c_{x,i} x_{i+1} + \sum_{i=1}^3 c_{y,i} y_i^T,$$ where $c_x $ represents a row vector of generator unit costs in [CHF/pu] for the 3 candiate nodes, and $c_y $ is cost in per-unit for the distribution line capacities.

----

### **Constraint functions:**

Total production capcity constraint:
* $x_2 + x_3 + x_4 \geq \sum_{i=1}^{4} \xi_i$

the total generation capacity must exceed the demand


Nodal in-flow capacity at node 1:
* $y_1 + y_2 + y_3 \geq \xi_1 $
There is not generation in node 1, hence, the total in-flow capacity in node 1 must be larger than its energy demand

Nodal flow constraints on nodes 2,3,4:
* $x_2 + y_1 \geq \xi_2,$
* $x_3 + y_2 \geq \xi_3,$
* $x_4 + y_3 \geq \xi_4,$
the sum of production and in-flow capactiy must be larger than the demand


Distribution capacity on each line:
* $y_1 \geq  x_2- \xi_2, $
* $y_2 \geq  x_3 -\xi_3, $
* $y_3 \geq  x_4 -\xi_4, $

if we have exceess generation capacity ($\Delta_i = x_i - \xi_i$) , we must be able to forward this power to the network such that $y_{i-1} \geq \Delta_i$

----

### **Linear programming:**


Note that this decision-making problem can be casted as a linear optimization problem as follows:
$$
\begin{align*}
\min_{x, y} \quad & c_x^\top x + c_y^\top y \\
\text{s.t.} \quad & A_xx  + A_yy \geq b_\xi \xi\\
 \quad & x_{min} \leq x  \leq x_{max}\\
  \quad & y_{min} \leq y  \leq y_{max}\\
\end{align*}
$$

* By aggregating decision variables and flipping the inequaltiy sign we can write this as a compact LP as follows:

$$
\begin{align*}
\min_{a\in \mathcal{A}} \quad & c^T a \\
\text{s.t.} \quad & A_{in}a \leq b_{in}
\end{align*}$$

where $a=(x,y)$, the matrix $A_{in} = [-A_x, -A_y]$ and $b_{in} = -b_\xi \xi$.


* The inequalty constraint matrix $ A_{\text{in}} $ combines the generation $ A_x$ and line $ A_y $ matrices, and is defined as follows:

$$ 
A_{\text{in}} =
\begin{bmatrix}
-1 & -1 & -1 & 0 & 0 & 0 \\    \tag{Network capacity constraints}
0 & 0 & 0 & -1 & -1 & -1 \\   %  Node 1 in-flow
-1 & 0 & 0 & -1 & 0 & 0 \\    % Node 2 in-flow + gen  
0 & -1 & 0 & 0 & -1 & 0 \\    % Node 3 in-flow + gen  
0 & 0 & -1 & 0 & 0 & -1 \\    % Node 4 in-flow + gen  
1 & 0 & 0 & -1 & 0 & 0 \\   % Line capacity 1  
0 & 1 & 0 & 0 & -1 & 0 \\   % Line capacity 2  
0 & 0 & 1 & 0 & 0 & -1      % Line capacity 3  
\end{bmatrix}
$$


* The right-hand side vector$ b_{\text{in}} $ is given by:
$$
b_{\text{in}} =
\begin{bmatrix}
-\sum\limits_{i=1}^{4} \xi \\
-\xi_0 \\
-\xi_1 \\
-\xi_2 \\
-\xi_3 \\
\xi_1 \\
\xi_2 \\
\xi_3
\end{bmatrix}
$$

