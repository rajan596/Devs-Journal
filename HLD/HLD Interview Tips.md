System design interviews are typically 45 minutes excluding introduction and other related questions.

If we are aware of specific step by step approach and ideal time we can spend on each section then interview can bring best out of your knowledge.

Inspired by [Hello Interview's](https://www.hellointerview.com/) framework listing down common sequencial approach.

1. [System Requirements](#system-requirements)
..

# System Requirements

https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery

## Functional Requirements
- Features of the system
- It should start with **User should be able to..**
- For Product style problems as a candidate we can make decisions on product requirements and act as a Product guy

## Non functional Requirements

- See what Non functional requirement makes particular **problem unique**
- Start with CAP theorem and decide on CP vs AP whats more important.
- Check R:W Ratio

## Back of the Envelop Estimations
- Do only if required.
- Can ask interviewer if this is required. This consumes time and might not be meaningful all the time.
- Can do if result of calculation has direct influence on design  

|Zeroes|Traffic|Storage|
|--|--|--|
|3|Thousand|KB|
|6|Million|MB|
|9|Billion|GB|
|12|Trillian|TB|
|15|--|PB|

## Core Entities
- *Not required for Infrastructure design question*
- It helps to get understanding of what data will be persisited in system and exchanged by the APIs.
Mention core entities of system. Like for online Judge it would be
1. user
2. Problem
3. Submission
4. Leaderboard
5. Competition

## API or Interface
- *Not required for Infrastructure design question*

- This will be user facing public APIs
- It will solve our Functional requirements and Core Entity data flow
- Mention REST APIs for each of the functional requirements

## System Interface and Data Flow (For Infrastructure/platform Design Questions)


## High level design

- Focus on Functional Requirements mentioned earlier as discussed with Interviewer
- Just try to build a system that satisfies those functional requirements and try to avoid any distractions (distraction -> not part of Fn requirements)
- Keep design simple
- When adding any new component ask yourself Is this needed ?
- Done Over Engineer the design
- If you know putting any component will make system more effifient it can be done at this step as well to save further time
- Prefer simplicity over Over Engineering solutions

## Deep dive

## Other Resources

# InterviewReady Guide

![alt text](./assets/interviewready-matrix.png)

# Other Resources
- [Mastering Microsoft system design interview as software engineer
](https://grokkingtechinterview.com/mastering-microsoft-system-design-interview-as-software-engineer-3089096f317e)