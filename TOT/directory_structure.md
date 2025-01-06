# Project Directory Structure

```
jdn-sts-relay/
├── lib/
│   └── relay.js          # WebSocket relay implementation
├── index.js              # Main server entry point
├── package.json          # Project configuration and dependencies
├── .env                  # Environment configuration (not in repo)
└── TOT/                  # Technical documentation
    ├── technical_documentation.md  # Detailed technical documentation
    └── directory_structure.md      # Project structure overview
```

## Key Files

### lib/relay.js
Core implementation of the WebSocket relay server that handles bidirectional communication between browser clients and OpenAI's Realtime API.

### index.js
Main entry point that:
- Initializes the WebSocket relay server
- Sets up the HTTP proxy server
- Configures environment variables
- Handles error scenarios

### package.json
Project manifest containing:
- Dependencies and devDependencies
- Script definitions
- Project metadata
- Build and development configurations

### .env
Environment configuration file containing sensitive data:
- OPENAI_API_KEY
- Optional WS_PORT override

### TOT/
Documentation directory containing:
- Comprehensive technical documentation
- Project structure and organization details
