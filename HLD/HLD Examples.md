# Product Design Problems
1. [URL Shortner](#url-shortner)
2. [Top K Problem](#top-k-problem)
3. [Location Tracking](#location-tracking-system)
4. [Video streaming](#video-streaming--chat-app)
5. [Design LeetCode](#design-leetcode)
6. [Rate Limiter](#rate-limiter)
7. [Distributed Job Scheduler](#distributed-scheduler)
8. [Tagging Service](#tagging-service)
9. [Ticket Master](#ticket-master)
10. [Dropbox](#design-dropbox-or-google-drive)
11. [Design Dropbox](#design-dropbox-or-google-drive)
12. [Design Uber](#design-uber)
13. [Design Twitter](#design-twitter)
14. [Design Ad click aggregator](#ad-click-aggregator)
15. [Design Web crawler](#design-web-crawler)
16. [Design Facebook Feed](#design-facebook-feed)
17. [Design Facebook Live comments](#design-facebook-live-comments)
18. [Design Yelp](#design-yelp)
19. [Design Tinder](#design-tinder)
20. [Design tweet Search](#design-tweet-search)

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
- Functional Requirements
    - Client should be able to schedule a job one time / periodic
    - Job scheduler will call a configured API callback at the time of schedule
    - Out of Scope
        - Executing arbitaty code
- Non Functional Requirements
    - Handle 1 Billion Jobs per day ~ 10^9/10^5 = 10^4 => 10,000 jobs/second
    - Job should not be executed multiple times for same timestamp
    - Job execution failure should be handled properly. Retry as per client configurations
    - There should be minimal delay between job schedule time and actual job being scheduled
- High Level Design Important Points
    - Job Details can be stored in HBase as its a distributed multi dimentional sorted map. Each cell is indexed by rowKey, colKey and timestamp
    - Client can call Job Acceptor service. This service will validate the request and add an entry in DB and returns Job ID. This will also assign one partitionId to each job
    - Job extractor service will scan DB and pick eligible jobs for execution and runs every N seconds. It will simply scan and publish data in RoQ.
    - Worker consumers can consume the message from RoQ and do callback as per client preference and update DB with success/failure status.
- Deep Dive
    - How to scan large amount of jobs to be executed efficiently and Reducing delay between scheduled time and picked time?
        - In-order to have good reach throuput to scan eligible jobs from DB we need to assign multiple workers which will do the scanning job.
        - Now if multiple workers will do same query at same time then they will get same jobs to be executed.
        - To solve this problem, Job Acceptor service assigns PartitionId to each job. Zookeeper can maintain worker nodes -> assigned partitions data and partition owner workers will do DB scan. This way we are able to do multiple scan at the same time from DB.
    - Job do not get executed multiple times
        -To ensure same partition is not assigned to multiple workers data is kept in zookeeper and one leader worker can do the job of partition assignmenet / reassignment.
        - partitions can be reassigned in a round robbin fashion.
    - How Job failures will be handles ?
        - If job fails to execute, client can do immediate or exponential retries as per client configuration.
        - If retry is required after some delay it can add data in retry queue
        - If even after max retries request is not successfil message will be sent to Dead letter queue
    - How to cope up with low worker processing and queue backlogs ?
        - If queue size exceeds certain threshold, we can pause scans for a while until workers cope up with the data
        - Bombarding RabbitMQ in case of slow workers can make RabbitMQ cluster unsable.
        - We can have relevant time window for each request. If defined any API call after certain window will be dropped. Lets say sending OTP.
- References
    - [Clockwork: The Backbone of PhonePe’s 2 Billion Daily Jobs](https://tech.phonepe.com/clockwork-the-backbone-of-phonepes-2-billion-daily-jobs/)
    - https://blog.algomaster.io/p/design-a-distributed-job-scheduler
    - [Youtube | ](https://www.youtube.com/watch?v=ur3ioZhwG8w)

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

# Design Web Crawler
- This is a Platform style question
- Problem: Extract the data from web to train LLM
- Functional Requirement
    - Web crowler with initial seed urls
    - extract the data and store the text for later processing
- Non Functional Requirement
    - *Important to ask for scale of the system, because it will have great influence in our system*
    - Crawl 10B pages having size 2MB in 5 days
    - Fault tolerance to handle failures gracefully and resume crawling without loosing progress
    - Politeness to adhere to robots.txt and not overload website servers
- APIs or System Interface
    - Input: Seed of URLs
    - Output: Text data extracted from web pages
- Data flow
    - Take seed URL from frontier and request IP from DNS
    - Fetch HTML from external server using IP
    - Extract the text from the HTML
    - store the text data in database
    - Extract any linked URLs from web pages and add then all to the list of URLs to crawl
    - Repeat above all steps
- High level design Important points
    - Frontier queue: Queue where initial seed URLs were feed. all other urls will be pushed to this queue for processing
    - crawler fetches data from queue and fetches data from URL and maintains entry in DB
    - Store extracted data in S3 along with raw HTML data
    - We can maintain DB like Dynamo or Postgres to store URLs and their S3 content link
- Deep dives
    - Being fault tolerance
        - Break the system into 2 parts. 1. Fetch 2. Text Extraction
        - This way any stage can be reprocessed / resumed independently
        - When URL fetch fails we can have queue as SQS as exponential backoff with visibilityTimeout and max retries
        - If it fails multiple times then URL can be added to dead letter queue
        - If crawler goes down - spin up new machine
    - Politeness
        - crawler should not overload original website and its functionality should not be impacted
        - Check for site having robots.txt - Its their for crawler instructions
        - It will have delays, allow lists, disallowed site pages
        - Crawler need to read robots.txt and check if crawling is allowed or not
        - Respect delays mentioned in this file
        - We can maintain last crawl time for any domain in our DB and entry might be valid upto defined period
        - Rate limiting can be introduced using Redis. We need to add *jitter* here to avoid all workers picking same domain at same time and getting rate limited again
    - Handle scale of data and 5 daty window
        - To handle this scale we need to see how many machines will be required here.
        - BOTE calculation will be required here. This is an I/O intensive task
        - Find machine capacity considering max bandwidth available is 400 Gbps or 50 GB/sec
        - We can assume we can only utilise ~30-50% of the bandwidth of machine.
        - Calculate how many days it will take at this rate and accordingly take servers
    - Ensure same URL do not get processed again
        - Maintain URL data in DB and check before processing.
        - Need to have index on URL
        - Create index on Hash in meta data DB - might be GlobalSexondayIndex
        - Bloom Filter: Probabilistic DS which Trades accuracy for trade. False Positives... (Might be overkill)
    - When multiple URL have same data
        - Maintain hash of content. Check if this content is already available in DB. If available then ignore the page
    - Crawler trap
        - URLs might be linking to other URLs with no content recursively
        - We can maintain depth as well in meta data DB and we can only go upto 20 depth
- Notes
    - DNS name resolution need to happen for every URL. DNS provider can rate limit sstem if it queries too frequently.
    - We can either use paid DNS service, keep multiple DND providers OR can cache domain -> IP from DNS response.
- References
    - https://www.hellointerview.com/learn/system-design/answer-keys/web-crawler

# Design Facebook Feed
- Functional Requirements
    - User shoud be able to create a post
    - Follow friends
    - view their timeline in chronological order
- Non Functional Requirements
    - Highly available with eventual consistency upto 2 mins
    - Viewing the post should be fast i.e, < 500 mS
    - Should be able to handle massive scale of users
    - unlimited follow/followers
- Core Entities
    - User
    - Follow
    - Post
- APIs
    - Create a post
        - POST /post with body { data:"post1" } -> 200 OK
        - JWT Authentication
    - Follow friend
        - POST /users/follow {followerId:"user345"} -> 200 OK
    - View timeline
        - GET /timeline?cursor={:lastPost}&pageSize=100 -> Post[] 200 OK
- HLD Imp Points
    - Create a post
        - Post service inserts data in post DB.
        - This can be simple DB like Relational or NoSQL like DynamoDB
    - Follow friend
        - Can use graph DB like Neo4J but not required for our use case since we dont need traversals
        - Create entry in Follow table. 
    - View timeline
        - Get the follower list, get the posts from followers, sort them and return
        - For quick lookup we will need indexing in PostDB
            - partition Post data by userId
            - sort them by timestamp
        - Pagination / Infinite scroll
            - Maintain cache of Posts when no data exists in Cache. Fetch from DB and store ~ pageSize*50 data
            - User will send *timestamp* as a cursor and we will return data older than passed timestamp value
            - maintain cache with eventual consistent limit TTL like 1 or 2 mins
- Deep dives
    - Hanlde user who is following a large number of users **Fanout Problem**
        - We need to compute the feed on write and keep a precomputed *Feed table*
        - Feed table will have feedUserId, postId, timestamp
        - when any post is created at create time we will check who follows this person and we can then add entry for all the followers in table
        - Partition can be based on user_id and sort key can be timestamp
    - Handle user having large number of followers
        - A popular account can have millions of followers. Writing in feed table for all followers will create too much load on DB and create post worker will be busy creating entries in DB
        - We can introduce async queue in-between to make the process async and make create post service faster
        - Still we have problem of huge no. of writes for popular accounts
        - We can set *threshold* to avoid precomputations for accounts having followwe count > threshold. Instead keep this result handy at the time of feed generation
        - When Feed request comes use precomputed feed table data + popular account posts and merge both post data and return to user.
    - Some posts are popular and valid for few days while some post reads goes to zero as time passes
        - Brings issue of hot key (posts) in DB
        - Good Solution: We can use cache as LRU with long ttl for popular posts. So most of the time popular post data will retrieved from Cache
            - Hot key problem then will be propogated to Cache. Cache partitioning will be difficult if certain partition gets hot keys
        - Best Solution: 
            - Rather than partitioning cache data across machines, we can just replicate cache across N machines with M memory
            - This will ensure all cache machine have same data with very high throughput. Theoratically Nx
- References
    - https://www.hellointerview.com/learn/system-design/answer-keys/fb-news-feed

# Design Facebook Live comments
- Functional Requirements
    - Comment on a live streaming video
    - See all comments in near real time
    - view coments before users joining live stream
- Non Functional Requirements
    - prioritise availability over consistency
    - Low latency in broadcasting comments in near real time
    - Scale to millions of views and thousands of commets / sec
- Core Entities
    - User
    - Live stream
    - Comment
- APIs
    - Add comment: POST /comment with data {comment:"", streamId:""}
    - Fetch past comments : GET /comment-history/:streamId [Imp: Pagination]
- High level Design imp points
    - Comments can be stored in NoSQL db as it dont need any joins or Transactions support
    - Add comment will be stored in NoSQL DB by Comment Manager service
- Deep Dives
    - *WebSocket* is a good solution but overkill here because here R:W ratio is very different
        - User will do very less coments but will continously get new comments. R/W ratio is not balanced.
        - This indicated that we dont need 2 way communication.
        - **SSE** is a good choice over here as its Unidirectional and works over HTTP.
        - Easier to setup with existing Infra
    - Pagination
        - Offset based 
            - Not good for high moving data
            - In case of add/delete data result can be incorrect or duplicate data will be sent
        - Cursor based
            - return custor for lastCommentId
            - If built index on cursot reduces the load on DB
        - Cursor pagination with prefetching and caching
            - Prefetch N*pagesize data from DB and maintain in cache
    - How to support millions of concurrent viewers ?
        - Separate our read and write traffic because R:W ration is not balanced
        - Comment add service will send new comment in Event queue for SSE servers to consume
        - Each SSE server will maintain liveStreamId -> {conn1, conn2, conn3, ...}
        - SSE server will consume Pub/Sub event and send the data to all connections
        - Partition Pub/Sub by videoId so SSE can subscribe to only relevant videoeId channel
        - L7 Load balancer: To make sure users watching live stream land on same server to avoid multiple/all server subscribing to all videoids more itnelligent load balancing technique can be used
            - Less SSE servers will be subscribed to the Pub/Sub resulting in efficient handling
            - Each SSE server will be subscribed to less Pub/sub channels which will reduce n/w traffic, computations
        - Dispatcher Service
            - It gets comments from comment service
            - It knows where to forward this request to which SSE machine
            - It takes load of event distribution and reduces strain on SSE servers
            - But this service need to have accurate details of SSE server details
- References
    - https://www.hellointerview.com/learn/system-design/answer-keys/fb-live-comments

# Design Yelp
- Functional Requirements
    - Search places nearby using terms & location
    - rate place
    - user will be able to see detailed page for any place
- Non Functional Requirements
    - availability > consistency
    - 1:1000 write:read ratio
    - low latency in search
    - Integrity in Ratings
- Core Entities
    - Place
    - Rating
    - Profile
    - Location
- API
    - GET /search?term={term}&lat={lat}&lang={lang}&cursor={cursor}&pageSize=100 -> Partial<Place> [No Auth Required]
    - GET /place/:placeId [No Auth Required]
    - GET /place/:placeId/reviews?cursor={cursor}&pageSize=100 -> Reviews[] [No Auth Required]
    - POST /place/ratings -> 200 OK
- HLD Imp Points
    - Since there are very less writes we can merge search and write service in one service
    - Elastic search can be used as search engine
    - When new places gets updated ES needs to be re-indexed with that data
    - Simple SQL database can be used for place and uniqueness of ratings can be maintained using UniqueKeys in DB
    - ES supports data in x unit radius from given location infor - supports geohashing
- Deep dives
    - TBA
- References
    - https://www.youtube.com/watch?v=pFTyGG4mORk

# Design Local delivery store like gopuff
- Functional Requirements
    - Query available items to buy under 1 Hr based on location
    - User can order goods
- Non Functional Requirements
    - Customer should be able to order from any store within 1 Hr delivery time
    - item availability request should be fast
    - Consistency: No 2 customer place same order for same physical good
    - Orders: 1M orders/day
    - References
        - https://www.hellointerview.com/learn/system-design/answer-keys/gopuff
- Scale
    - 1M orders / day ~ 1o^6/10^5 ~ 10 orders / sec
    - considering uneven traffic we can assume peak traffic as ~50 orders / sec
    - queries: 5% traffic of orders --> 20k requests / second 
- Data Entities
    - Order [id, userId, date]
    - Item [id, name, sku]
    - OrderItem [orderId, itemInstance]
    - ItemInstance [id, itemId, dcId, status]
    - DistributionCentre [lat, long]
- APIs
    - GET /avalaility?lat={}&long={}&items={item1,item2}
    - POST /order {lat: LAT, long: LONG, items: ...}
- HLD Important Points
    - Find DSs who can deliver in 1 Hr
        - Pick data from DC table having distance < x unit and check it agains timeservice having distance < 1 Hr
        - When available API is called use this data to prune down search results
    - Query available items
        - Query from Inventory DB
        - Put a cache in-between to get most of the data from cache with 1 min TTL to maintain freshness
        - Best Solution: Since data is mostly group in near by DC locations. We can group them based on region id like initial 3 digits of postal code in same partition and we can have replicas of partitions to have better throuput. We can tolerate bit of inconsistency here.
    - Order Item **String Consistency** | **Double Booking**
        - If we are maintaining inventory data in different data store like Dynamo and Orders in ACID db then we will can use 3 Phase commit and we will need transaction manager. This will bring additional complexity in our system
        - Same DB for Inventory and Orders - PostgreSQL: 
            - Use Transactions with Isolation level as Serialization
            - In transaction check if inventory items are available -> if yes then create new order -> update items as ordered and then return order to user
        - Problems: Unable to scale independenyly inventory and orders and need to use same DB
- Deep Dives
    - At the time of checkout items are showing as Unavailable
        - Since multiple people could have added items in cart so many people would get frustrated by seeing Item unavailable error at the time of checkout
        - We will need to add some reservation for x minutes when item is added to card and then we will need to free it if not checked out
        - Possible Solution: Adding cache to maintain locked items. We will need to keep cache data in sync. We also need to read from cache before returning available items to user
        - Ideal Solution
            - Add a status - *Reserved* and expiration timestamp in ItemInstance
            - When user add in cart mark it as Reserved with 60 min expiration timestamp
            - When user do checkout update status to Sold
            - Add background worker to periodically check for expired reservations and update status to Available
    - Late allocation of Distribution Centres
        - Some DCs having higher population density will get more orders while some DCs will be underutilised
        - Sol 1: We can put some rule engine which based on availability can assign DCs
        - Sol 2: Rule Engine - Considers current inventory, reservations, geographycal distribution
        - Sol 3: [Complex] Create a Promise entity which will give list of DCs which can fulfill the order
            - Instead of creating reservations, create Promise
            - Availability service will consider Promise and deduct availability based on Promise data
            - Resolve Promise when order is placed

# Design Tinder
- Functional Requirements
    - See potential match as per filters & nearby location --> Feed
    - Allow left and right swipe
    - Match notification
    - Out of scope
        - Chat between them
        - premium subscription unlimited swipes. We will assume unlimited swipes
- Non Functional Requirements
    - Low latency in showing nearby profiles & almost zero time in next profiles one by one
    - System should be highly available
    - Scale to 20M DAU, ~200 swipes/user/day
    - Do not show repeated profile user has already seen
- Core Entities
    - User / UserProfile
    - Match (userA <-> userB)
    - Swipe -> Expression of saying Yes or No by usera to userb
    - Location
- API
    - View Feed: GET /feed?location={}&distance={} -> User[]
        - client can load and show one by one. Keep some pool in UI to avoid api call when swipe happens
        - No need for pagination as client can request more recommendations when existing list exhausted
    - Swips: POST /swipe/{target_user} body : { intent={Like|Dislike} }
- High level design
    - User can define filter and other users can see profile in recommendation **Challenging part**
        - Approach 1: Direct DB query
            - Not scalable and has higher latency
            - User will have to wait for good amount of time between page loads
        - Approach 2: Cache DB Response in cache and directly serve from there
            - Zero time between page loads
            - Problems in cache staleness
        - Approach 3: **Feed Cache based off user Index**
            - We need to rank profiles based on user filters. Some profile will be more relatable. This mean we need some kind of *ranking*
            - Since we are looking for nearby profiles location is main criteria and nearby user profile should reside nearby. which indicates we need to *partition data based on location*
            - Redis or ElasticSearch based solution can be used here.
            - CDC pipeline to keep data in-sync with UserDB
            - Maintan user feed cache when app opens or profiles are abount to get exhaused while swiping. Upon dehyration in cache feed load more profiles from ES/Redis.
            - Index + Pre computed feed -> makes system faster
            - Challenges: Complexity, reliable CDC, Density uneven distribution for dense location users, Cache staleness
    - left and Right swipe
        - Upon user swipe POST api will be triggered
        - Our system is write heavy and lot of swipe data is expected to store for swiping_user -> target_user and intent.
        - *Cassandra* will be a good choice here as its write optimised and data can be partitioned using swiping_user.
        - Challenges: Eventual consistency with Cassandra
    - Match Notification
        - When swipe request comes we can check for stored intent by target user. If we find intent=yes with incoming intent=yes then we can trigger notification workflow by other service
- Deep Dives
    - How to avoid showing user same profile again ?
        - Approach 1: DB query. 
            - Efficient as data is partitioned based on userid
            - If user has long swipe history then checking the data might be not trivial
            - If system is eventual consistent DB might not return data which user has just seen which may trigger showing same profiles again
        - Approach 2: Approach 1 + client side cache for recently viewed profiles
            - client can maintain last K swipes and filter any swipe getting repeated
        - Approach 3: Cache + Contains check in DB + Bloom Filter
            - For users with large swipe history beyond certain threshold we can maintain Bloom filter
            - It will accurately say if user_id is shown to user or not but can also say its shown which was not shown actually. Hence, False Positive. We might never show some profiles to user
            - We can tune Bloom filter to reduce error percentage
            - Challenges
                - Cache Bllom filter needs to be updated upon every swipe
                - If node goes down needs recalaulation of Bloom filter which can be expensive
    - Cache staleness: When user changes criteria or changes location and no longer meets feed criteria
        - Since data is cached we need someway to remove out of criteria matcher users from feed
        - What happens when user suggested in feed changes the criteria..?
        - Have Cache TTL < 1 Hr. Background worker will preiodically calculate feed and cache it for Active users
        - We can also tune no of profiles to cache in cache
        - When requesting user updates criteria at that time as well we can selectively choose to re-calculate feed.
        - *Tip*: having tunable parameters in system like redis ttl, profiles count in cache, set of users to calcuate feed for can be udeful to find optimised parameter tuning
    - Reliable match creation ?
        - Since system is eventual consistent how to avoid match miss as data replication can take some time ?
        - We can have cached data for user -> yes swipes and can be partitioned by user_id to accomodate for replication delays
- References
    - https://www.hellointerview.com/learn/system-design/answer-keys/tinder

# Design Tweet Search
- Functional Requirements
    - User should be able to search by any keyword
    - User should be able to sort by likes/time
    - User should be able to search results in pages upto 100
- Non Functional Requirements
    - Availability over consistency
    - Read requests >>> New tweets
    - Low latency while serving data
        - New tweets can be served in < 30 sec
        - All tweets should be discoverable even older/unpopular ones
    - Should support high volume of requests
- Scale
    - 100M users -> 1 tweet/day, 10 likes/day and 1 search /day
    - For simplicity lets assume 100k seconds per day
    - Writes/sec = 100M * 1 per day / 100k seconds = 1k tweets/second
    - Likes/sec = 100M * 10 per day / 100k seconds = 10k tweet likes/second
    - Search/sec = 100M * 1 per day / 100k seconds = 1k tweet search/second
    - Storage: 10 years* 365 days * 100M/day * 1kb => 365 TB of total tweets
- Core Entities
    - Tweet
    - Likes
    - Search term/keyword
- APIs
    - GET /search?query={term}&sort_order={Likes|Time}
- High Level Design Important Points
    - Indexing our tweets
        - We need a Reverse Indes solution for this requirement
        - ES is based on Lucene which supports Horizontal scaling, Replication, Arbitory text Index creation
        - Challenges: Needs a lot of configurations to make it right. Operational overhead.
    - Handling the large volume of writes to system
        - 2 Phase architecture: Store Like count only when count reaches to the power of 2
        - So we will have only approximate logarithmic view of our data
        - Ranking service will pick top N tweets get their actual like counts and sorts before returning to user.
        - ES can maintain approximateLogCount <-- which wil reduce load on ES to update viewes
    - Pagination
        - Fetch extra pages from DB and store it in cache with 1 Hr TTL. Challenges: Large amount of memory needed in Cache
        - Re-use search result cache with timestamp. Maintain cache sorted chronological order. Get next N results from cache with timestamp < input timestamp
- References
    - https://www.hellointerview.com/learn/system-design/answer-keys/tweet-search

### References
- [Hello Interview](https://www.hellointerview.com/learn/system-design/answer-keys/leetcode)
- [System Design One](https://systemdesign.one/system-design-interview-cheatsheet/#system-design-template)
- [YouTube: System Design Interview](https://www.youtube.com/@SystemDesignInterview)
- [YouTube: Tech Dummies Narendra L](https://www.youtube.com/@TechDummiesNarendraL)
