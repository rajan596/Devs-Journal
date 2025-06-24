# Model Context Protocol

LLMs can understand and transform user requirements like user is looking for current temprature. But LLM dont know the answer to it but it can know how to get the answer. How how ?? Simple Do an relevant API call to the server who knows the answer.

In our case we can create a server and expose end point which can query any weather site and get the data for us and gives the result back to client.

Congratulations !! We have successfully built the server.

This is just a customised server. We need to make a server in a way it follows the standard defined by MCP / using their SDKs. 

Congratulations. Now we have a MCP server with us ready.

Couple of examples of MCP server

1. Get weather information
2. Query Database
3. Get chat data from slack
4. Query interner and return the response.

Any software like chatbot can be a client for the MCP server and get the required details from the MCP server.