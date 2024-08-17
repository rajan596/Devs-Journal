# Content Overview
- [Top K Problem](#top-k-problem)
- [Location Tracking](#location-tracking-system)
- [Design LeetCode](#design-leetcode)

# Top K Problem

**Problem:** Find Top K most viwed videos, Trending songs etc



**References:**
- [Hello Interview](https://www.hellointerview.com/learn/system-design/answer-keys/top-k)

# Location Tracking System

References:
- **Imp** [School Bus Tracker System Architecture](https://medium.com/@joudwawad/school-bus-tracker-system-architecture-6dd3307e3860)

- Real time location can be updated using WebSockets from Bus to Servers
- 4 Types of Geo Indexing techniques. Geo Indexing can convert 2D (Lat, Long) to 1D 
![alt text](./assets/images/geo_indexing.png)
- QuadTree: Divide region in 4 Parts until required criteria (Like no. of shops < 100) are met
    - Its an in-memory Tree data structure
- Google S2
    - In-memory solutio provided by Google library
    - Good for Geo fencing solutions
    - Allows to draw a circle of 2-3 Kms (or any custom number) and can be helpful locating things there
- Geo Hashing
    - Popular concept used by Yelp and other proximity systems
    - GeoHash String will look something like this -> skb6tyu. Here 1 string character can represent specific cell in a N x M grid.
        - Here if zooming is requires then sk will be considered. appending one character to geohash increases accuracy and decreases area size.
        - geoHash string can be constructed until desired level of precision is achieved
    - Data Storage
        - One of the approach to store the data is to partition data based on Consistent Hashing

# Video Streaming / Chat App

### Important Points:
- Selection on Communication Protocol
    - HTTP / HTTP Long Polling: 
        - Single Directional Protocol
        - TCP conn terminates upon response. 
        - Overhead of TCP conn creation and termination
    - WebSocket: 
        - Bi-directional comm protocol

# Design LeetCode

Refer [Hello Interview](https://www.hellointerview.com/learn/system-design/answer-keys/leetcode) for complete solution. Here I will be writing important points for this Design

#### Important Points
- In this case user is submitting code which we need to execute. For this scenasio we can run code on our main servers. It needs **Isolated** execution to address Security concerns.
- To address Isolation and Security couple of options are...
    - To use VM: Problem with VM is they dont share host OS. Resource intensive and slow start up.
    - Docket containers: Docket containers share host VM. So good option here. We can have multiple containers referring Java/python/etc run time
    - AWS Lambda / Serverless Functions : Can also be an option but might suffer with cold start
- If Leaderboard is in Requirement then efficiently and near realtime showing it is important design problem
    - If just relies on SQL query -> Sloq query -> High DB load
    - If we maintain cache with some TTL then DB load decrease -> Data staleness in API response
    - **Redis Sorted Set** comes to the Rescue. If can have unique elements sorted by score.
- WebSockets to display real time data might be over engineering for this use case. But can mention this solution exists but not relevant. For this case client side polling lets say every 3 Sec would work great.

### References
- [Hello Interview](https://www.hellointerview.com/learn/system-design/answer-keys/leetcode)

### References:
1. [Zoom System Design | System Design Video Application | System design of Zoom/Teams Application](https://www.youtube.com/watch?v=lrMon5qlHAg)
