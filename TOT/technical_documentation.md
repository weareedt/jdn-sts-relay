# JDN STS Relay Technical Documentation

## Project Overview
JDN STS Relay is a dual-purpose server implementation that provides:
1. A WebSocket relay server for OpenAI's Realtime API
2. An HTTP proxy server for forwarding messages to a JDN endpoint

## Technology Stack

### Core Technologies
- **Runtime Environment**: Node.js
- **Language**: JavaScript (ES6+)
- **Module System**: ES Modules (type: "module")
- **Package Manager**: npm

### Key Dependencies
- **@openai/realtime-api-beta**: OpenAI's Realtime API client
- **ws**: WebSocket server implementation
- **express**: Web framework for HTTP server
- **dotenv**: Environment configuration management
- **cors**: Cross-Origin Resource Sharing middleware

## Architecture

### 1. WebSocket Relay Server (RealtimeRelay)

#### Components
- **RealtimeRelay Class** (`lib/relay.js`)
  - Manages WebSocket connections
  - Handles bidirectional communication between browser clients and OpenAI
  - Implements message queuing for connection management
  - Provides logging functionality

#### Features
- Bidirectional event relay
- Message queuing during connection establishment
- Error handling and connection management
- Secure API key handling

### 2. HTTP Proxy Server

#### Components
- **HTTP Server** (`index.js`)
  - Handles CORS preflight requests
  - Proxies POST requests to JDN endpoint
  - Implements error handling and response forwarding

#### Features
- CORS support with preflight handling
- Secure forwarding to HTTPS endpoints
- Error handling and status code preservation
- Bearer token authentication

## Configuration

### Environment Variables
- `OPENAI_API_KEY`: Required for OpenAI API authentication
- `WS_PORT`: WebSocket server port (default: 8081)
- HTTP server runs on port 3001 (fixed)

### Endpoints
1. WebSocket: `ws://localhost:8081`
   - Main WebSocket endpoint for realtime communication
2. HTTP: `http://localhost:3001/api/forward_message`
   - POST endpoint for message forwarding

## Implementation Details

### WebSocket Communication Flow
1. Client connects to WebSocket server
2. Server authenticates with OpenAI using API key
3. Bidirectional relay established:
   - Client → OpenAI: Events parsed and forwarded
   - OpenAI → Client: Events serialized and relayed

### HTTP Proxy Flow
1. Client sends POST request
2. Server handles CORS if needed
3. Request forwarded to `aishah.jdn.gov.my`
4. Response relayed back to client

## Security Considerations

### Authentication
- OpenAI API key required for WebSocket relay
- Bearer token authentication for JDN endpoint
- Environment variable based configuration

### CORS Policy
- Configured for cross-origin requests
- Implements OPTIONS preflight handling
- Allows specific HTTP methods and headers

## Development

### Running the Server
```bash
# Install dependencies
npm install

# Start both servers
npm run dev

# Start relay server only
npm run relay
```

### Scripts
- `relay`: Starts WebSocket relay server with nodemon
- `dev`: Runs both relay server and React development server

## Error Handling

### WebSocket Server
- Connection validation
- Message parsing error handling
- OpenAI connection error management
- Graceful connection closure

### HTTP Proxy
- Request validation
- Proxy error handling
- Status code preservation
- Error response formatting
