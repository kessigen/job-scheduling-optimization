# Optimizing Job Scheduling to Minimize Total Wait Time Before Execution

Kessigen Ramasubboo, 27 April 2025

## Introduction

This project focuses on developing efficient ways to schedule tasks on CPUs. We will build our own task scheduling methods using combinatorial optimization techniques. Our main goal is to reduce the total time jobs spend waiting to be executed while still following basic scheduling rules. These rules include making sure jobs start after their specified arrival times, don’t overlap if they’re on the same core, and respect any order they might need to be completed in. By improving task scheduling, we aim to make computers work faster and use resources more efficiently.

## Problem Statement

How can we schedule a set of jobs on a single-core or multi-core processor to minimize the total waiting time for all jobs, while ensuring that jobs start only after their release times and that jobs on the same core do not overlap?

![Job Scheduling Gantt Chart](images/gantt_chart.png)

## Variable and Constraint Growth: Full LP vs. Greedy LP

- Full LP: Solves all tasks in one single optimization.
- Greedy LP: Solves smaller batches of tasks per LP iteratively.

Full LP: In the full LP, the algorithm needs to consider all precedence constraints, ordering and core assignments considered at the same time for all possible schedules. While the result of this algorithm gives us a lower bound on waiting time, it has quadratic complexity. This algorithm has limited scalability. Solving becomes too slow or infeasible beyond N = 22 on a laptop.

Greedy LP. The greedy method divides tasks into batches of size B (e.g., B = 5) and solves them one batch at a time. This algorithm is highly scalable. Our method solves up to N = 1000 in reasonable time frame on the same machine.

While the full LP produces the best schedule, it does not scale well. The greedy LP sacrifices some optimality but allows for efficient large-scale scheduling by significantly reducing problem size and therefore complexity.

![Variable and Constraint Growth: Full LP vs. Greedy LP](images/lp_growth.png)

## Results

Here, we ran 100 simulations for each implementation using randomized (but logically sound) parameter values. We then compared the efficiency of each implementation by evaluating the total wait time in each simulation and plotting the results as probability distributions.

![Probability Distribution of Total Wait Times](images/wait_time_distributions.png)

| Metric | Single (FCFS) | Multicore (Parallel) | Multicore (Batch) |
| --- | --- | --- | --- |
| Minimum | 159 | 0 | 10 |
| 25th Percentile | 315.75 | 0 | 55 |
| 50th Percentile (Median) | 405 | 0 | 74 |
| 75th Percentile | 507.5 | 2 | 107.75 |
| 90th Percentile | 613.6 | 4.1 | 145 |
| 95th Percentile | 638.05 | 8.05 | 175.4 |
| 99th Percentile | 687.41 | 12.04 | 197.2 |
| Maximum | 728 | 16 | 217 |
| Mean | 417.98 | 1.4 | 84.61 |
| Mode | 310 | 0 | 47 |
| Standard Deviation | 131.75 | 2.98 | 44.30 |

## Discussion

There have been many new scheduling algorithms over the years that leverage advances in CPU technology, some able to make predictions like our future-knowing multi-core algorithm or even using AI technology. However to this day, most brands implement simpler algorithms. On average, batch scheduling on multiple cores completes jobs nearly 5 times faster than traditional FCFS on a single core. However, compared to naive logic of FCFS scheduler which has alomost 0 computational overhead, the time gain must be greater than the total computational overhead of the multiple LP runs for the greedy algorithm.

Furthermore, we found that combinatorial explosions can occur during the search for an optimal schedule, sometimes even regardless of the number of jobs, N. These situations can drastically increase the scheduler’s runtime, highlighting a key challenge in scaling optimization-based approaches. For future work, we can try to mitigate this by adding a layer of code to our greedy algorithm that detects high overlapping ’density’ of individual batches and dynamically switches to a simpler strategy like FCFS for that batch to avoid performance issues.

## Conclusion

As we have seen, efficient scheduling is crucial to system performance. While, more complex algorithms seem to be more optimal, they also have their drawbacks. Therefore, finding the right balance between speed and efficiency is essential.
