# WebSockets & Real-time Communication Complete Cheat Sheet

## Table of Contents
- [WebSocket Fundamentals](#websocket-fundamentals)
- [Client-Side WebSockets](#client-side-websockets)
- [Server-Side WebSockets](#server-side-websockets)
- [Real-time Patterns](#real-time-patterns)
- [Message Protocols](#message-protocols)
- [Error Handling & Reconnection](#error-handling--reconnection)
- [Scaling & Performance](#scaling--performance)
- [Alternative Technologies](#alternative-technologies)
- [Security Considerations](#security-considerations)
- [Best Practices](#best-practices)

## WebSocket Fundamentals

### What are WebSockets?
- **Full-duplex** communication channel over a single TCP connection
- **Persistent connection** between client and server
- **Low latency** real-time data transfer
- **Bidirectional** communication (both client and server can send messages)

### WebSocket vs HTTP
```javascript
// HTTP (Request-Response)
Client -> Request -> Server
Client <- Response <- Server

// WebSocket (Full-duplex)
Client <-> Bidirectional <-> Server
```

### WebSocket URLs
```javascript
// WebSocket URL schemes
ws://example.com:8080/chat    // Unencrypted
wss://example.com:443/chat    // Encrypted (TLS)

// Handshake process
1. HTTP Upgrade request
2. Server responds with 101 Switching Protocols
3. Connection upgraded to WebSocket
```

## Client-Side WebSockets

### Basic WebSocket Connection
```javascript
// Creating WebSocket connection
const socket = new WebSocket('wss://echo.websocket.org');

// Connection events
socket.onopen = (event) => {
    console.log('Connected to WebSocket server');
    socket.send('Hello Server!');
};

socket.onmessage = (event) => {
    console.log('Message from server:', event.data);
};

socket.onclose = (event) => {
    console.log('Disconnected from server:', event.code, event.reason);
};

socket.onerror = (error) => {
    console.error('WebSocket error:', error);
};
```

### Advanced Client Implementation
```javascript
class WebSocketClient {
    constructor(url, options = {}) {
        this.url = url;
        this.options = options;
        this.socket = null;
        this.reconnectAttempts = 0;
        this.maxReconnectAttempts = 5;
        this.reconnectInterval = 1000;
        
        this.messageHandlers = new Map();
        this.connect();
    }
    
    connect() {
        try {
            this.socket = new WebSocket(this.url);
            
            this.socket.onopen = (event) => {
                console.log('WebSocket connected');
                this.reconnectAttempts = 0;
                this.onConnect(event);
            };
            
            this.socket.onmessage = (event) => {
                this.handleMessage(event);
            };
            
            this.socket.onclose = (event) => {
                console.log('WebSocket disconnected');
                this.onDisconnect(event);
                this.handleReconnection();
            };
            
            this.socket.onerror = (error) => {
                console.error('WebSocket error:', error);
                this.onError(error);
            };
            
        } catch (error) {
            console.error('Failed to create WebSocket:', error);
        }
    }
    
    handleMessage(event) {
        try {
            const data = JSON.parse(event.data);
            const { type, payload } = data;
            
            // Call specific handler for message type
            if (this.messageHandlers.has(type)) {
                this.messageHandlers.get(type)(payload);
            }
            
            // Call general message handler
            this.onMessage(data);
            
        } catch (error) {
            console.error('Error parsing message:', error);
        }
    }
    
    send(type, payload) {
        if (this.socket && this.socket.readyState === WebSocket.OPEN) {
            const message = JSON.stringify({ type, payload });
            this.socket.send(message);
        } else {
            console.warn('WebSocket not connected');
        }
    }
    
    on(type, handler) {
        this.messageHandlers.set(type, handler);
    }
    
    handleReconnection() {
        if (this.reconnectAttempts < this.maxReconnectAttempts) {
            this.reconnectAttempts++;
            const delay = this.reconnectInterval * Math.pow(2, this.reconnectAttempts - 1);
            
            console.log(`Reconnecting in ${delay}ms... (attempt ${this.reconnectAttempts})`);
            
            setTimeout(() => {
                this.connect();
            }, delay);
        } else {
            console.error('Max reconnection attempts reached');
        }
    }
    
    // Lifecycle methods to be overridden
    onConnect(event) {}
    onDisconnect(event) {}
    onError(error) {}
    onMessage(data) {}
    
    close() {
        if (this.socket) {
            this.socket.close(1000, 'Client initiated close');
        }
    }
}

// Usage
const client = new WebSocketClient('wss://api.example.com/ws');

client.on('chat_message', (payload) => {
    console.log('New chat message:', payload);
});

client.on('user_joined', (payload) => {
    console.log('User joined:', payload.username);
});

client.send('join_room', { roomId: 'general' });
```

### React Hook for WebSockets
```javascript
import { useState, useEffect, useRef, useCallback } from 'react';

function useWebSocket(url, options = {}) {
    const [isConnected, setIsConnected] = useState(false);
    const [lastMessage, setLastMessage] = useState(null);
    const [error, setError] = useState(null);
    const socketRef = useRef(null);
    const reconnectCountRef = useRef(0);
    
    const connect = useCallback(() => {
        try {
            socketRef.current = new WebSocket(url);
            
            socketRef.current.onopen = () => {
                setIsConnected(true);
                setError(null);
                reconnectCountRef.current = 0;
                options.onOpen?.();
            };
            
            socketRef.current.onmessage = (event) => {
                const data = JSON.parse(event.data);
                setLastMessage(data);
                options.onMessage?.(data);
            };
            
            socketRef.current.onclose = (event) => {
                setIsConnected(false);
                options.onClose?.(event);
                
                // Auto-reconnect
                if (reconnectCountRef.current < (options.maxReconnectAttempts || 5)) {
                    reconnectCountRef.current++;
                    setTimeout(connect, options.reconnectInterval || 1000);
                }
            };
            
            socketRef.current.onerror = (error) => {
                setError(error);
                options.onError?.(error);
            };
            
        } catch (error) {
            setError(error);
        }
    }, [url, options]);
    
    const sendMessage = useCallback((type, payload) => {
        if (socketRef.current && socketRef.current.readyState === WebSocket.OPEN) {
            socketRef.current.send(JSON.stringify({ type, payload }));
        }
    }, []);
    
    const disconnect = useCallback(() => {
        if (socketRef.current) {
            socketRef.current.close(1000, 'Client disconnected');
        }
    }, []);
    
    useEffect(() => {
        connect();
        
        return () => {
            disconnect();
        };
    }, [connect, disconnect]);
    
    return {
        isConnected,
        lastMessage,
        error,
        sendMessage,
        disconnect,
        reconnect: connect
    };
}

// Usage in React component
function ChatRoom() {
    const { isConnected, lastMessage, sendMessage } = useWebSocket(
        'wss://api.example.com/chat',
        {
            onOpen: () => console.log('Connected to chat'),
            onMessage: (data) => console.log('New message:', data),
            maxReconnectAttempts: 3
        }
    );
    
    const handleSendMessage = (text) => {
        sendMessage('chat_message', { text, timestamp: Date.now() });
    };
    
    return (
        <div>
            <div>Status: {isConnected ? 'Connected' : 'Disconnected'}</div>
            {/* Chat UI */}
        </div>
    );
}
```

## Server-Side WebSockets

### Node.js with ws Library
```javascript
const WebSocket = require('ws');
const http = require('http');

// Create HTTP server
const server = http.createServer();
const wss = new WebSocket.Server({ server });

// Connection management
const clients = new Map();

wss.on('connection', (ws, request) => {
    const clientId = generateId();
    const clientInfo = {
        id: clientId,
        ws,
        ip: request.socket.remoteAddress,
        connectedAt: new Date()
    };
    
    clients.set(clientId, clientInfo);
    
    console.log(`Client ${clientId} connected`);
    
    // Send welcome message
    ws.send(JSON.stringify({
        type: 'welcome',
        payload: { clientId, message: 'Connected to server' }
    }));
    
    // Broadcast user joined
    broadcast({
        type: 'user_joined',
        payload: { clientId, userCount: clients.size }
    }, clientId);
    
    // Handle messages
    ws.on('message', (data) => {
        try {
            const message = JSON.parse(data);
            handleMessage(clientId, message);
        } catch (error) {
            console.error('Error parsing message:', error);
            sendError(clientId, 'Invalid message format');
        }
    });
    
    // Handle disconnection
    ws.on('close', (code, reason) => {
        clients.delete(clientId);
        console.log(`Client ${clientId} disconnected`);
        
        // Broadcast user left
        broadcast({
            type: 'user_left',
            payload: { clientId, userCount: clients.size }
        });
    });
    
    // Handle errors
    ws.on('error', (error) => {
        console.error(`WebSocket error for client ${clientId}:`, error);
    });
});

function handleMessage(clientId, message) {
    const client = clients.get(clientId);
    if (!client) return;
    
    const { type, payload } = message;
    
    switch (type) {
        case 'chat_message':
            // Broadcast chat message to all clients
            broadcast({
                type: 'chat_message',
                payload: {
                    clientId,
                    text: payload.text,
                    timestamp: Date.now()
                }
            });
            break;
            
        case 'private_message':
            // Send private message to specific client
            const targetClient = clients.get(payload.targetClientId);
            if (targetClient) {
                sendToClient(targetClient.id, {
                    type: 'private_message',
                    payload: {
                        from: clientId,
                        text: payload.text,
                        timestamp: Date.now()
                    }
                });
            }
            break;
            
        default:
            console.warn(`Unknown message type: ${type}`);
    }
}

function broadcast(message, excludeClientId = null) {
    const data = JSON.stringify(message);
    
    clients.forEach((client, clientId) => {
        if (clientId !== excludeClientId && client.ws.readyState === WebSocket.OPEN) {
            client.ws.send(data);
        }
    });
}

function sendToClient(clientId, message) {
    const client = clients.get(clientId);
    if (client && client.ws.readyState === WebSocket.OPEN) {
        client.ws.send(JSON.stringify(message));
    }
}

function sendError(clientId, error) {
    sendToClient(clientId, {
        type: 'error',
        payload: { message: error }
    });
}

function generateId() {
    return Math.random().toString(36).substr(2, 9);
}

// Start server
server.listen(8080, () => {
    console.log('WebSocket server running on port 8080');
});
```

### Python with FastAPI WebSockets
```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from typing import List, Dict
import json
import asyncio
import uuid

app = FastAPI()

class ConnectionManager:
    def __init__(self):
        self.active_connections: Dict[str, WebSocket] = {}
        self.client_info: Dict[str, Dict] = {}
    
    async def connect(self, websocket: WebSocket, client_id: str):
        await websocket.accept()
        self.active_connections[client_id] = websocket
        self.client_info[client_id] = {
            "connected_at": asyncio.get_event_loop().time(),
            "ip": websocket.client.host if websocket.client else "unknown"
        }
    
    def disconnect(self, client_id: str):
        if client_id in self.active_connections:
            del self.active_connections[client_id]
        if client_id in self.client_info:
            del self.client_info[client_id]
    
    async def send_personal_message(self, message: dict, client_id: str):
        if client_id in self.active_connections:
            await self.active_connections[client_id].send_text(json.dumps(message))
    
    async def broadcast(self, message: dict, exclude_client_id: str = None):
        disconnected = []
        for client_id, websocket in self.active_connections.items():
            if client_id != exclude_client_id:
                try:
                    await websocket.send_text(json.dumps(message))
                except Exception:
                    disconnected.append(client_id)
        
        # Clean up disconnected clients
        for client_id in disconnected:
            self.disconnect(client_id)

manager = ConnectionManager()

@app.websocket("/ws/{client_id}")
async def websocket_endpoint(websocket: WebSocket, client_id: str):
    await manager.connect(websocket, client_id)
    
    try:
        # Send welcome message
        await manager.send_personal_message({
            "type": "welcome",
            "payload": {"client_id": client_id, "message": "Connected to server"}
        }, client_id)
        
        # Broadcast user joined
        await manager.broadcast({
            "type": "user_joined",
            "payload": {"client_id": client_id, "user_count": len(manager.active_connections)}
        }, client_id)
        
        while True:
            data = await websocket.receive_text()
            
            try:
                message = json.loads(data)
                await handle_message(client_id, message)
            except json.JSONDecodeError:
                await manager.send_personal_message({
                    "type": "error",
                    "payload": {"message": "Invalid JSON format"}
                }, client_id)
                
    except WebSocketDisconnect:
        manager.disconnect(client_id)
        await manager.broadcast({
            "type": "user_left",
            "payload": {"client_id": client_id, "user_count": len(manager.active_connections)}
        })

async def handle_message(client_id: str, message: dict):
    message_type = message.get("type")
    payload = message.get("payload", {})
    
    if message_type == "chat_message":
        await manager.broadcast({
            "type": "chat_message",
            "payload": {
                "client_id": client_id,
                "text": payload.get("text", ""),
                "timestamp": asyncio.get_event_loop().time()
            }
        })
    
    elif message_type == "private_message":
        target_client_id = payload.get("target_client_id")
        if target_client_id in manager.active_connections:
            await manager.send_personal_message({
                "type": "private_message",
                "payload": {
                    "from": client_id,
                    "text": payload.get("text", ""),
                    "timestamp": asyncio.get_event_loop().time()
                }
            }, target_client_id)
    
    else:
        await manager.send_personal_message({
            "type": "error",
            "payload": {"message": f"Unknown message type: {message_type}"}
        }, client_id)

# Run with: uvicorn main:app --reload --port 8000
```

### Python with websockets Library
```python
import asyncio
import websockets
import json
import logging
from typing import Set, Dict

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class WebSocketServer:
    def __init__(self):
        self.connections: Set[websockets.WebSocketServerProtocol] = set()
        self.client_info: Dict[websockets.WebSocketServerProtocol, Dict] = {}
    
    async def register(self, websocket: websockets.WebSocketServerProtocol):
        self.connections.add(websocket)
        client_id = id(websocket)
        self.client_info[websocket] = {
            "id": client_id,
            "connected_at": asyncio.get_event_loop().time()
        }
        logger.info(f"Client {client_id} connected")
        
        # Send welcome message
        await websocket.send(json.dumps({
            "type": "welcome",
            "payload": {"client_id": client_id}
        }))
        
        # Broadcast user joined
        await self.broadcast({
            "type": "user_joined",
            "payload": {"client_id": client_id, "user_count": len(self.connections)}
        }, exclude=websocket)
    
    async def unregister(self, websocket: websockets.WebSocketServerProtocol):
        if websocket in self.connections:
            self.connections.remove(websocket)
            client_id = self.client_info[websocket]["id"]
            del self.client_info[websocket]
            logger.info(f"Client {client_id} disconnected")
            
            # Broadcast user left
            await self.broadcast({
                "type": "user_left",
                "payload": {"client_id": client_id, "user_count": len(self.connections)}
            })
    
    async def broadcast(self, message: dict, exclude: websockets.WebSocketServerProtocol = None):
        if self.connections:
            disconnected = []
            message_str = json.dumps(message)
            
            for connection in self.connections:
                if connection != exclude:
                    try:
                        await connection.send(message_str)
                    except websockets.exceptions.ConnectionClosed:
                        disconnected.append(connection)
            
            # Clean up disconnected clients
            for connection in disconnected:
                await self.unregister(connection)
    
    async def handle_message(self, websocket: websockets.WebSocketServerProtocol, message: str):
        try:
            data = json.loads(message)
            message_type = data.get("type")
            payload = data.get("payload", {})
            
            client_id = self.client_info[websocket]["id"]
            
            if message_type == "chat_message":
                await self.broadcast({
                    "type": "chat_message",
                    "payload": {
                        "client_id": client_id,
                        "text": payload.get("text", ""),
                        "timestamp": asyncio.get_event_loop().time()
                    }
                })
            
            elif message_type == "ping":
                await websocket.send(json.dumps({
                    "type": "pong",
                    "payload": {"timestamp": asyncio.get_event_loop().time()}
                }))
            
            else:
                await websocket.send(json.dumps({
                    "type": "error",
                    "payload": {"message": f"Unknown message type: {message_type}"}
                }))
                
        except json.JSONDecodeError:
            await websocket.send(json.dumps({
                "type": "error",
                "payload": {"message": "Invalid JSON format"}
            }))
    
    async def handler(self, websocket: websockets.WebSocketServerProtocol, path: str):
        await self.register(websocket)
        try:
            async for message in websocket:
                await self.handle_message(websocket, message)
        except websockets.exceptions.ConnectionClosed:
            pass
        finally:
            await self.unregister(websocket)

# Start server
async def main():
    server = WebSocketServer()
    async with websockets.serve(server.handler, "localhost", 8765):
        logger.info("WebSocket server started on ws://localhost:8765")
        await asyncio.Future()  # run forever

if __name__ == "__main__":
    asyncio.run(main())
```

## Real-time Patterns

### Pub/Sub Pattern
```javascript
class PubSub {
    constructor() {
        this.subscriptions = new Map();
    }
    
    subscribe(channel, callback) {
        if (!this.subscriptions.has(channel)) {
            this.subscriptions.set(channel, new Set());
        }
        this.subscriptions.get(channel).add(callback);
        
        // Return unsubscribe function
        return () => {
            this.unsubscribe(channel, callback);
        };
    }
    
    unsubscribe(channel, callback) {
        if (this.subscriptions.has(channel)) {
            this.subscriptions.get(channel).delete(callback);
            
            // Clean up empty channels
            if (this.subscriptions.get(channel).size === 0) {
                this.subscriptions.delete(channel);
            }
        }
    }
    
    publish(channel, data) {
        if (this.subscriptions.has(channel)) {
            this.subscriptions.get(channel).forEach(callback => {
                try {
                    callback(data);
                } catch (error) {
                    console.error('Error in subscription callback:', error);
                }
            });
        }
    }
}

// Usage with WebSockets
class WebSocketPubSub extends PubSub {
    constructor(webSocketClient) {
        super();
        this.client = webSocketClient;
        
        this.client.onMessage((data) => {
            if (data.type === 'pubsub') {
                this.publish(data.channel, data.payload);
            }
        });
    }
    
    subscribe(channel, callback) {
        const unsubscribe = super.subscribe(channel, callback);
        
        // Notify server about subscription
        this.client.send('subscribe', { channel });
        
        // Return enhanced unsubscribe
        return () => {
            unsubscribe();
            this.client.send('unsubscribe', { channel });
        };
    }
    
    publish(channel, data) {
        this.client.send('publish', { channel, data });
    }
}
```

### Room-Based Communication
```javascript
class RoomManager {
    constructor() {
        this.rooms = new Map(); // roomId -> Set of clientIds
        this.clientRooms = new Map(); // clientId -> Set of roomIds
    }
    
    joinRoom(clientId, roomId) {
        // Initialize room if it doesn't exist
        if (!this.rooms.has(roomId)) {
            this.rooms.set(roomId, new Set());
        }
        
        // Add client to room
        this.rooms.get(roomId).add(clientId);
        
        // Track room membership for client
        if (!this.clientRooms.has(clientId)) {
            this.clientRooms.set(clientId, new Set());
        }
        this.clientRooms.get(clientId).add(roomId);
        
        return this.rooms.get(roomId).size; // Return room member count
    }
    
    leaveRoom(clientId, roomId) {
        // Remove client from room
        if (this.rooms.has(roomId)) {
            this.rooms.get(roomId).delete(clientId);
            
            // Clean up empty rooms
            if (this.rooms.get(roomId).size === 0) {
                this.rooms.delete(roomId);
            }
        }
        
        // Remove room from client's membership
        if (this.clientRooms.has(clientId)) {
            this.clientRooms.get(clientId).delete(roomId);
            
            // Clean up client if no rooms
            if (this.clientRooms.get(clientId).size === 0) {
                this.clientRooms.delete(clientId);
            }
        }
    }
    
    leaveAllRooms(clientId) {
        if (this.clientRooms.has(clientId)) {
            const rooms = Array.from(this.clientRooms.get(clientId));
            rooms.forEach(roomId => this.leaveRoom(clientId, roomId));
        }
    }
    
    getRoomMembers(roomId) {
        return this.rooms.has(roomId) 
            ? Array.from(this.rooms.get(roomId))
            : [];
    }
    
    getClientRooms(clientId) {
        return this.clientRooms.has(clientId)
            ? Array.from(this.clientRooms.get(clientId))
            : [];
    }
    
    broadcastToRoom(roomId, message, excludeClientId = null) {
        const members = this.getRoomMembers(roomId);
        return members.filter(clientId => clientId !== excludeClientId);
    }
}
```

## Message Protocols

### Structured Message Format
```javascript
// Message structure
const message = {
    id: 'msg_123456',           // Unique message ID
    type: 'chat_message',       // Message type for routing
    timestamp: 1627891234567,   // Message timestamp
    payload: {                  // Actual message data
        text: 'Hello world',
        userId: 'user_123',
        roomId: 'general'
    },
    metadata: {                 // Additional metadata
        version: '1.0',
        source: 'web_client'
    }
};

// Message type catalog
const MESSAGE_TYPES = {
    // Connection management
    CONNECT: 'connect',
    DISCONNECT: 'disconnect',
    PING: 'ping',
    PONG: 'pong',
    
    // Chat
    CHAT_MESSAGE: 'chat_message',
    TYPING_START: 'typing_start',
    TYPING_END: 'typing_end',
    
    // Presence
    USER_JOINED: 'user_joined',
    USER_LEFT: 'user_left',
    USER_ONLINE: 'user_online',
    USER_OFFLINE: 'user_offline',
    
    // Rooms
    JOIN_ROOM: 'join_room',
    LEAVE_ROOM: 'leave_room',
    ROOM_MESSAGE: 'room_message',
    
    // Notifications
    NOTIFICATION: 'notification',
    
    // Errors
    ERROR: 'error'
};
```

### Binary Data Transfer
```javascript
// Sending binary data
function sendBinaryData(websocket, data) {
    if (websocket.readyState === WebSocket.OPEN) {
        if (data instanceof ArrayBuffer) {
            websocket.send(data);
        } else if (data instanceof Blob) {
            websocket.send(data);
        } else {
            // Convert to ArrayBuffer
            const encoder = new TextEncoder();
            const buffer = encoder.encode(JSON.stringify(data));
            websocket.send(buffer);
        }
    }
}

// Receiving binary data
websocket.onmessage = (event) => {
    if (event.data instanceof ArrayBuffer) {
        // Handle binary data
        const decoder = new TextDecoder();
        const text = decoder.decode(event.data);
        const data = JSON.parse(text);
        handleMessage(data);
    } else {
        // Handle text data
        const data = JSON.parse(event.data);
        handleMessage(data);
    }
};

// File transfer example
function sendFile(websocket, file) {
    const reader = new FileReader();
    
    reader.onload = (event) => {
        const arrayBuffer = event.target.result;
        
        // Send file metadata first
        websocket.send(JSON.stringify({
            type: 'file_metadata',
            payload: {
                name: file.name,
                type: file.type,
                size: file.size
            }
        }));
        
        // Send file data in chunks
        const chunkSize = 16384; // 16KB chunks
        const totalChunks = Math.ceil(arrayBuffer.byteLength / chunkSize);
        
        for (let chunkIndex = 0; chunkIndex < totalChunks; chunkIndex++) {
            const start = chunkIndex * chunkSize;
            const end = Math.min(start + chunkSize, arrayBuffer.byteLength);
            const chunk = arrayBuffer.slice(start, end);
            
            websocket.send(JSON.stringify({
                type: 'file_chunk',
                payload: {
                    chunkIndex,
                    totalChunks,
                    data: Array.from(new Uint8Array(chunk))
                }
            }));
        }
    };
    
    reader.readAsArrayBuffer(file);
}
```

## Error Handling & Reconnection

### Robust Reconnection Strategy
```javascript
class RobustWebSocket {
    constructor(url, options = {}) {
        this.url = url;
        this.options = {
            maxReconnectAttempts: 5,
            reconnectDelay: 1000,
            heartbeatInterval: 30000,
            heartbeatTimeout: 5000,
            ...options
        };
        
        this.socket = null;
        this.reconnectAttempts = 0;
        this.heartbeatInterval = null;
        this.heartbeatTimeout = null;
        this.isExplicitClose = false;
        
        this.connect();
    }
    
    connect() {
        try {
            this.socket = new WebSocket(this.url);
            this.setupEventHandlers();
        } catch (error) {
            this.handleError(error);
        }
    }
    
    setupEventHandlers() {
        this.socket.onopen = (event) => {
            console.log('WebSocket connected');
            this.reconnectAttempts = 0;
            this.startHeartbeat();
            this.onOpen(event);
        };
        
        this.socket.onmessage = (event) => {
            // Reset heartbeat timeout on any message
            this.resetHeartbeatTimeout();
            
            try {
                const data = JSON.parse(event.data);
                
                // Handle heartbeat responses
                if (data.type === 'pong') {
                    this.handlePong(data);
                    return;
                }
                
                this.onMessage(data);
            } catch (error) {
                this.handleError(error);
            }
        };
        
        this.socket.onclose = (event) => {
            console.log('WebSocket disconnected:', event.code, event.reason);
            this.stopHeartbeat();
            this.onClose(event);
            
            if (!this.isExplicitClose) {
                this.handleReconnection();
            }
        };
        
        this.socket.onerror = (error) => {
            console.error('WebSocket error:', error);
            this.handleError(error);
        };
    }
    
    startHeartbeat() {
        this.stopHeartbeat();
        
        this.heartbeatInterval = setInterval(() => {
            if (this.socket.readyState === WebSocket.OPEN) {
                this.sendHeartbeat();
                this.startHeartbeatTimeout();
            }
        }, this.options.heartbeatInterval);
    }
    
    stopHeartbeat() {
        if (this.heartbeatInterval) {
            clearInterval(this.heartbeatInterval);
            this.heartbeatInterval = null;
        }
        if (this.heartbeatTimeout) {
            clearTimeout(this.heartbeatTimeout);
            this.heartbeatTimeout = null;
        }
    }
    
    sendHeartbeat() {
        this.send('ping', { timestamp: Date.now() });
    }
    
    startHeartbeatTimeout() {
        this.heartbeatTimeout = setTimeout(() => {
            console.warn('Heartbeat timeout - forcing reconnect');
            this.socket.close(); // This will trigger reconnection
        }, this.options.heartbeatTimeout);
    }
    
    resetHeartbeatTimeout() {
        if (this.heartbeatTimeout) {
            clearTimeout(this.heartbeatTimeout);
            this.startHeartbeatTimeout();
        }
    }
    
    handlePong(data) {
        const latency = Date.now() - data.payload.timestamp;
        console.log(`Heartbeat latency: ${latency}ms`);
    }
    
    handleReconnection() {
        if (this.reconnectAttempts < this.options.maxReconnectAttempts) {
            this.reconnectAttempts++;
            
            const delay = this.calculateReconnectDelay();
            console.log(`Reconnecting in ${delay}ms... (attempt ${this.reconnectAttempts})`);
            
            setTimeout(() => {
                this.connect();
            }, delay);
        } else {
            console.error('Maximum reconnection attempts reached');
            this.onMaxReconnectAttempts();
        }
    }
    
    calculateReconnectDelay() {
        // Exponential backoff with jitter
        const baseDelay = this.options.reconnectDelay;
        const maxDelay = 30000; // 30 seconds
        const delay = Math.min(baseDelay * Math.pow(2, this.reconnectAttempts - 1), maxDelay);
        const jitter = delay * 0.1 * Math.random(); // ±10% jitter
        
        return delay + jitter;
    }
    
    send(type, payload) {
        if (this.socket && this.socket.readyState === WebSocket.OPEN) {
            this.socket.send(JSON.stringify({ type, payload }));
            return true;
        }
        return false;
    }
    
    close() {
        this.isExplicitClose = true;
        this.stopHeartbeat();
        
        if (this.socket) {
            this.socket.close(1000, 'Client initiated close');
        }
    }
    
    // Lifecycle methods to be overridden
    onOpen(event) {}
    onMessage(data) {}
    onClose(event) {}
    onError(error) {}
    onMaxReconnectAttempts() {}
}
```

## Scaling & Performance

### Load Balancing with Redis
```javascript
// Using Redis for horizontal scaling
const redis = require('redis');
const { createClient } = redis;

class RedisWebSocketManager {
    constructor() {
        this.publisher = createClient();
        this.subscriber = createClient();
        
        this.setupRedis();
    }
    
    async setupRedis() {
        await this.publisher.connect();
        await this.subscriber.connect();
        
        // Subscribe to relevant channels
        await this.subscriber.subscribe('broadcast', this.handleRedisMessage.bind(this));
        await this.subscriber.subscribe('room_*', this.handleRedisMessage.bind(this));
    }
    
    async handleRedisMessage(message, channel) {
        const data = JSON.parse(message);
        
        // Route message to appropriate WebSocket connections
        switch (data.type) {
            case 'broadcast':
                await this.broadcastToAll(data.payload);
                break;
            case 'room_message':
                await this.broadcastToRoom(data.roomId, data.payload);
                break;
            case 'user_message':
                await this.sendToUser(data.userId, data.payload);
                break;
        }
    }
    
    async broadcastToAll(payload) {
        // This would broadcast to all connected clients in this server instance
        // Other server instances handle their own clients
        const message = JSON.stringify(payload);
        
        // Implementation depends on your WebSocket server setup
        // wss.clients.forEach(client => client.send(message));
    }
    
    async publishMessage(channel, message) {
        await this.publisher.publish(channel, JSON.stringify(message));
    }
}
```

### Connection Pool Management
```javascript
class ConnectionPool {
    constructor(maxConnections = 10000) {
        this.maxConnections = maxConnections;
        this.connections = new Map();
        this.stats = {
            totalConnections: 0,
            activeConnections: 0,
            peakConnections: 0
        };
    }
    
    addConnection(clientId, websocket) {
        if (this.connections.size >= this.maxConnections) {
            throw new Error('Maximum connections reached');
        }
        
        this.connections.set(clientId, {
            websocket,
            connectedAt: Date.now(),
            lastActivity: Date.now(),
            messageCount: 0
        });
        
        this.updateStats();
    }
    
    removeConnection(clientId) {
        this.connections.delete(clientId);
        this.updateStats();
    }
    
    getConnection(clientId) {
        const connection = this.connections.get(clientId);
        if (connection) {
            connection.lastActivity = Date.now();
        }
        return connection;
    }
    
    updateStats() {
        this.stats.activeConnections = this.connections.size;
        this.stats.peakConnections = Math.max(
            this.stats.peakConnections,
            this.stats.activeConnections
        );
    }
    
    // Clean up inactive connections
    cleanupInactiveConnections(maxInactiveTime = 300000) { // 5 minutes
        const now = Date.now();
        const inactive = [];
        
        this.connections.forEach((connection, clientId) => {
            if (now - connection.lastActivity > maxInactiveTime) {
                inactive.push(clientId);
            }
        });
        
        inactive.forEach(clientId => {
            const connection = this.connections.get(clientId);
            if (connection) {
                connection.websocket.close(1000, 'Inactive connection');
                this.removeConnection(clientId);
            }
        });
        
        return inactive.length;
    }
    
    // Get connection statistics
    getStats() {
        return {
            ...this.stats,
            totalConnections: this.stats.totalConnections,
            connectionLimit: this.maxConnections
        };
    }
}
```

## Alternative Technologies

### Server-Sent Events (SSE)
```javascript
// Client-side SSE
class SSEClient {
    constructor(url) {
        this.url = url;
        this.eventSource = null;
        this.handlers = new Map();
    }
    
    connect() {
        this.eventSource = new EventSource(this.url);
        
        this.eventSource.onopen = (event) => {
            console.log('SSE connection opened');
        };
        
        this.eventSource.onmessage = (event) => {
            try {
                const data = JSON.parse(event.data);
                this.handleMessage(data);
            } catch (error) {
                console.error('Error parsing SSE message:', error);
            }
        };
        
        this.eventSource.onerror = (error) => {
            console.error('SSE error:', error);
        };
        
        // Listen for custom events
        this.eventSource.addEventListener('chat', (event) => {
            const data = JSON.parse(event.data);
            this.handleEvent('chat', data);
        });
        
        this.eventSource.addEventListener('notification', (event) => {
            const data = JSON.parse(event.data);
            this.handleEvent('notification', data);
        });
    }
    
    handleMessage(data) {
        // Handle default messages
        console.log('SSE message:', data);
    }
    
    handleEvent(eventType, data) {
        if (this.handlers.has(eventType)) {
            this.handlers.get(eventType).forEach(handler => handler(data));
        }
    }
    
    on(eventType, handler) {
        if (!this.handlers.has(eventType)) {
            this.handlers.set(eventType, new Set());
        }
        this.handlers.get(eventType).add(handler);
    }
    
    close() {
        if (this.eventSource) {
            this.eventSource.close();
        }
    }
}

// Server-side SSE (Node.js/Express)
app.get('/events', (req, res) => {
    // Set SSE headers
    res.writeHead(200, {
        'Content-Type': 'text/event-stream',
        'Cache-Control': 'no-cache',
        'Connection': 'keep-alive',
        'Access-Control-Allow-Origin': '*'
    });
    
    // Send initial connection event
    res.write('event: connected\ndata: ' + JSON.stringify({
        message: 'Connected to SSE server',
        timestamp: Date.now()
    }) + '\n\n');
    
    // Send periodic heartbeat
    const heartbeat = setInterval(() => {
        res.write('data: ' + JSON.stringify({
            type: 'heartbeat',
            timestamp: Date.now()
        }) + '\n\n');
    }, 30000);
    
    // Handle client disconnection
    req.on('close', () => {
        clearInterval(heartbeat);
        res.end();
    });
});

// Send custom event
function sendSSEEvent(res, event, data) {
    res.write(`event: ${event}\n`);
    res.write(`data: ${JSON.stringify(data)}\n\n`);
}
```

### Socket.IO
```javascript
// Client-side Socket.IO
import io from 'socket.io-client';

const socket = io('https://api.example.com', {
    transports: ['websocket', 'polling'], // Fallback options
    reconnection: true,
    reconnectionAttempts: 5,
    reconnectionDelay: 1000,
    timeout: 20000
});

// Connection events
socket.on('connect', () => {
    console.log('Connected to server');
});

socket.on('disconnect', (reason) => {
    console.log('Disconnected:', reason);
});

socket.on('connect_error', (error) => {
    console.error('Connection error:', error);
});

// Custom events
socket.on('chat message', (data) => {
    console.log('Chat message:', data);
});

socket.on('user joined', (user) => {
    console.log('User joined:', user);
});

// Emitting events
socket.emit('join room', { roomId: 'general' });
socket.emit('chat message', { text: 'Hello!', roomId: 'general' });

// Server-side Socket.IO (Node.js)
const { Server } = require('socket.io');
const http = require('http');

const server = http.createServer();
const io = new Server(server, {
    cors: {
        origin: "https://example.com",
        methods: ["GET", "POST"]
    }
});

io.on('connection', (socket) => {
    console.log('User connected:', socket.id);
    
    // Join room
    socket.on('join room', (roomId) => {
        socket.join(roomId);
        socket.to(roomId).emit('user joined', { userId: socket.id });
    });
    
    // Handle chat messages
    socket.on('chat message', (data) => {
        io.to(data.roomId).emit('chat message', {
            ...data,
            userId: socket.id,
            timestamp: Date.now()
        });
    });
    
    // Handle disconnection
    socket.on('disconnect', () => {
        console.log('User disconnected:', socket.id);
    });
});

server.listen(3000, () => {
    console.log('Socket.IO server running on port 3000');
});
```

## Security Considerations

### Authentication & Authorization
```javascript
// JWT Authentication for WebSockets
class AuthenticatedWebSocket {
    constructor(url, token) {
        this.url = url;
        this.token = token;
        this.connect();
    }
    
    connect() {
        // Include token in query string or headers
        const authUrl = `${this.url}?token=${this.token}`;
        this.socket = new WebSocket(authUrl);
        
        // Or use custom headers (not supported in browser WebSocket API)
        // This requires using a library or handling on server side
    }
}

// Server-side authentication (Node.js with ws)
const jwt = require('jsonwebtoken');

function authenticateWebSocket(request, callback) {
    const token = getTokenFromRequest(request);
    
    if (!token) {
        return callback(new Error('No token provided'));
    }
    
    try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        callback(null, decoded);
    } catch (error) {
        callback(new Error('Invalid token'));
    }
}

function getTokenFromRequest(request) {
    // Get token from query string
    const url = new URL(request.url, `http://${request.headers.host}`);
    return url.searchParams.get('token');
    
    // Or from cookies
    // const cookies = parseCookies(request.headers.cookie);
    // return cookies.token;
}

// Usage in WebSocket server
wss.on('connection', (ws, request) => {
    authenticateWebSocket(request, (error, user) => {
        if (error) {
            ws.close(1008, 'Authentication failed');
            return;
        }
        
        // Store user info with WebSocket connection
        ws.user = user;
        handleAuthenticatedConnection(ws);
    });
});
```

### Rate Limiting
```javascript
class RateLimiter {
    constructor(options = {}) {
        this.options = {
            windowMs: 60000, // 1 minute
            maxRequests: 100,
            ...options
        };
        
        this.requests = new Map(); // clientId -> request timestamps
    }
    
    checkLimit(clientId) {
        const now = Date.now();
        const windowStart = now - this.options.windowMs;
        
        if (!this.requests.has(clientId)) {
            this.requests.set(clientId, []);
        }
        
        const clientRequests = this.requests.get(clientId);
        
        // Remove old requests outside the window
        const recentRequests = clientRequests.filter(time => time > windowStart);
        this.requests.set(clientId, recentRequests);
        
        // Check if limit exceeded
        if (recentRequests.length >= this.options.maxRequests) {
            return false;
        }
        
        // Add current request
        recentRequests.push(now);
        return true;
    }
    
    getRemainingRequests(clientId) {
        const now = Date.now();
        const windowStart = now - this.options.windowMs;
        
        if (!this.requests.has(clientId)) {
            return this.options.maxRequests;
        }
        
        const clientRequests = this.requests.get(clientId);
        const recentRequests = clientRequests.filter(time => time > windowStart);
        
        return Math.max(0, this.options.maxRequests - recentRequests.length);
    }
}

// Usage in WebSocket server
const rateLimiter = new RateLimiter({ windowMs: 60000, maxRequests: 100 });

wss.on('connection', (ws, request) => {
    const clientId = getClientId(request);
    
    ws.on('message', (data) => {
        if (!rateLimiter.checkLimit(clientId)) {
            ws.send(JSON.stringify({
                type: 'error',
                payload: { message: 'Rate limit exceeded' }
            }));
            return;
        }
        
        // Process message
        handleMessage(ws, data);
    });
});
```

## Best Practices

### Performance Optimization
```javascript
// Message batching
class MessageBatcher {
    constructor(batchSize = 10, batchTimeout = 100) {
        this.batchSize = batchSize;
        this.batchTimeout = batchTimeout;
        this.batch = [];
        this.timeout = null;
        this.callback = null;
    }
    
    setCallback(callback) {
        this.callback = callback;
    }
    
    addMessage(message) {
        this.batch.push(message);
        
        if (this.batch.length >= this.batchSize) {
            this.flush();
        } else if (!this.timeout) {
            this.timeout = setTimeout(() => this.flush(), this.batchTimeout);
        }
    }
    
    flush() {
        if (this.timeout) {
            clearTimeout(this.timeout);
            this.timeout = null;
        }
        
        if (this.batch.length > 0 && this.callback) {
            this.callback(this.batch);
            this.batch = [];
        }
    }
}

// Usage
const batcher = new MessageBatcher();
batcher.setCallback((messages) => {
    websocket.send(JSON.stringify({
        type: 'batch',
        payload: { messages }
    }));
});

// Add individual messages
batcher.addMessage({ type: 'chat', text: 'Hello' });
batcher.addMessage({ type: 'chat', text: 'World' });
```

### Monitoring & Logging
```javascript
class WebSocketMonitor {
    constructor() {
        this.stats = {
            connections: 0,
            messages: {
                sent: 0,
                received: 0,
                errors: 0
            },
            bandwidth: {
                sent: 0,
                received: 0
            }
        };
        
        this.connectionHistory = [];
    }
    
    logConnection(clientId, type) {
        this.stats.connections += type === 'connect' ? 1 : -1;
        
        this.connectionHistory.push({
            clientId,
            type,
            timestamp: Date.now()
        });
        
        // Keep only last 1000 connection events
        if (this.connectionHistory.length > 1000) {
            this.connectionHistory = this.connectionHistory.slice(-1000);
        }
    }
    
    logMessage(direction, size, success = true) {
        if (direction === 'sent') {
            this.stats.messages.sent++;
            this.stats.bandwidth.sent += size;
        } else {
            this.stats.messages.received++;
            this.stats.bandwidth.received += size;
        }
        
        if (!success) {
            this.stats.messages.errors++;
        }
    }
    
    getStats() {
        return {
            ...this.stats,
            uptime: process.uptime(),
            timestamp: Date.now()
        };
    }
    
    getConnectionStats(timeWindow = 3600000) { // 1 hour
        const windowStart = Date.now() - timeWindow;
        const recentConnections = this.connectionHistory.filter(
            event => event.timestamp > windowStart
        );
        
        const connects = recentConnections.filter(e => e.type === 'connect').length;
        const disconnects = recentConnections.filter(e => e.type === 'disconnect').length;
        
        return {
            connects,
            disconnects,
            netChange: connects - disconnects
        };
    }
}
```

---

## Quick Reference Card

### WebSocket Ready States
```javascript
WebSocket.CONNECTING = 0  // Connection is being established
WebSocket.OPEN = 1        // Connection is open and ready
WebSocket.CLOSING = 2     // Connection is being closed
WebSocket.CLOSED = 3      // Connection is closed or couldn't be opened
```

### Common Close Codes
```javascript
1000 - Normal closure
1001 - Going away
1002 - Protocol error
1003 - Unsupported data
1006 - Abnormal closure
1008 - Policy violation
1009 - Message too large
1011 - Internal error
```

### Essential Patterns
```javascript
// Heartbeat pattern
setInterval(() => {
    if (socket.readyState === WebSocket.OPEN) {
        socket.send(JSON.stringify({ type: 'ping' }));
    }
}, 30000);

// Reconnection pattern
function connectWithRetry(url, retries = 5) {
    const socket = new WebSocket(url);
    
    socket.onclose = () => {
        if (retries > 0) {
            setTimeout(() => connectWithRetry(url, retries - 1), 1000);
        }
    };
    
    return socket;
}

// Message queuing
class MessageQueue {
    constructor() {
        this.queue = [];
        this.isConnected = false;
    }
    
    send(message) {
        if (this.isConnected) {
            socket.send(message);
        } else {
            this.queue.push(message);
        }
    }
    
    onConnect() {
        this.isConnected = true;
        this.queue.forEach(message => socket.send(message));
        this.queue = [];
    }
}
```

### Pro Tips
1. **Always use WSS** (WebSocket Secure) in production
2. **Implement proper authentication** and authorization
3. **Use heartbeats** to detect dead connections
4. **Implement rate limiting** to prevent abuse
5. **Handle reconnections gracefully** with exponential backoff
6. **Use message batching** for high-frequency updates
7. **Monitor connection metrics** for performance insights
8. **Clean up resources** on connection close
9. **Validate all incoming messages** on the server
10. **Use structured message formats** with versioning

---

*Remember: WebSockets are powerful for real-time communication but require careful handling of connection state, error recovery, and security considerations. Always test your implementation under various network conditions and load scenarios!*