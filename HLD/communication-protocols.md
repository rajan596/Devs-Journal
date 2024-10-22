

# HTTP

## HTTP important features/params
- `Connection: keep-alive`
    - Same established TCP connection is used for subsequent requests if server also agrees.
    - This reduces overhead of creating new tcp connection in each request, DNS lookup and SSL Handshake
    - useful for frequent HTTP request from client to server for periodic data updates like location date publish
    - https://stackoverflow.com/questions/20763999/explain-http-keep-alive-mechanism

# TCP

# UDP

# FTP

# WebRTC

# Polling techniques

### Short Polling
- Client requests server for new data at specific interval using simple HTTP request
- If data is available server respnds with data otherwise gived 204 Content not available
- Pros:
  - Simple and faster to implement
  - Useful when no realtime communication is required to client
- Cons:
  - More load on server because of periodic poll in the case when data is not available - Resource wastage
  - Latency between data availability and data reaches to client
- Alternatives: Long polling, WebSockets, Server sent Events
- References: [Understanding Short Polling in Client-Server Communication](https://substack.com/@pushkarkumar/p-147993524)

### Long Polling
- Keeps connection open between client and server as long as possible
- Server responds back when data is available. if data is not available server Holds requests and wait until data is available
- More persistent connection than short polling. Data reaches to client immediately when available
- Repeat new connection each time data is received or timeout has occurred
- Pros
  - Dont need complex infrastructure to implement
  - Simple to implement
- Cons
  - Resource intensive as it holds all client requests
  - Scalability issues

## Web Sockets
- Not based on HTTP

## Server Sent Events
- Based on HTTP
- Modern browser supports for SSE
- In this persistent connection establishes and only server can send event to client
- References
  - [Medium Article](https://medium.com/deliveryherotechhub/what-is-server-sent-events-sse-and-how-to-implement-it-904938bffd73)

```bash
GET /api/v1/live-scores   <-- simple GET request
Accept: text/event-stream <-- Indicates a stream
Cache-Control: no-cache.  <-- disable caching
Connection: keep-alive.   <-- Persisitent connecton

Response:
id: 1
event: score
data: GOAL Liverpool 1 - 1 Arsenal
data: GOAL Manchester United 3 - 3 Manchester City
```
