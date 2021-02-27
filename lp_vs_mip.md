---
layout: default
---

## The classic transportation problem extended from  lp to mip

The switch between the different formulation use the sequence $setglobal LPFormulation (0 or 1)

~~~
$Title  A Transportation Problem (TRNSPORT,SEQ=1)


* Definition of the equations that will be present in LP or MIP
* (1 for LP 0 for MIP)
$setglobal LPFormulation 0



Sets
       i   canning plants   / seattle, san-diego /
       j   markets          / new-york, chicago, topeka / ;

Parameters

       a(i)  capacity of plant i in cases
         /    seattle     350
              san-diego   600  /

       b(j)  demand at market j in cases
         /    new-york    325
              chicago     300
              topeka      275  / ;

Table d(i,j)  distance in thousands of miles
                    new-york       chicago      topeka
      seattle          2.5           1.7          1.8
      san-diego        2.5           1.8          1.4  ;

Scalar f  freight in dollars per case per thousand miles  /90/ ;

*======= FOR MIP  FORMULATION ==========
* MIP
scalar minS minimum shipment / 100 /
*scalar bigM minimum shipment / 325 /


bigM big M;
*bigM =  smax(j,b(j));
bigM = min(smax(i,a(i)), smax(j,b(j)));


*===============================================================================
Parameter c(i,j)  transport cost in thousands of dollars per case ;
c(i,j) = f * d(i,j) / 1000 ;

Positive Variable x ;

Variables
       x(i,j)  shipment quantities in cases
       z       total transportation costs in thousands of dollars
       ship(i,j) ;

$If %LPFormulation% == 1 POSITIVE VARIABLES  ship(i,j) ; ship.UP(i,j) = 1 ;
$If not %LPFormulation% == 1 BINARY VARIABLES ship(i,j) ;



  Equations
   cost        define objective function
   supply(i)   observe supply limit at plant i
   demand(j)   satisfy demand at market j
*======= FOR MIP  FORMULATION ==========*
  minship(i,j) minimum shipment
  maxship(i,j) maximum shipment;

  cost ..        z  =e=  sum((i,j), c(i,j)*x(i,j)) ;
  supply(i) ..   sum(j, x(i,j))  =l=  a(i) ;
  demand(j) ..   sum(i, x(i,j))  =g=  b(j) ;

*======= FOR MIP  FORMULATION ==========*
  minship(i,j)..x(i,j) =g= minS * ship(i,j);
  maxship(i,j)..x(i,j) =l= bigM * ship(i,j);



*========== Model Definition============*
Model transportLP
/
cost,
supply,
demand,
$If not %LPFormulation% == 1    minship ,
$If not %LPFormulation% == 1    maxship
/
;


$If not %LPFormulation% == 1      Solve transportLP using mip minimizing z ;
$If %LPFormulation% == 1          Solve transportLP using lp minimizing z ;

parameter replp(i,j,*) report parameter;
replp(i,j,'LP') = x.l(i,j);
parameter repmip(i,j,*) report parameter;
repmip(i,j,'MIP') = x.l(i,j);


$If %LPFormulation% == 1          Display replp;
$If not %LPFormulation% == 1      Display repmip;
~~~

*Reference:Dantzig, G B, Chapter 3.3. In Linear Programming and Extensions.
Princeton University Press, Princeton, New Jersey, 1963.*


[back](./)
