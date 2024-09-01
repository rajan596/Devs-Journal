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
- [Design Twitter](#design-twitter)
- [Design Ad click aggregator](#ad-click-aggregator)

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
- Functional Requirements
    - Upload a file
    - Download a file
    - Automatically sync file across devices
- Non Functional Requirements
    - availability >> consistency
    - low latency upload and downloads
    - support large files upto 50 GB
    - High data entity (syn accuracy)
- Core Entities
    - File (raw data)
    - File meta data
    - Users
- APIs
    - Upload: POST /files --> 200
    - Download: GET /file/:fileId --> File and FileMetadata
    - Sync: GET /file/changes --> filesIds[]
- High level design Important points
    - Upload file -- store in S3 -> blob storage
    - Maintain meta data in Primary DB
    - Sync local change to remote
        - Put watcher on files using OS specific native APIs
        - Need to have local DB for maintaining local file systems data
    - Sync remote change to local: via sync api calls
- Deep Dives
    - 50 GB large files support
        - Upload directly to S3 using multipart upload. [S3 Documentation for Multipart upload](https://aws.amazon.com/blogs/compute/uploading-large-objects-to-amazon-s3-using-multipart-upload-and-transfer-acceleration/)
            - Initiate the multipart upload and obtain an upload id
            - Divide the large object into multiple parts, get a presigned URL for each part, and upload the parts of a large object in parallel
            - Complete the upload by calling the complete api
            - S3 merges all the chunks upon client finalisation call
        - Ask S3 to give pre-signed link where client can upload data
        - Need to use chunking so that small chunks can be uploaded
        - maintain hash of the bytes to see if content is same across different level
        - chunks can be stored inside file meta data object | NoSQL DynamoDB can be used for it
        - Before updating chunk status as uploaded we can check with S3 if its actually uploaded ?
        - We can get S3 notification as CDC from Object storage
    - CDN is not needed here as user might be doing operationg with his own file and S3 data centre might be located near geo region. CDNs are pretty expensive.
    - Data can be compressed to transfer fewer bytes over network
    - Sync fast
        - Long Polling / Websocket will be overkill here
        - Adaptive polling from client / Refresh button
        - Get only delta updated chunks for any updated file.
    - Sync to be consistent (Optional)
        - Event bus might be put which tracks all the changes
        - DB maintains last processed custor
        - Dropbox does this
        - Reconcilliation periodicall like weekly and check all the user folders and files
    - Security
        - Encryption in transit
        - Encryption in Object storage. Supported by S3
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

# Design Twitter
- Functional Requirements
    - CRUD operations on new tweet
    - Follow other users
    - View a timeline of tweets
    - Like, Reply, retweet
    - Search for tweets
- Non Functional Requirements
    - Scale to 100+ M users
    - handle hgh volume of tweets
    - availavility >> consistenty
    - Security & Privacy of data
    - Low latency
- Core Entities
    - Tweet
    - User
- API 
    - /search?term={term}&sort_by={LIKE|timestamp}&page=0
- high level design Imp points
- Deep Dives
    - How to scale search ?
        - As total data can be in PBs
        - We need inverted index DB
        - ElasticSearch is a powerful search service based on Lucene which supports horizontal scaling, replication, and creating arbitrary full-text indexes on your data - a perfect fit for this problem.
    - To manage high write throuput : 2 Stage architecture
        - Put like count in Redis and once count reaches to the power of 2 Or 100/1000 then update DB
        - Ranking layer finds top 100/1000 tweets by like, sorts and returns to the user
    - Pagination
        - Offset pagination with result cache
            - Fetch extra data from DB than required and cache in Redis
            - Return required N tweets and cache remaining tweets in cache
            - Reduces DB load but Huge data needs to be caches

# Ad Click Aggregator
- Functional Requirements
    - User clicks an ad and gets redirected to advertiser's website
    - Advertiser can query matrix over time with 1 min granularity
- Non Functional Requirements
    - Scalable to support 10k click per second
    - Low latency analytics query < 1s
    - Near real time
    - Idempotency of ad clicks
    - Fault tolerance
    - Data Integrity
- Scale [Better to discuss scale for platform interview questions]
    - 10M ads at given time
    - 10k ad clicks per second at peak
- System Interface & Data Flow (Can be skipped as well)
    - Input to system
        - Click data
        - Adv query
    - Outout
        - Redirections
        - Adv -- aggregated click Metrics
    - Data Flow
        - click data comes into the system
        - user is redirected
        - click data validation (like idempotency)
        - click data logged
        - click data aggregated
        - aggregated data queried
- High level Imp points
    - Giving redirection url to browser may have security concerns like user can directly go to adv link from DOM and not call ad click API
    - User click request is received to our service and our service gives 302 redirect response and logs click
    - Kafka can be introduces to process click events
- Deep dives
    - Apache Flink can help with the Stream aggregations. 
        - We can specify aggregation window like 1 min
        - We can also specify Flush interval like 10 sec. Every 10 sec update DB
    - When some popular ad many people are clicking..
        - Hot partitioning issue in Evetn stream as it might be shareded by partition
        - Hot partition can be handle by making key as -> {adId}:{O-N} random no. which will evently distribute popular add to diff partitions
    - Handle fault tolerance
        - Enable Retention policy in Kafka/kinesis like 7 days. This handles Flink job going down scenarios
        - For large aggregation window size like 1 day - checkpoint can be introduced where Flink stores snapshot in S3 every couple of hours
        - No need for checkpoint for 1 min aggregation window
    - Data Integrity
        - Periodic reconcilliaton to ensuce accuracy of data
        - Dump Kinesis data in S3. Periodically like everyday run Spark job and consume data from S3 , do aggregation --> give it to reconcilliation worker. Worker overrides data in OLAP db in case mismatch is found
    - Idempotency
        - generate Ad impression id - unique id and send it to browser. When user clicks send the same impression id to server
        - store impression id in Redis for some time.
        - Some user can change impression id can can do manual hits
            - To handle this impression id can be signed one and send to browser
            - Browser sends signed impression id -> validation this id from server side.


### References
- [Hello Interview](https://www.hellointerview.com/learn/system-design/answer-keys/leetcode)
- [System Design One](https://systemdesign.one/system-design-interview-cheatsheet/#system-design-template)
- [YouTube: System Design Interview](https://www.youtube.com/@SystemDesignInterview)
- [YouTube: Tech Dummies Narendra L](https://www.youtube.com/@TechDummiesNarendraL)
