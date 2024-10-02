# Interview tips for LLD Round

- Clarify all the requirements and confirm with interviewer
    - Who are the users of our system ?
    - What we want to do ?
    - Sometimes complexity lies in very ambiguous requirements
- Create flow and confirm with the interviewer
- Try to come up with class Entities
- [Optional] See if you can come up with a class Diagram.
    - This becomes bit time consuming for Online interviews
    - This helps for planning classes and its relations
- Skip creating constructor / CRUD methods.
- We need to mention important method of our entities
- Write Production grade code
- Data Flow Discussion : Try simulating how Classed together with using methods of each other fulfill the required functionality.
- Discuss `concurrency` issues if it can happen. This is very important at Senior level
- Try to find If Design pattern can be used here and name the Design pattern. Brush up Design patterns before going to LLD Interview
- Design keeping SOLID principles in mind

## Defining the flow

An example of a Possible LLD flow

1. User comes to an ATM machine
2. User inserts Card
3. Card is validated by ATM
    3.1) Invalid card break the flow and shoe error message
4. User enters credentials

# LLD Questions

| Question | Link  | 
|---|---|
| Rate Limiter  | [This Page](#rate-limiter)  |
| Multi Layer Cache  | [This page](#multi-layer-cache)  |
| Design Thread Pool | [This page](#design-thread-pool-like-executorservice-in-java)  |


## Rate Limiter


## Multi Layer Cache

- [Problem Statement](https://github.com/anomaly2104/multi-level-cache-low-level-system-design/blob/main/problem-statement.md) | [Video Problem Description](https://www.youtube.com/watch?v=WUhtVmHr0EM&list=PL564gOx0bCLp8Mqv3nlE8QP8UaX0hKkEA&index=1&t=31s)
- [Solution]()



## Design Thread Pool like ExecutorService in Java

# References
- https://github.com/kumaransg/LLD/blob/main/README.md
- https://github.com/prasadgujar/low-level-design-primer/blob/master/solutions.md