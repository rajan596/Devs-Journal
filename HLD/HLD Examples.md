# Content Overview
- [URL Shortner](#url-shortner)
- [Top K Problem](#top-k-problem)
- [Location Tracking](#location-tracking-system)
- [Video streaming](#video-streaming--chat-app)
- [Design LeetCode](#design-leetcode)
- [Rate Limiter](#rate-limiter)
- [Distributed Scheduler](#distributed-scheduler)
- [Tagging Service](#tagging-service)
- [Ticket Master](#ticket-master)
- [Dropbox](#design-dropbox-or-google-drive)
- [Design Dropbox](#design-dropbox-or-google-drive)
- [Design Uber](#design-uber)

# URL Shortner

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
    - Good when we have uneven distribution
    - Good when we dont have high frequency update
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
    - Less good when we have uneven distibution of density
    - **Good when we have high frequrency updates**

# Video Streaming / Chat App

- Selection on Communication Protocol
    - HTTP / HTTP Long Polling: 
        - Single Directional Protocol
        - TCP conn terminates upon response. 
        - Overhead of TCP conn creation and termination
    - WebSocket: 
        - Bi-directional comm protocol

# Design LeetCode
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
- Refer [Hello Interview](https://www.hellointerview.com/learn/system-design/answer-keys/leetcode) for complete solution.

# Rate Limiter

### Algorithms for Rate Limiting
1. Token bucket Algorithm

2. Fixed window 

3. Sliding window 

# Distributed Scheduler

[Youtube | ](https://www.youtube.com/watch?v=ur3ioZhwG8w)

# Tagging Service

Service will have main 2 entities
1. Item -- N items
2. Tag -- M tags in system
3. ItamTag -- maintains many:many relationship between Item x tag

- Store all item related data in item
- Store Each new tag in tag table
- Store item tags as a new entry in itamTag table

References: 
    - [YT: System Design Fight Club](https://www.youtube.com/watch?v=WNIR7eiv0Hk)
    - [YT: Partitial Index](https://www.youtube.com/watch?v=CA2_0ZhVW2g)

# Ticket Master

- Functional Requirement
    - Book ticket
    - View Event
    - Search Event
- Non Functional Requirement
    - No double booking. Some part CP for booking, View,Search AP system
    - Reads >> Write
    - Scalability at the time of popular events
- Core Entities
    - Event
    - Venue
    - Performer
    - Ticket
- Important points
    - Booking will be 2 step process
        1. Reserve ticket until payment is done
        2. Book the ticket once payment is done
    - How do we make ticket unavailable for 10 mins configured time if 10 min passed ?
        1. Naive: Cron job runs every 10 mins and updates status from Reserved --> Available
        2. Better: **Redis lock for 10 mins i.e, TTL**
    - To make search faster we can introduce Elastic Search. How it will be synced with our DB ?
        1. Let application code update ES / Publish to queue/kafka
            - Problem: When something goes wrong at each step, difficult to maintain consistency with primary DB
        2. Better: Let CDC handle this with or without kafka
        3. To make it even faster --> OpenSearch (AWS managed ES) gives option to cache 10k query results in LRU fashion
        4. ES data can be cached as well based on user query -> Response
        5. CDN can also be an option here.. API response can be cached 30sec - 1min
            - Benefit: super fast
            - Downsides
                - If query has multiple combinations of params -> data storage in CDN becomes less relevan
                - Cant support personalisations
    - Think how to update seat map view in near real time to user.
        - Long Polling
        - Web Sockets : Requires separate infra
        - Server sent Event : Better option here as based on HTTP and Server can send data to client (1 way)
    - How to handle so many people trying to book tickets for polular events
        - **Virtual waiting queue**
        - Add people in virtual queue - Can be maintained using Redis sorted set
        - Pull 100 people in batch and allow for booking and inform them
- Reference
    - [Youtube: Hello Interview](https://www.youtube.com/watch?v=fhdPyoO6aXI)

# Design Dropbox or Google Drive
- 
- References
    - [Youtube : Hello Interview](https://www.youtube.com/watch?v=_UZ1ngy-kOI)

# Design Uber
- Functional Requirements
    - *User should be able to -* Input start location, end location and get Estimated fare
    - *User should be able to -* Request a ride in realtime
    - *User should be able to -* Drives should be able to accept/deny request, navigate to pickup/dropoff
- Non Functional Requirements <Qualities of the system>
    - *System should be* able to match ride with low latency < 1min or fail
    - Consistency of matching. Ride to driver 1:1
    - Highly available outside the matching
    - Handling high throuput, surges for peak hours or special events. 100s of thousands of requests per region.
- Entities
    - Ride
    - Driver
    - Rider
    - Location
- APIs
    - For Fare Estimates
    - For ride request - async
    - Driver location update at intervals
    - Drive ride accept/reject API
    - Ride status update API - Like Ride status -> picked up | dropped off
- High level design Important points
    - Drivers sends their location at specific interval
    - Driver locations to be stored in Location DB
    - App fetches ETA, distance from Third party map service and calculates fare
    - Ride matching service will send notification to nearby drivers. Driver will accept/reject ride
    - Ride details are updated at each stage like ride accepted, arrived, in-ride, completed
- Deep dives
    - Can do BOTE estimation to see driver location update tps It will be around 600k tps. 
        - **How to handle this much tps ?** / **How to make promimity query faster ?**
            - In memory data store is required. Redis can be used here
            - Redis can handle upto 100k - 1M tps
            - Redis supports GeoHashing. Easy to store, calculate dont need additional Data structure
        - Consistency : We dont send more than one req at a time for given ride 
            - 1 server sequencially sends request to driver one by one
        - Consistency : We dont send any driver more than one request at a time
            - Maintain lock in Redis / DynamoDB based on TTL
        - handle surges
            - Introduce queue for Ride matching service
- Similar Problems
    - Yelp
    - Find my friend
- References
    - [Hello Interview : Youtube](https://www.youtube.com/watch?v=lsKU38RKQSo&t=1s)

### References
- [Hello Interview](https://www.hellointerview.com/learn/system-design/answer-keys/leetcode)
- [System Design One](https://systemdesign.one/system-design-interview-cheatsheet/#system-design-template)
- [YouTube: System Design Interview](https://www.youtube.com/@SystemDesignInterview)
- [YouTube: Tech Dummies Narendra L](https://www.youtube.com/@TechDummiesNarendraL)
