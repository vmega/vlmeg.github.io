---
layout: default
---

## Network Flow Problem

Four nodes in the system network connected to each other.
~~~
Sets 
i /1*4/
Map I to j
 ~~~
The objective function will be 

\\[ \sum_{i,j} xp_{i,j}-\sum_{i,j} xp_{j,i}=F_{i}  \\]

~~~
$title NETWORK FLOW PROBLEM



SET
 I          set of nodes in the network  /I1*I4/
 CONEX(I,I) set of node connections  /I1.I2,I1.I3,I1.I4,I2.I4,I3.I4/;

**  The set of nodes I must be duplicated to refer to


 ALIAS(I,J)


PARAMETERS
 F(I)   the input or output flow at node I
      /I1   7
       I2  -4
       I3  -1
       I4  -2/
 FMAX(I,J) maximum flow capacity of conduction going from I to J;

FMAX(I,J)=4;

**  Optimization variables are declared.

VARIABLES
  z      objective function variable
  x(I,J) the flow going from node I to node J;


x.lo(I,J)=-FMAX(I,J);
x.up(I,J)=FMAX(I,J);

** Constraints

EQUATIONS
 COST        objective function
 BALANCE(I)  conservation of flow conditions;

**  The objective function is a function of flows between connected nodes.
**  This requirement is stated using the $CONEX(I,J) condition.
**  The four BALANCE equations only consider the flow between
**  connected nodes and again the $CONEX(I,J) condition is necessary.

COST ..         z   =E=  SUM(CONEX(I,J),x(I,J)) ;
BALANCE(I) ..   SUM(J$CONEX(I,J),x(I,J))-SUM(J$CONEX(J,I),x(J,I))  =E=  F(I) ;



MODEL netflow /ALL/;
SOLVE netflow USING lp MINIMIZING z;

~~~


Reference:Castillo, E., Conejo A.J., Pedregal, P., Garc\'{\i}a, R. and Alguacil, N. (2002). Building and Solving Mathematical Programming Models in Engineering and Science, Pure and Applied Mathematics Series, Wiley, New York.


[back](./)
