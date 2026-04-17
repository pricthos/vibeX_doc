<!--
{
  "navGroups": {
    "installation": "Getting Started",
    "authentication--api-keys": "Authentication",
    "quick-start": "Quick Start",
    "jwt-token-management": "JWT Management",
    "api-usage-pattern": "API Pattern",
    "user-management": "Users",
    "id-types": "ID Types",
    "chat": "Chat",
    "chat-events": "Chat Events",
    "web-push-notifications": "Web Push",
    "webrtc-videovoice-calls": "WebRTC",
    "reference": "Reference"
  }
}
-->

# TalkFlow SDK

Chat + WebRTC Integrated JavaScript SDK

TalkFlow SDK lets you quickly add **real-time chat** and **video/voice calling** to your web service. No need to implement complex WebSocket connections or WebRTC configurations yourself — build a full-featured chat experience like Kakao Talk with just a few lines of code.

---

## Installation

There are two ways to add the SDK to your project. NPM is recommended for most cases.

### NPM

If you're using a bundler like React, Vue, or Next.js, install via NPM.

```bash
npm install @vibexnpm/talkflow
```

### CDN

You can add the SDK directly to an HTML file via a script tag — no build step required. Great for quick prototyping.

```html
<!-- Latest version (recommended) -->
<script src="https://cdn.jsdelivr.net/npm/@vibexnpm/talkflow"></script>

<!-- Specific version (recommended for production) -->
<script src="https://cdn.jsdelivr.net/npm/@vibexnpm/talkflow@1.0.0"></script>

<script>
    // When loaded via CDN, access via TalkFlowSDK.default
    const client = new TalkFlowSDK.default({
        apiKey: 'CLIENT_KEY',
        projectId: 'your-project-id',
        jwtToken: '<JWT from your backend>'
    });
</script>
```

> **Production tip**: Always pin a specific version like `@1.0.0` when using CDN. Without a version, unexpected breaking changes can occur when the SDK updates.

#### Enable Web Push via CDN (Optional)

To use `enablePushNotifications()`, load the Firebase SDK **before** TalkFlow. Order matters.

```html
<!-- 1. Load Firebase first -->
<script src="https://www.gstatic.com/firebasejs/11.8.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/11.8.1/firebase-messaging-compat.js"></script>

<!-- 2. TalkFlow SDK -->
<script src="https://cdn.jsdelivr.net/npm/@vibexnpm/talkflow"></script>
```

> When installed via NPM, Firebase is included automatically — this step is not needed.

---

## Authentication & API Keys

### API Key Types

TalkFlow issues **two types of API keys** per project. It's important to use the right key in the right place.

| Key | Prefix | Location | Purpose |
|---|---|---|---|
| **Server Key** | `sk-` | Server environment variable (never expose to browser) | Full API access including JWT issuance |
| **Client Key** | `ck-` | Browser SDK (safe to expose) | Chat/call/query only |

The Client Key (`ck-`) is safe to include in browser code. Without a JWT, authenticated APIs cannot be called, and JWT issuance itself is blocked on the server side.

### Security Policy

| Policy | Description |
|---|---|
| **JWT issuance blocked for Client Key** | Calling `/api/v1/users/auth` with `ck-` returns 403 |
| **Server Key blocked in browser** | Using `sk-` in a browser in production returns 403 |
| **SDK warning** | Initializing SDK with `sk-` logs `console.warn` in dev, `console.error` otherwise |

---

## Quick Start

### Development / Prototype — Using Server Key

When first developing or testing, you can get started immediately with a Server Key. `registerUser()` handles JWT issuance and setup automatically.

```javascript
import TalkFlowClient from '@vibexnpm/talkflow';

const client = new TalkFlowClient({
    apiKey: 'sk-xxxx',             // Development Server Key
    projectId: 'your-project-id',
    env: 'development'             // Dev mode — allows sk- key
});

// Registers user and configures JWT in one call
await client.registerUser({ userId: 'user-123', nickname: 'John' });

// Start WebSocket connection (begins real-time communication)
await client.connect();

// Ready to test chat immediately
await client.sendTextMessage('room-id', 'Hello!');
```

> ⚠️ **Warning**: `registerUser()` is for development convenience only. Calling it with a Client Key (`ck-`) in production returns 403. Follow the "Production Auth Flow" below for real services.

### Production Auth Flow — Client Key + Backend JWT

In production, JWTs must be **issued by your server** for security.

Flow summary:
1. User logs into your service
2. Your server requests a JWT from TalkFlow (using Server Key)
3. TalkFlow returns the JWT to your server
4. Your server sends the JWT to the browser
5. The browser SDK connects to TalkFlow using the JWT

This ensures the Server Key is never exposed to the browser.

#### Backend Implementation Example (Node.js)

Issue a JWT from your server and return it to the browser.

```javascript
// Example: Express.js route
app.post('/api/chat-token', authenticateMyService, async (req, res) => {
    const { userId, nickname } = req.user; // Already authenticated user

    // Request JWT from TalkFlow server
    const response = await fetch('https://chat.apiorbit.net/api/v1/users/auth', {
        method: 'POST',
        headers: {
            'X-API-KEY': process.env.TALKFLOW_SERVER_KEY,   // sk- key in env var
            'X-PROJECT-ID': process.env.TALKFLOW_PROJECT_ID,
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({ userId, nickname })
    });

    const data = await response.json();
    res.json({ jwtToken: data.data.accessToken });
});
```

#### Browser SDK Initialization

Initialize the SDK with the JWT received from your server.

```javascript
import TalkFlowClient from '@vibexnpm/talkflow';

// Step 1: Get JWT from your backend
const { jwtToken } = await fetch('/api/chat-token').then(r => r.json());

// Step 2: Initialize SDK with Client Key (safe to expose in browser)
const client = new TalkFlowClient({
    apiKey: 'ck-xxxx',
    projectId: 'your-project-id',
    env: 'production'
});

// Step 3: Connect with JWT + enable web push
await client.connect(jwtToken, { enablePush: true });

// Step 4: All features are now available
await client.subscribeRoom('room-id');
client.setActiveRoom('room-id');   // Auto-mark messages as read in this room
```

#### Re-login (Switching Users)

When switching accounts, close the existing session first, then reconnect.

```javascript
await client.logout();
const { jwtToken } = await fetchNewJwt();
await client.connect(jwtToken, { enablePush: true });
```

---

## JWT Token Management

JWTs have an expiry time and must be refreshed periodically. You can swap the token without dropping the connection.

```javascript
// Set token manually (before connect(), if needed)
await client.setToken('new-jwt-token');

// Replace token only (keep connection alive)
// Safe to call for the same user without reconnecting
client.updateToken('new-jwt-token');

// Check expiry — second arg (60) means "treat as expired if < 60s remaining"
import { isJWTExpired, getJWTRemainingTime } from '@vibexnpm/talkflow';

if (isJWTExpired(token, 60)) {
    const { jwtToken } = await fetch('/api/chat-token/refresh').then(r => r.json());
    await client.setToken(jwtToken);
}

// Time remaining until expiry (milliseconds)
const remainingMs = getJWTRemainingTime(token);
```

---

## API Usage Pattern

Once initialized, a single `client` object controls everything. Methods and events all follow the `client.xxx()` pattern.

```javascript
// All features via one client
await client.subscribeRoom(roomId);       // Subscribe to a room
await client.sendMessage(roomId, data);   // Send a message
client.setActiveRoom(roomId);             // Set currently viewed room

// All events via client.on()
client.on('chatMessage', handler);        // Receive messages
client.on('messageRead', handler);        // Read receipts
```

> The `client.xxx()` interface stays stable across SDK versions. No code changes needed when upgrading.

---

## User Management

> **Important**: User registration and authentication must be handled **on your server** using the Server Key. Never do this in the browser.

You'll need a user's internal `userId` (UUID) before creating rooms or starting 1:1 chats. Make sure to familiarize yourself with user lookup before working with chat features.

```javascript
// Search users by nickname — use this to find someone for 1:1 chat
const result = await client.searchUsers({
    keyword: 'John',  // Required, max 50 chars
    limit: 20         // Max 50, default 20
});
// Use result's userId (internal UUID) when creating chat rooms

// Check if a user exists in TalkFlow
// Pass your service's member ID (externalUserId)
const exists = await client.checkUserExists('my-service-user-id');

// Get all users
const users = await client.getUsers({ size: 20 });

// Update my profile
// ⚠️ metadata is fully replaced, not merged
// Read existing metadata first if you want to preserve fields
await client.updateMyInfo({
    nickname: 'New Name',                    // Max 100 chars
    profileImageUrl: 'https://...',          // Max 500 chars
    metadata: { department: 'Engineering' }  // Entire metadata replaced
});
```

---

## ID Types

There are two ID types that are often confused. Using the wrong one will cause API failures.

| ID Type | Description | Example |
|---|---|---|
| **userId (internal UUID)** | TalkFlow-generated unique ID | `550e8400-e29b-41d4-a716-446655440000` |
| **externalUserId** | Your service's member ID (passed at registration) | `user-123`, `member_001` |

- `POST /api/v1/users/auth` → `userId` field = **your service's member ID** (externalUserId)
- `createOneToOneRoom(friendId)` → **TalkFlow internal UUID**
- `inviteToGroupRoom(userIds)` → **TalkFlow internal UUID** array
- `checkUserExists(projectUserId)` → **your service's member ID** (externalUserId)

> **Tip**: The `user.userId` in the `POST /api/v1/users/auth` response is the TalkFlow internal UUID. Store this in your DB and use it when specifying users in chat room or call APIs.

---

## Chat

### Room Management

Create, query, and configure chat rooms.

```javascript
// Get my chat room list (for the chat list screen)
const rooms = await client.getRooms({ size: 20 });

// Get only group rooms
const groupRooms = await client.getRooms({ type: 'GROUP' });

// Enter a room — returns room info + recent messages together
// Use this to load initial data when opening a chat screen
const roomDetail = await client.getRoom('room-id');
// roomDetail.room: name, participant count, etc.
// roomDetail.messages: recent message list

// Get room info only (no entry processing)
const roomInfo = await client.getRoomInfo('room-id');

// Create or get existing 1:1 room
// friendId must be a TalkFlow internal UUID
const room = await client.createOneToOneRoom('friend-user-id');

// Create a group room
const groupRoom = await client.createGroupRoom({
    roomName: 'Team Chat',                           // Required, max 100 chars
    description: 'Engineering team chat room',       // Optional, max 500 chars
    invitedUserIds: ['user-id-1', 'user-id-2'],     // TalkFlow UUIDs
    roomType: 'GROUP'                               // Default, can be omitted
});

// Create a private group room — password required to enter
// Passwords are encrypted server-side (bcrypt)
const privateRoom = await client.createGroupRoom({
    roomName: 'Private Meeting Room',
    invitedUserIds: ['user-id-1'],
    roomType: 'PRIVATE_GROUP',
    password: 'secret1234'         // Required, 4–50 chars
});

// Set message retention period
// Messages older than the period won't be visible (not deleted, just filtered)
const tempRoom = await client.createGroupRoom({
    roomName: "Today's Meeting",
    invitedUserIds: ['user-1'],
    messageRetentionHours: 24     // 1 hour to 1 year (8760h), unlimited if unset
});
```

### Joining & Subscribing to Rooms

Real-time messages require a subscription. Follow this order:

**Basic flow**:
1. Join room — `joinGroupRoom` (first time entering a group room)
2. Subscribe — `subscribeRoom` — start receiving real-time messages
3. Set active room — `setActiveRoom` — enable auto read receipts
4. On navigation away — `clearActiveRoom` — stop auto read receipts
5. On exit/unmount — `unsubscribeRoom` — stop receiving messages

```javascript
// Join a public group room (no approval needed)
await client.joinGroupRoom('room-id');

// Join a private group room — password required
await client.joinGroupRoom('room-id', 'secret1234');
// Already a participant? Password not required
// Re-joining after leaving requires the password again

// ★ Subscribe to a room — required to receive real-time messages
await client.subscribeRoom('room-id');

// ★ Set active room — auto-marks new messages as read
// Call this when the user enters the chat screen
client.setActiveRoom('room-id');

// When navigating away or returning to list — stop auto read
client.clearActiveRoom();

// Unsubscribe — stays as participant, just stops receiving
client.unsubscribeRoom('room-id');

// Leave room completely — removed from participant list
await client.leaveRoom('room-id');

// Check subscribed rooms
const subscribedRooms = client.getSubscribedRooms();
const isSubscribed = client.isSubscribed('room-id');
```

### Room List Real-time Updates

Keep the chat list screen updated in real-time, just like KakaoTalk.

**Features:**
- New messages move the room to the top of the list + increment unread count
- New rooms appear in the list immediately
- Leaving a room removes it from the list automatically
- Works correctly in multi-node/multi-instance environments (Redis Pub/Sub)

By default, subscription starts automatically when `connect()` is called.

```javascript
// Default — auto-subscribes on connect() (recommended)
await client.connect();

// Manual control
const client = new TalkFlowClient({
    apiKey: 'CLIENT_KEY',
    projectId: 'your-project-id',
    autoSubscribeRoomList: false
});
await client.subscribeRoomList();
client.unsubscribeRoomList();
client.isRoomListSubscribed();
```

#### Event Handling

```javascript
// New message — move room to top, update unread count
client.on('roomListMessage', (event) => {
    moveRoomToTop(event.roomId);
    updateLastMessage(event.roomId, event.lastMessage, event.lastMessageAt);
    // Don't increment unread for your own messages
    if (event.actorId !== currentUserId) {
        incrementUnread(event.roomId, event.unreadCountDelta);
    }
});

// New room created — add to list
client.on('roomListCreated', (event) => {
    addRoomToList(event.roomId);
});

// Someone joined — add to list if it's you, otherwise update count
client.on('roomListJoined', (event) => {
    if (event.actorId === currentUserId) {
        addRoomToList(event.roomId);
    } else {
        setParticipantCount(event.roomId, event.activeParticipantCount);
    }
});

// ★ You left a room — remove from list
client.on('roomListSelfLeft', (event) => {
    removeRoomFromList(event.roomId);
});

// Message retention expired — remove old messages from UI
client.on('retentionCleanup', ({ roomId, cutoffTime }) => {
    removeMessagesBefore(roomId, cutoffTime);
});
```

#### Event Payload Structure

```typescript
interface RoomListEvent {
    eventType: 'MESSAGE_RECEIVED' | 'ROOM_CREATED' | 'ROOM_JOINED' | 'ROOM_LEFT' | 'ROOM_UPDATED';
    roomId: string;
    actorId: string;                  // User who triggered the event
    members?: MemberInfo[];            // Users who joined/left
    activeParticipantCount?: number;  // Updated participant count
    unreadCountDelta?: number;        // Unread count increase (usually 1)
    lastMessage?: string;             // Latest message content or preview
    lastMessageType?: string;         // 'TEXT' | 'IMAGE' | 'FILE' | 'VIDEO' | 'AUDIO' | 'SYSTEM'
    lastMessageAt?: string;           // ISO 8601 timestamp
    timestamp: string;                // Event time (ISO 8601)
}
```

#### Room Subscription vs List Subscription

| | `subscribeRoom(roomId)` | `subscribeRoomList()` |
|---|---|---|
| **Used on** | Chat room screen | Chat list screen |
| **Receives** | Messages, read receipts, typing, member changes | List update metadata |
| **Auto read** | When `setActiveRoom()` is set | N/A |
| **When to call** | Every time a room is opened | Once at app start |

Both subscriptions are independent and can be active simultaneously.

### Sending Messages

#### sendMessage Parameters

| Field | Type | Required | Description |
|---|---|---|---|
| `messageId` | `string` | Required | Client-generated unique ID. UUID recommended. Retrying with the same ID prevents duplicates |
| `message` | `string` | Conditional | Text content. Max 5000 chars |
| `fileInfos` | `FileInfo[]` | Conditional | File attachment metadata. Max 20 items |
| `separateFiles` | `boolean` | Optional | Send files as individual messages (default: `true`) |
| `replyToMessageId` | `string` | Optional | Original message ID to reply to |

> Either `message` or `fileInfos` must be provided.

```javascript
// Simplest way — SDK generates messageId automatically
await client.sendTextMessage('room-id', 'Hello!');

// Specify messageId manually
// Retrying with the same ID is safe — server handles deduplication
await client.sendMessage('room-id', {
    messageId: crypto.randomUUID(),
    message: 'Hello!'
});

// File attachment
// fileUrl must be a pre-uploaded file URL
await client.sendMessage('room-id', {
    messageId: crypto.randomUUID(),
    message: 'Here is the file',
    fileInfos: [{
        fileName: 'image.jpg',
        fileUrl: 'https://your-storage.com/image.jpg',
        fileSize: 1024,              // in bytes
        fileType: 'image/jpeg'       // MIME type
    }]
});

// Reply — server automatically attaches original message content
await client.sendReply('room-id', 'I agree!', 'original-msg-id');
```

#### separateFiles Behavior

Controls how multiple files are grouped into messages.

| Value | Behavior | Messages created |
|---|---|---|
| `true` (default) | One message per file | Same as file count |
| `false` | Group files of same type together | Same as number of file types |

- **KakaoTalk style** (each photo sent individually) → `true` (default)
- **Instagram carousel style** (multiple photos as one card) → `false`

### Querying, Editing & Deleting Messages

```javascript
// Query messages — cursor-based pagination
const messages = await client.getMessages('room-id', {
    size: 50,                         // Max 100
    lastId: 'last-message-id',
    lastSortValue: 1673424645123      // epoch ms from previous response
});

// Load next page
if (messages.data.hasNext) {
    const nextPage = await client.getMessages('room-id', {
        size: 50,
        lastId: messages.data.lastId,
        lastSortValue: messages.data.lastSortValue
    });
}

// Edit your own message — reflected in real-time on recipient's screen
await client.editMessage('room-id', 'msg-id', 'Updated content');

// Delete for everyone — shows "This message was deleted" on both sides
await client.deleteMessage('room-id', 'msg-id');

// Delete for yourself only — hidden on your screen, original visible to others
await client.deleteMessage('room-id', 'msg-id', 'SELF');
```

> Only your own messages can be edited or deleted. Editing shows an "Edited" label to recipients.

### Emoji Reactions

React to messages with emoji, just like KakaoTalk reactions.

```javascript
// Add reaction
await client.addReaction('room-id', 'msg-id', '👍');

// Remove your reaction
await client.removeReaction('room-id', 'msg-id', '👍');

// reactions field example in received message:
// [{ emoji: '👍', userIds: ['user-1', 'user-2'] }, { emoji: '😂', userIds: ['user-3'] }]
```

### Pinning Messages

Pin important messages to the top of a room. Admin only.

```javascript
// Pin a message (admin required)
await client.pinMessage('room-id', 'msg-id');

// Unpin (admin required)
await client.unpinMessage('room-id');

// Check pinned message ID from room.pinnedMessageId
```

### Typing Indicators

Show "John is typing..." in real-time.

```javascript
// Connect to input event
// SDK auto-stops typing after 3 seconds of inactivity
inputField.addEventListener('input', () => {
    client.startTyping('room-id');
});

// sendMessage auto-calls stopTyping internally
client.stopTyping('room-id'); // Only call manually if needed

// Receive typing events from others (your own events are filtered out)
client.on('typing', ({ roomId, userId, userName, typing }) => {
    if (typing) {
        showTypingIndicator(`${userName} is typing...`);
    } else {
        hideTypingIndicator();
    }
});
```

> Typing events are ephemeral and not saved to DB. Subscribing to a room with `subscribeRoom()` automatically starts typing subscription too.

### Room Settings & Invites

```javascript
// Update group room settings — admin only, only specified fields are updated
await client.updateGroupRoom('room-id', {
    roomName: 'New Room Name',
    description: 'Updated description',
    messageRetentionHours: 48,
    anyoneCanInvite: true        // Allow any participant to invite
});

// Change password (PRIVATE_GROUP only)
await client.updateGroupRoom('room-id', { password: 'newPassword123' });

// Invite new users
await client.inviteToGroupRoom('room-id', ['user-id-3', 'user-id-4']);
```

---

## Chat Events

Events available after calling `subscribeRoom()` or `subscribeRoomList()`.

### Connection Events

| Event | Description |
|---|---|
| `connected` | Server connection established |
| `disconnected` | Server connection lost |
| `reconnecting` | Auto-reconnect in progress |
| `connectionError` | Connection failed |
| `stateChange` | Connection state changed |
| `tokenSet` | JWT token was set |
| `loggedOut` | Logout complete |

### Room Events

| Event | Description |
|---|---|
| `chatMessage` | Message received (includes own messages) |
| `newChatMessage` | New message received (excludes own) |
| `messageRead` | Recipient read a message — update read count UI here |
| `typing` | Someone started or stopped typing |
| `memberJoined` | Someone joined the room |
| `memberLeft` | Someone left the room |
| `roomSubscribed` | Room subscription complete |
| `roomUnsubscribed` | Room subscription cancelled |

### Room List Events

| Event | Description |
|---|---|
| `roomListUpdate` | All list events combined (branch by eventType) |
| `roomListMessage` | New message received → move room to top |
| `roomListCreated` | New room created |
| `roomListJoined` | Someone joined a room |
| `roomListLeft` | Someone left a room |
| `roomListSelfLeft` | You left a room — remove it from the list |
| `retentionCleanup` | Message retention expired → remove expired messages from UI |

---

## Web Push Notifications

Receive notifications even when the browser is closed. Uses Firebase Cloud Messaging (FCM).

### Setup

**Step 1: Place service worker file (one-time)**

The service worker must be served from the domain root due to browser security policy.

```bash
cp node_modules/@vibexnpm/talkflow/public/firebase-messaging-sw.js public/
```

**Step 2: Enable push in SDK**

```javascript
await client.connect();

// Enable push — runs asynchronously in background (non-blocking)
client.enablePushNotifications();

// Optional: listen for activation complete
client.on('pushEnabled', () => {
    console.log('Push notifications enabled');
});

// Handle foreground push — SDK auto-suppresses alerts for the active room
client.on('pushNotification', ({ title, body, data }) => {
    showInAppNotification(title, body, data.roomId);
});
```

**Step 3: Navigate to room on notification click**

```javascript
// Warm start: app is already open
navigator.serviceWorker.addEventListener('message', (event) => {
    if (event.data.type === 'NAVIGATE_TO_ROOM') {
        router.push(`/chat/${event.data.roomId}`);
    }
});

// Cold start: app opened via notification click
const pendingRoomId = await client.consumePendingRoom();
if (pendingRoomId) {
    router.push(`/chat/${pendingRoomId}`);
}
```

### enablePushNotifications Options

| Option | Type | Default | Description |
|---|---|---|---|
| `firebaseConfig` | `object` | TalkFlow built-in | Firebase initialization config |
| `vapidKey` | `string` | TalkFlow built-in | FCM Web Push VAPID public key |
| `serviceWorkerPath` | `string` | `/firebase-messaging-sw.js` | Service worker file path |

> ⚠️ **Warning**: Overriding `firebaseConfig` or `vapidKey` with your own Firebase values will not send push notifications. TalkFlow server only sends from its own Firebase project.

---

## WebRTC (Video/Voice Calls)

### Group Calls

Multi-participant video/voice calls.

```javascript
// Start call — triggers camera and microphone permission request
await client.startCall({
    roomId: 'call-room-id',
    isGroup: true,
    mediaConstraints: { video: true, audio: true }
});

// Receive remote stream — assign to video element's srcObject
client.on('remoteTrack', ({ peerId, track, streams }) => {
    const videoElement = document.getElementById(`video-${peerId}`);
    videoElement.srcObject = streams[0];
});

// End call
client.endCall();

const inCall = client.isInCall();
const participants = client.getParticipants();
```

### Call Room Management

```javascript
// Create a call room (optionally linked to a chat room)
const callRoom = await client.createCallRoom({
    roomName: 'Team Meeting',
    isGroup: true,
    chatRoomId: 'linked-chat-room-id'  // Optional
});

const roomInfo = await client.getCallRoom('call-room-id');
await client.joinCallRoomApi('call-room-id');
await client.leaveCallRoomApi('call-room-id');
```

### 1:1 Calls

```javascript
// Request a call (targetUserId = TalkFlow internal UUID)
await client.callUser('target-user-id');

// Handle incoming call
client.on('incomingCall', async ({ callerId }) => {
    if (confirm('Accept call?')) {
        await client.acceptCall(callerId);
    } else {
        client.rejectCall(callerId);
    }
});

client.on('callBusy', ({ userId }) => {
    alert('The other person is already on a call. Please try again later.');
});
```

### Media Controls

```javascript
// Toggle camera/mic — returns new state
const videoEnabled = client.toggleVideo();
const audioEnabled = client.toggleAudio();

client.setVideoEnabled(false);
client.setAudioEnabled(true);

// Screen share
await client.startScreenShare();
client.stopScreenShare();

// List available devices
const devices = await client.getDevices();
// devices.videoInputs, devices.audioInputs, devices.audioOutputs

// Switch device (e.g. front/back camera)
await client.switchDevice(devices.videoInputs[1].deviceId, 'video');

const localStream = client.getLocalStream();
```

### Video Quality Settings

```javascript
await client.applyVideoConstraints({ width: 1280, height: 720, frameRate: 30 });

const videoSettings = client.getVideoSettings();
const audioSettings = client.getAudioSettings();
```

### TURN Server Setup

TURN servers are needed for connections behind firewalls. The SDK handles this automatically in most cases.

```javascript
// Option 1: Automatic (recommended)
await client.startCall({ roomId: 'room-id' });

// Option 2: Pre-initialize — faster connection startup
await client.initializeIceServers();
await client.startCall({ roomId: 'room-id' });

// Option 3: Manual — only if you have your own TURN server
const client = new TalkFlowClient({
    apiKey: 'your-api-key',
    projectId: 'your-project-id',
    iceServers: [
        { urls: 'stun:stun.l.google.com:19302' },
        { urls: 'turn:your-turn.com:3478', username: 'user', credential: 'pass' }
    ]
});
```

### WebRTC Events

| Event | Description |
|---|---|
| `localStreamStarted` | Local camera/mic stream started |
| `remoteTrack` | Remote video/audio stream received |
| `callStarted` | Call started |
| `callEnded` | Call ended |
| `incomingCall` | Incoming 1:1 call |
| `callInvitation` | Group call invitation received |
| `callBusy` | The other party is already on a call |
| `userJoined` | Someone joined the call |
| `userLeft` | Someone left the call |
| `screenShareStarted` | Screen sharing started |
| `screenShareEnded` | Screen sharing ended |
| `mediaStateChanged` | Camera/mic on/off state changed |
| `deviceChange` | Audio/video device change detected |
| `error` | WebRTC error occurred |

---

## Reference

### Configuration Options

All options available when creating a `TalkFlowClient` instance.

```javascript
const client = new TalkFlowClient({
    // Required
    apiKey: 'your-api-key',
    projectId: 'your-project-id',

    // Auth
    jwtToken: 'your-jwt-token',

    // Environment
    env: 'production',             // 'production' | 'staging' | 'development'

    // Connection
    useSockJS: true,
    reconnectDelay: 5000,          // Reconnect delay in ms
    maxReconnectAttempts: 10,      // Max reconnect attempts

    // Room list
    autoSubscribeRoomList: true,

    // Logging
    logLevel: TalkFlowClient.LogLevel.WARN,

    // WebRTC ICE servers (optional — auto-issued by default)
    iceServers: [
        { urls: 'stun:stun.l.google.com:19302' },
        { urls: 'turn:turn.example.com:3478', username: 'user', credential: 'pass' }
    ]
});
```

### Environments

| Environment | Value | Server URL |
|---|---|---|
| Production | `production` (default) | `https://chat.vibe-x.app` |
| Staging | `staging` | `https://stg-chat.vibe-x.app` |
| Development | `development` | `http://dev-chat.apiorbit.net` |

### State Inspection

```javascript
client.hasToken();      // JWT is set (boolean)
client.isReady();       // Ready to connect
client.isConnected();   // Currently connected to server
client.getState();      // 'disconnected' | 'connecting' | 'connected' | 'reconnecting' | 'error'
client.getUserId();     // Current user's TalkFlow internal UUID
client.isInitialized(); // SDK initialized

// Full status snapshot (useful for debugging)
const status = client.getStatus();
```

### Log Level

Use `DEBUG` during development to see detailed SDK internals. Lower to `WARN` or `ERROR` in production.

```javascript
import { LogLevel } from '@vibexnpm/talkflow';

client.setLogLevel(LogLevel.DEBUG);  // All logs (recommended for dev)
client.setLogLevel(LogLevel.INFO);   // Info and above
client.setLogLevel(LogLevel.WARN);   // Warnings and above (default)
client.setLogLevel(LogLevel.ERROR);  // Errors only
client.setLogLevel(LogLevel.NONE);   // No logs
```

### Input Constraints

Server-side validation rules. Violations return a 400 error.

#### Users

| API | Field | Constraint |
|---|---|---|
| `registerUser` | `userId` | Max 255 chars |
| `registerUser` | `nickname` | Max 100 chars |
| `updateMyInfo` | `nickname` | Max 100 chars |
| `updateMyInfo` | `profileImageUrl` | Max 500 chars |
| `searchUsers` | `keyword` | Max 50 chars |
| `searchUsers` | `limit` | 1–50 (default 20) |
| `getUsers` | `size` | 1–100 (default 50) |

#### Chat Rooms

| API | Field | Constraint |
|---|---|---|
| `createGroupRoom` | `roomName` | Max 100 chars |
| `createGroupRoom` | `description` | Max 500 chars |
| `createGroupRoom` | `invitedUserIds` | Max 50 users |
| `createGroupRoom` | `password` | 4–50 chars (PRIVATE_GROUP only) |
| `createGroupRoom` | `messageRetentionHours` | 1–8760 hours (unlimited if unset) |
| `getRooms` | `size` | 1–100 (default 50) |

#### Messages

| API | Field | Constraint |
|---|---|---|
| `sendMessage` | `message` | Max 5000 chars |
| `sendMessage` | `fileInfos` | Max 20 items |
| `editMessage` | `message` | Max 5000 chars |
| `getMessages` | `size` | 1–100 (default 50) |

### Cleanup

Always call this when the app closes or a component unmounts. Prevents memory leaks and cleans up server resources.

```javascript
// Closes WebSocket, ends active calls, removes all subscriptions and listeners
await client.destroy();
```

### Browser Support

| Browser | Minimum Version |
|---|---|
| Chrome | 70+ |
| Firefox | 65+ |
| Safari | 13+ |
| Edge | 79+ |

### TypeScript Support

```typescript
import TalkFlowClient, { ConnectionState, ChatMessageType } from '@vibexnpm/talkflow';

const client = new TalkFlowClient({
    apiKey: 'ck-xxxx',
    projectId: 'your-project-id'
});
```

Type definitions are available at `dist/index.d.ts`.
