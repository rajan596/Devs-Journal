# Authentication Mechanisms

## JWT based authentication

- Sample API Request and Response
```bash
POST /auth/login HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "username": "john.doe",
  "password": "mySecurePassword123"
}

Response:- 
{
  "token_type": "Bearer",
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJqb2huLmRvZSIsIm5hbWUiOiJKb2huIERvZSIsInJvbGUiOiJ1c2VyIiwiaWF0IjoxNzAwMDAwMDAwLCJleHAiOjE3MDAwMDM2MDB9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
  "refresh_token": "def50200a8c4f2b3e9d1c0a9b8c7d6e5f4g3h2i1j0k9l8m7n6o5p4q3r2s1t0",
  "expires_in": 3600,
  "user": {
    "id": 12345,
    "username": "john.doe",
    "email": "john@example.com",
    "role": "user"
  }
}

GET /api/user/profile HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJqb2huLmRvZSIsIm5hbWUiOiJKb2huIERvZSIsInJvbGUiOiJ1c2VyIiwiaWF0IjoxNzAwMDAwMDAwLCJleHAiOjE3MDAwMDM2MDB9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

POST /api/posts HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "title": "My First Blog Post",
  "content": "This is the content of my blog post.",
  "tags": ["tech", "tutorial"]
}
```
- There are libraries which can be used to implement JWT based auth workflows

### Refresh token Managemenet
- Server side
    - Refresh token is stored into persistent storage
    - Encryption at rest is mandatory. Hash of refresh token is stored into database.
    - Token rotation is required after use or expiry. Every time a refresh token is used, it's immediately invalidated and a new one is issued. This is called refresh token rotation and is the industry standard
- Client side
    - Web applications: Store refresh tokens in HTTP-only, Secure, SameSite cookies
    - Mobile apps: Use platform-specific secure storage
        - iOS: Keychain with kSecAttrAccessibleWhenUnlockedThisDeviceOnly
        - Android: EncryptedSharedPreferences or Android Keystore

### JWT Structure
- The signature is created by encoding the header and payload with a secret key, ensuring the token hasn't been tampered with

```bash
{
  "alg": "HS256",
  "typ": "JWT"
}

{
  "sub": "john.doe",
  "name": "John Doe",
  "role": "user",
  "iat": 1700000000,
  "exp": 1700003600
}

```
- Access token vs Refresh token
    - Access tokens are short-lived credentials (typically 5-60 minutes) used to directly access protected resources and APIs.
    - Refresh tokens are long-lived credentials (typically 7-90 days or longer) used exclusively to obtain new access tokens when the current one expires.
    - When an access token expires, the client sends the refresh token to a dedicated endpoint that issues a fresh access token *without requiring user re-authentication*
    - Refresh token is stored securely on the authorization server
    - How They Work Together
        - The authentication flow follows this pattern:​
        - User logs in successfully with credentials
        - Server issues both an access token (15 minutes) and refresh token (30 days)
        - Client stores access token in memory and refresh token in HTTP-only cookie
        - Client makes API requests using: Authorization: Bearer <access_token>
        - After 15 minutes, access token expires and API returns 401 Unauthorized
        - Client automatically sends refresh token to /auth/refresh endpoint
        - Server validates refresh token and issues new access token (and optionally new refresh token)
        - Client continues making requests with new access token
        - Process repeats until refresh token expires after 30 days, requiring full re-login