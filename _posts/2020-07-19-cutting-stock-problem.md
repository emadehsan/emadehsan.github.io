---
layout: post
title: "Cutting Stock Problem - 1D - How to cut Rods, Paper Rolls from Stock with minimum wastage"
date:   2020-07-19 00:44:09 +0500
permalink: /cutting-stock-problem
---

<p align="center">
  <img alt="Cutting Stock Problem" src="/images/csp-preview.png"/>
</p>

If you have seen wide Paper or Fabric Rolls cut into smaller width rolls, or cutting of big metal rods.

You might have wondered:
> ### How many ways are there to cut a big stock item into small pieces of required lengths?

The answer is: Too many ways

Perhaps a better question is:
> ### How to cut required small pieces from the stock item so that waste is minimum?

Another question you could ask:
> ### How to cut the required small pieces from the stock item so that minimum possible stock items are used?


All these questions are addressed with the name 'Cutting Stock Problem' (CSP) and are studied under a sub-field of Applied Mathematics called Operations Research. People in the Metal, Glass, Paper and Textile industry deal with this problem everyday.


## 1D vs 2D Cutting Stock Problem
The are a couple of variations of CSP. If each next piece that we want requires a single cut to get, it's called 1D or One Dimensional Cutting Stock Problem. Examples include cutting of Paper Rolls, Fabric Rolls and Metal Rods.

<p align="center">
  <img alt="1D Cutting Stock Problem example" src="/images/1d-example.png"/>
  <p align="center">
    1D Cutting Stock Problem
  </p>
</p>


If the cutting involves a rectangular sheet cut into small rectangular sheets of required sizes, it's called 2D or Two Dimensional Cutting Stock Problem
Examples includes Cutting of Glass Sheets and Metal Sheets.
<p align="center">
  <img alt="1D Cutting Stock Problem example" src="/images/2d-example.png"/>
  <p align="center">
    2D Cutting Stock Problem example
  </p>
</p>


This article discusses 1D Problem in depth and in the next article, we'll discuss the 2D Problem.
For the sake of example, let us assume that we have standard rods of size 89 cm in our stock.
<p align="center">
  <img alt="stock rod 89cm" src="/images/stock-rod.PNG"/>
  <p align="center">
  </p>
</p>


A customer order arrives and it says, they need: 
* 1 rod of 45 cm
* 1 of 25 cm
* 3 rods of 20 cm and
* 1 of 35 cm
<p align="center">
  <img alt="Customer Order" src="/images/customer-order.PNG"/>
  <p align="center">
    Customer Order
  </p>
</p>


## Cutting without a plan
We immediately start cutting. We don't think of rearranging the rods and we cut them in the manner that they were mentioned in the customer order.

From 1st Stock rod, we cut: 45 cm & 25 cm and we are left with 19 cm which does not satisfy any of our customer demands.

We move to the next rod for cutting. we cut: 20 cm, another 20 and another 20, we are left with 29 cm. Our last required rod is 35 cm that cannot be cut from 29 cm so we have to cut another stock rod

We start cutting the 3rd rod. we cut: 35 cm and we are left with 54 cm

<p align="center">
  <img alt="Cut summery" src="/images/cut-summery.PNG"/>
  <p align="center">
    Cut summary
  </p>
</p>

So with no planning for cuts, we ended up using 3 stock rods to satisfy the customer demand and our leftover is 19 cm, 29 cm and 54 cm. We might be able to use the leftover 29 and 54 cm rods in some future orders but 19 cm leftover will most likely go to waste because seemingly no customer needs rods less than 20 cm.

This no planning for cuts approach is certainly not ideal. What if the next order requires a 60 cm rod. We cannot satisfy that with 29 and 54 cm rods.

So, is there a way to satisfy this customer demand So that we use minimum possible stock rods? and our waste is minimum too?


## CSP Tool - to plan the cuts
Let us plan for cuts with the help a tool. We designed this simple open source tool to solve Cutting Stock Problem which is free to use and available at 

> ### <a href="https://alternate.parts/csp">https://alternate.parts/csp</a>

This CSP Tool can plan both 1D and 2D Cutting Stock Problem. (The 2D part is not complete yet.)

Let us focus on 1D tool which helps us in finding an optimal plan to cut your stock rods or stock fabric rolls so that minimum possible stock is used and waste is minimum too.
<p align="center">
  <img src="/images/csp-tool.png"/>
  <p align="center">
    <a href="https://alternate.parts/csp" >CSP Tool</a> in action. Plan for cuts is in the diagram and table on the right
  </p>
</p>



Here you see 2 tables. 
In the top table, you specify the details of customer rods to cut. In the bottom table, you enter stock rods details. 

Notice that you cannot specify the quantities of the stock rods for now. Actually this tool will tell you how many stock rods will be required to completely satisfy this demand.
(We might add this feature soon so that you could limit the number of stock rods to be used and satisfy maximum possible customer demand while staying in the limit.)

Let's enter the sizes of customer rolls in the top table
45 - 1
25 - 1
20 - 3
35 - 1

Enter size of stock rods in the bottom table: 89

Notice that you don't need to write the units like cm or inch. You only specify size. click "Cut". You can see the plan:

<p align="center">
  <img src="/images/solution.PNG"/>
  <p align="center">
  </p>
</p>


In the diagram on the right, we see the plan of how to cut the rods. Each row specifies 1 stock rod and each box represents 1 small customer rod. Rods with the same width or size have the same color. And the blackish color specifies the waste  or the leftover part of the stock rod.

In the bottom right table, we can see the Usage or Utilization of each stock rod. 1st was utilized 95.5% and 89.9% of the 2nd was used. And here is the details of the cuts. You can also download these cut details as a CSV file and import to Microsoft Excel. Or simply copy and paste to Google sheets.

## How it works
Let us see how CSP Tool works behind the scenes. It uses Google's OR-Tools library which is a fantastic library to solve problems like Cutting Stock Problem.

```bash
$ pip install ortools
```

OR-Tools is very straightforward to use. You specify the variables. Variables will contain the result of your problem. In our case, they will contain the number of stock rods used and the plan of cutting
```py
  # array of boolean declared as int, if y[i] is 1, 
  # then y[i] Big roll is used, else it was not used
  y = [ solver.IntVar(0, 1, f'y_{i}') for i in range(k[1]) ] 

  # x[i][j] = 3 means that small-roll width specified by i-th order
  # must be cut from j-th order, 3 times 
  x = [[solver.IntVar(0, b[i], f'x_{i}_{j}') for j in range(k[1])] \
      for i in range(num_orders)]
  
  unused_widths = [ solver.NumVar(0, parent_width, f'w_{j}') \
      for j in range(k[1]) ] 
  
  # will contain the number of big rolls used
  nb = solver.IntVar(k[0], k[1], 'nb')
```

Then you specify constraints. Constraints are limits within which your algorithm must find a solution. In our case the constraints are that
1. All the customer orders must be satisfied
```py
  # constraint: demand fulfillment
  for i in range(num_orders):  
    # small rolls from i-th order must be at least as many in quantity
    # as specified by the i-th order
    solver.Add(sum(x[i][j] for j in range(k[1])) >= demands[i][0]) 
```

2. And Sum of sizes of small rods cut from a big stock rod cannot exceed the size of the big rod.
```py
  # constraint: max size limit
  for j in range(k[1]):
    # total width of small rolls cut from j-th big roll, 
    # must not exceed big rolls width
    solver.Add( \
        sum(demands[i][1]*x[i][j] for i in range(num_orders)) \
        <= parent_width*y[j] \
      )
```

And then specify the objective. The objective is what is your main goal with this algorithm. Like our goal with CSP is to minimize the number of stock items used.
```py
  Cost = solver.Sum((j+1)*y[j] for j in range(k[1]))

  solver.Minimize(Cost)
```

The code for this algorithm is available at on [GitHub](https://github.com/emadehsan/csp)

## Don't know programming? No worries!
If you don't understand this programming part, no worries. You don't need to know programming to use the [CSP Tool](https://alternate.parts/csp).

You can watch the explanation of CSP, demo of CSP Tool and some glass cutting in action in this accompanying video
<p align="center">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/4WXtfO9JB20" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>

### Thinks to keep in mind
* Works with integers only: IP (Integer Programming) problems working with integers only. If you have some values that have decimal part, you can multiply all of your inputs with some number that will make them integers (or close estimation).
* You cannot specify units: Whether your input is in Inches or Meters, you have to keep a record of that yourself and conversions if any.


## Further Readings
## Google's OR Tools
[Google's OR Tools](https://developers.google.com/optimization) library was used in making this tool. They have great tutorials and examples that are easy to follow without any background in Operations Research. It is available for Python, C++, Java and C#.

### Practical Python AI Projects
We also learned a lot from Professor Serge Kruk's book: [Practical Python AI Projects](https://amzn.to/3iPceJD)
It is very easy to understand and totally recommended. In fact most of the code used in our CSP Tool is taken from this book.

The 2D Cutting Stock Problem is even more interesting and challenging to solve. See you next time with 2D CSP. :)
