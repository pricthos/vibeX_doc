<!--
{
  "navGroups": {
    "권장-초기-설정-순서": "초기 설정",
    "설치": "설치",
    "인증-및-api-key": "인증",
    "빠른-시작": "빠른 시작",
    "jwt-토큰-관리": "JWT 관리",
    "api-사용-패턴": "API 패턴",
    "사용자-관리": "사용자",
    "id-구분": "ID 구분",
    "채팅": "채팅",
    "채팅-이벤트": "채팅 이벤트",
    "웹-푸시-알림": "웹 푸시",
    "webrtc-영상음성-통화": "WebRTC",
    "레퍼런스": "레퍼런스"
  }
}
-->

# TalkFlow SDK

채팅 + WebRTC 통합 JavaScript SDK

TalkFlow SDK는 여러분의 웹 서비스에 **실시간 채팅**과 **영상/음성 통화** 기능을 빠르게 붙일 수 있게 해주는 도구입니다. 복잡한 WebSocket 연결이나 WebRTC 설정을 직접 구현할 필요 없이, 몇 줄의 코드만으로 카카오톡 같은 채팅 기능을 만들 수 있어요.

---

---

## 권장 초기 설정 순서

고객사에서 가장 많이 헷갈리는 부분은 **JWT 발급**, **서버 연결**, **푸시 권한 요청**의 순서입니다. 초기 연동은 아래 순서를 권장합니다.

1. 고객사 backend가 TalkFlow JWT를 발급합니다.
2. 브라우저에서 `TalkFlowClient`를 생성합니다.
3. `await client.connect(jwtToken)`으로 연결합니다.
4. 연결이 끝난 뒤, **사용자 버튼 클릭**으로 `client.enablePushNotifications()`를 호출합니다.

```javascript
const client = new TalkFlowClient({
    apiKey: 'ck-your-client-key',
    projectId: 'your-project-id'
});

// 1) 고객사 backend에서 JWT 발급
const jwtToken = await fetchJwtFromYourBackend();

// 2) TalkFlow 연결
await client.connect(jwtToken);

// 3) 푸시는 사용자 액션(버튼 클릭) 안에서 활성화 권장
pushButton.addEventListener('click', () => {
    client.enablePushNotifications();
});
```

### 푸시 권한 팝업 주의사항

- `Notification.requestPermission()`은 브라우저 정책상 **사용자 제스처(클릭/탭)** 안에서 호출하는 것이 가장 안전합니다.
- 특히 Chrome 계열에서는 비동기 연결 작업이 끝난 뒤 자동으로 권한 요청을 띄우면, 팝업이 바로 안 뜨거나 새로고침 후에만 뜨는 것처럼 보일 수 있습니다.
- 그래서 초기 온보딩에서는 `connect(jwt, { enablePush: true })` 보다, **연결 완료 후 버튼 클릭으로 `enablePushNotifications()` 호출**을 권장합니다.

```javascript
// ✅ 권장
await client.connect(jwtToken);
enablePushButton.onclick = () => client.enablePushNotifications();

// ⚠️ 초기 온보딩에서는 비권장
await client.connect(jwtToken, { enablePush: true });
```

---

## 설치

SDK를 프로젝트에 추가하는 방법은 두 가지입니다. 보통은 NPM을 사용하는 걸 권장해요.

### NPM

React, Vue, Next.js 등 번들러를 사용하는 프로젝트라면 NPM으로 설치하세요.

```bash
npm install @vibexnpm/talkflow
```

### CDN

별도의 빌드 과정 없이 HTML 파일에 바로 script 태그로 추가할 수 있습니다. 빠르게 테스트해보고 싶을 때 유용해요.

```html
<!-- 최신 버전 (권장) -->
<script src="https://cdn.jsdelivr.net/npm/@vibexnpm/talkflow"></script>

<!-- 특정 버전 지정 (운영 환경 권장) -->
<script src="https://cdn.jsdelivr.net/npm/@vibexnpm/talkflow@1.0.0"></script>

<!-- 메이저 버전만 지정 (1.x.x 중 최신) -->
<script src="https://cdn.jsdelivr.net/npm/@vibexnpm/talkflow@1"></script>

<!-- 마이너 버전까지 지정 (1.0.x 중 최신) -->
<script src="https://cdn.jsdelivr.net/npm/@vibexnpm/talkflow@1.0"></script>

<script>
    // CDN으로 로드하면 TalkFlowSDK.default로 접근합니다
    const client = new TalkFlowSDK.default({
        apiKey: 'CLIENT_KEY',
        projectId: 'your-project-id',
        jwtToken: '<고객사 backend 에서 받은 JWT>'
    });
</script>
```

> **운영 환경 팁**: CDN을 사용할 때는 `@1.0.0`처럼 버전을 명시하세요. 버전 없이 쓰면 SDK가 업데이트될 때 예상치 못한 동작 변경이 생길 수 있어요.

#### CDN 사용 시 웹 푸시 활성화 (선택)

웹 푸시 알림을 쓰려면 Firebase SDK를 **TalkFlow보다 먼저** 로드해야 합니다. 순서가 중요해요.

```html
<!-- 1. Firebase 먼저 로드 -->
<script src="https://www.gstatic.com/firebasejs/11.8.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/11.8.1/firebase-messaging-compat.js"></script>

<!-- 2. TalkFlow SDK -->
<script src="https://cdn.jsdelivr.net/npm/@vibexnpm/talkflow"></script>
```

> NPM으로 설치한 경우엔 Firebase가 자동으로 함께 설치되므로 이 과정이 필요 없습니다.

---

## 인증 및 API Key

### API Key 종류

TalkFlow는 프로젝트당 **두 종류의 API Key**를 발급합니다. 어떤 키를 어디서 쓰는지 구분하는 게 중요해요.

| 키 | 접두사 | 보관 위치 | 용도 |
|---|---|---|---|
| **Server Key** | `sk-` | 서버 환경변수 (절대 브라우저에 노출 금지) | JWT 발급 포함 전체 API 접근 |
| **Client Key** | `ck-` | 브라우저 SDK (노출돼도 안전) | 채팅/통화/조회만 가능 |

Client Key(`ck-`)는 브라우저 코드에 그대로 넣어도 괜찮습니다. JWT 없이는 인증이 필요한 API를 호출할 수 없고, JWT 발급 자체는 서버에서만 할 수 있도록 막혀 있어요.

### 보안 정책

| 정책 | 설명 |
|---|---|
| **Client Key로 JWT 발급 차단** | `ck-` 키로 JWT 발급 시도하면 서버가 403 반환 |
| **Server Key 브라우저 사용 차단** | 프로덕션에서 `sk-` 키를 브라우저에서 쓰면 서버가 403 반환 |
| **SDK 경고** | `sk-` 키로 SDK 초기화하면 개발 환경은 `console.warn`, 그 외엔 `console.error` 출력 |

---

## 빠른 시작

### 개발/프로토타입 — Server Key 사용

처음 개발하거나 기능을 테스트할 때는 Server Key를 써서 바로 시작할 수 있어요. `registerUser()`를 호출하면 JWT 발급부터 설정까지 자동으로 처리해줍니다.

```javascript
import TalkFlowClient from '@vibexnpm/talkflow';

const client = new TalkFlowClient({
    apiKey: 'sk-xxxx',             // 개발용 Server Key
    projectId: 'your-project-id',
    env: 'development'             // 개발 모드 — sk- 키 허용
});

// 사용자 등록과 JWT 설정을 한 번에 처리해줍니다
await client.registerUser({ userId: 'user-123', nickname: '홍길동' });

// WebSocket 연결 시작 (서버와 실시간 통신 준비)
await client.connect();

// 바로 채팅 테스트 가능
await client.sendTextMessage('room-id', '안녕하세요!');
```

> ⚠️ **주의**: `registerUser()`는 개발 편의용입니다. 프로덕션에서 Client Key(`ck-`)로 호출하면 서버가 403을 반환해요. 실제 서비스에서는 아래 "프로덕션 인증 플로우"를 따르세요.

### 프로덕션 인증 플로우 — Client Key + Backend JWT

실제 서비스에서는 보안을 위해 JWT를 **여러분의 서버에서 발급**해야 합니다.

흐름 요약:
1. 사용자가 여러분 서비스에 로그인
2. 여러분 서버가 TalkFlow 서버에 JWT 발급 요청 (Server Key 사용)
3. TalkFlow 서버가 JWT를 여러분 서버에 반환
4. 여러분 서버가 JWT를 브라우저로 전달
5. 브라우저 SDK가 JWT로 TalkFlow에 연결

이 방식을 쓰면 Server Key는 절대 브라우저에 노출되지 않아요.

#### 고객사 Backend 구현 예시 (Node.js)

여러분의 서버에서 이렇게 JWT를 발급해서 브라우저로 내려주세요.

```javascript
// 예: Express.js 라우터
app.post('/api/chat-token', authenticateMyService, async (req, res) => {
    const { userId, nickname } = req.user; // 이미 로그인된 사용자 정보

    // TalkFlow 서버에 JWT 발급 요청
    const response = await fetch('https://chat.apiorbit.net/api/v1/users/auth', {
        method: 'POST',
        headers: {
            'X-API-KEY': process.env.TALKFLOW_SERVER_KEY,   // sk- 키는 환경변수로 관리
            'X-PROJECT-ID': process.env.TALKFLOW_PROJECT_ID,
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({ userId, nickname })
    });

    const data = await response.json();
    res.json({ jwtToken: data.data.accessToken });
});
```

#### 브라우저 SDK 초기화

서버에서 받은 JWT로 SDK를 초기화하고 연결합니다.

```javascript
import TalkFlowClient from '@vibexnpm/talkflow';

// 1단계: 내 서버에서 JWT 받기
const { jwtToken } = await fetch('/api/chat-token').then(r => r.json());

// 2단계: SDK 초기화 — Client Key 사용 (브라우저에 노출돼도 안전)
const client = new TalkFlowClient({
    apiKey: 'ck-xxxx',
    projectId: 'your-project-id',
    env: 'production'
});

// 3단계: JWT로 연결 + 웹 푸시 자동 활성화
await client.connect(jwtToken, { enablePush: true });

// 4단계: 이제 모든 기능 사용 가능
await client.subscribeRoom('room-id');
client.setActiveRoom('room-id');   // 이 방의 메시지를 자동으로 읽음 처리
```

#### 재로그인 (다른 사용자로 전환)

다른 계정으로 전환할 때는 기존 세션을 먼저 끊고 새로 연결하세요.

```javascript
await client.logout();
const { jwtToken } = await fetchNewJwt();
await client.connect(jwtToken, { enablePush: true });
```

---

## JWT 토큰 관리

JWT는 만료 기간이 있으므로 주기적으로 갱신해야 합니다. 연결을 끊지 않고 토큰만 교체할 수 있어요.

```javascript
// 토큰 설정 (connect() 전에 수동으로 설정할 때)
await client.setToken('new-jwt-token');

// 토큰만 교체 (연결 유지) — 같은 사용자라면 WebSocket 연결을 끊지 않아도 됩니다
client.updateToken('new-jwt-token');

// 만료 확인 — 두 번째 인자(60)는 "만료까지 60초 미만이면 만료된 것으로 처리" 의미
import { isJWTExpired, getJWTRemainingTime } from '@vibexnpm/talkflow';

if (isJWTExpired(token, 60)) {
    const { jwtToken } = await fetch('/api/chat-token/refresh').then(r => r.json());
    await client.setToken(jwtToken);
}

// 토큰 만료까지 남은 시간 (밀리초)
const remainingMs = getJWTRemainingTime(token);
```

---

## API 사용 패턴

SDK를 초기화하고 나면 하나의 `client` 객체로 모든 기능을 제어합니다. 메서드와 이벤트 모두 `client.xxx()` 형태로 통일되어 있어요.

```javascript
// 모든 기능을 client 하나로 호출합니다
await client.subscribeRoom(roomId);       // 채팅방 구독
await client.sendMessage(roomId, data);   // 메시지 전송
client.setActiveRoom(roomId);             // 현재 보고 있는 방 설정

// 이벤트도 모두 client에 구독합니다
client.on('chatMessage', handler);        // 메시지 수신
client.on('messageRead', handler);        // 읽음 처리
```

> 내부 구조가 바뀌어도 `client.xxx()` 방식은 항상 동일하게 유지됩니다. SDK 버전을 올려도 기존 코드를 고칠 필요가 없어요.

---

## 사용자 관리

> **중요**: 사용자 등록과 인증은 반드시 **여러분의 서버**에서 Server Key로 처리해야 합니다. 브라우저에서 직접 하지 마세요.

채팅방을 만들거나 1:1 채팅을 시작하려면 상대방의 내부 userId가 필요합니다. 그래서 채팅 기능을 쓰기 전에 사용자 조회/검색을 먼저 알아두는 게 좋아요.

```javascript
// 닉네임으로 사용자 검색 — 1:1 채팅 상대를 찾을 때 사용
const result = await client.searchUsers({
    keyword: '홍',  // 필수, 최대 50자
    limit: 20       // 최대 50개, 기본 20
});
// result에서 원하는 사용자의 userId(내부 UUID)를 꺼내 채팅방 생성에 사용하세요

// 특정 회원이 TalkFlow에 등록됐는지 확인
// 인자로 여러분 서비스의 회원 ID(externalUserId)를 씁니다
const exists = await client.checkUserExists('my-service-user-id');

// 전체 사용자 목록 조회
const users = await client.getUsers({ size: 20 });

// 내 프로필 수정
// ⚠️ metadata는 부분 업데이트가 아닌 전체 교체입니다
// 기존 metadata를 유지하려면 기존 값을 읽어서 합친 뒤 전달하세요
await client.updateMyInfo({
    nickname: '새 닉네임',                   // 최대 100자
    profileImageUrl: 'https://...',          // 최대 500자
    metadata: { department: '기획팀' }       // 기존 metadata 전체 교체됨
});
```

---

## ID 구분

SDK에서 자주 혼동되는 ID 타입이 두 가지 있어요. 잘못 쓰면 API가 실패하니 꼭 구분하세요.

| ID 타입 | 설명 | 예시 |
|---|---|---|
| **userId (내부 UUID)** | TalkFlow가 내부적으로 생성한 고유 ID | `550e8400-e29b-41d4-a716-446655440000` |
| **externalUserId** | 여러분 서비스의 회원 ID (등록 시 전달한 값) | `user-123`, `member_001` |

- `POST /api/v1/users/auth`에서 `userId` 필드 → **여러분 서비스의 회원 ID** (externalUserId)
- `createOneToOneRoom(friendId)` → **TalkFlow 내부 UUID** 사용
- `inviteToGroupRoom(userIds)` → **TalkFlow 내부 UUID** 배열
- `checkUserExists(projectUserId)` → **여러분 서비스의 회원 ID** (externalUserId)

> **Tip**: `POST /api/v1/users/auth` 응답의 `user.userId`가 TalkFlow 내부 UUID입니다. 이 값을 여러분 DB에 저장해두고, 채팅방/통화 API에서 상대방을 지정할 때 사용하세요.

---

## 채팅

### 채팅방 관리

채팅방을 만들고, 조회하고, 설정하는 방법입니다.

```javascript
// 내 채팅방 목록 가져오기 (채팅 리스트 화면에서 사용)
const rooms = await client.getRooms({ size: 20 });

// 그룹 채팅방만 필터링해서 가져오기
const groupRooms = await client.getRooms({ type: 'GROUP' });

// 특정 방 입장 — 방 정보와 최근 메시지를 함께 반환합니다
// 채팅 화면을 열 때 이 메서드로 초기 데이터를 한 번에 가져오세요
const roomDetail = await client.getRoom('room-id');
// roomDetail.room: 방 이름, 참가자 수 등
// roomDetail.messages: 최근 메시지 목록

// 방 정보만 필요할 때 (입장 처리 없이 메타데이터만 조회)
const roomInfo = await client.getRoomInfo('room-id');

// 1:1 채팅방 생성 또는 기존 방 반환
// friendId는 TalkFlow 내부 UUID를 써야 합니다
const room = await client.createOneToOneRoom('friend-user-id');

// 그룹 채팅방 생성
const groupRoom = await client.createGroupRoom({
    roomName: '우리 팀 채팅방',                      // 필수, 최대 100자
    description: '개발팀 공용 채팅방',               // 선택, 최대 500자
    invitedUserIds: ['user-id-1', 'user-id-2'],    // 초대할 사람들 (TalkFlow UUID)
    roomType: 'GROUP'                              // 기본값, 생략 가능
});

// 비밀 그룹 채팅방 — 입장 시 비밀번호가 필요합니다
// 비밀번호는 서버에서 암호화해서 저장하므로 안전합니다
const privateRoom = await client.createGroupRoom({
    roomName: '비공개 회의실',
    invitedUserIds: ['user-id-1'],
    roomType: 'PRIVATE_GROUP',
    password: 'secret1234'         // 필수, 4~50자
});

// 메시지 보관 기간 설정 — 설정하면 기간이 지난 메시지가 보이지 않게 됩니다
const tempRoom = await client.createGroupRoom({
    roomName: '오늘 회의',
    invitedUserIds: ['user-1'],
    messageRetentionHours: 24     // 1시간~1년(8760시간), 미설정 시 무제한
});
```

### 채팅방 입장 및 구독

채팅방에서 실시간 메시지를 받으려면 구독이 필요합니다. 아래 순서대로 진행하세요.

**기본 흐름**:
1. 방 참여 — `joinGroupRoom` (그룹 채팅방에 처음 들어갈 때)
2. 방 구독 — `subscribeRoom` — 실시간 메시지 수신 시작
3. 현재 방 설정 — `setActiveRoom` — 자동 읽음 처리 활성화
4. 다른 화면 이동 시 — `clearActiveRoom` — 읽음 처리 중단
5. 앱 종료/화면 이탈 시 — `unsubscribeRoom` — 구독 해제

```javascript
// 공개 그룹 채팅방 참여 (방장 승인 없이 바로 입장)
await client.joinGroupRoom('room-id');

// 비밀 그룹 채팅방 참여 — 비밀번호 필요
await client.joinGroupRoom('room-id', 'secret1234');
// 이미 참가자인 경우엔 비밀번호 없이도 통과됩니다
// 방을 나갔다가 재입장할 땐 비밀번호를 다시 입력해야 해요

// ★ 채팅방 구독 — 실시간 메시지를 받으려면 반드시 호출해야 합니다
await client.subscribeRoom('room-id');

// ★ 현재 보고 있는 방 설정 — 이 방의 새 메시지를 자동으로 읽음 처리합니다
// 채팅 화면에 진입할 때 호출하세요
client.setActiveRoom('room-id');

// 다른 화면으로 이동하거나 목록으로 돌아갈 때 — 자동 읽음 처리 중단
client.clearActiveRoom();

// 채팅방 구독 해제 — 참가자 상태는 유지, 실시간 수신만 중단
client.unsubscribeRoom('room-id');

// 채팅방 완전히 나가기 — 참가자 목록에서도 제거됩니다
await client.leaveRoom('room-id');

// 현재 구독 중인 방 목록 확인
const subscribedRooms = client.getSubscribedRooms();

// 특정 방이 구독 중인지 확인
const isSubscribed = client.isSubscribed('room-id');

// 참가 가능한 공개 그룹 채팅방 조회 (아직 참가하지 않은 방)
const availableRooms = await client.getAvailableGroupRooms();

// 프로젝트 내 모든 그룹 채팅방 조회
const allGroupRooms = await client.getAllGroupRooms();

// 모든 채팅방 구독 한 번에 해제
client.unsubscribeAllRooms();
```

### 채팅방 리스트 실시간 업데이트

카카오톡처럼 채팅 목록 화면에서도 새 메시지가 오면 실시간으로 업데이트되는 기능입니다.

**제공 기능:**
- 새 메시지가 오면 해당 방이 목록 최상단으로 이동 + 안 읽은 카운트 증가
- 새 방이 생성되면 목록에 즉시 추가
- 내가 방을 나가면 목록에서 자동으로 제거
- 여러 서버/인스턴스 환경에서도 정확하게 동작 (Redis Pub/Sub 기반)

기본적으로 `connect()` 시 자동으로 구독이 시작됩니다. 별도 설정이 필요 없어요.

```javascript
// 기본 방식 — connect()하면 자동으로 리스트 구독 시작 (권장)
await client.connect();

// 수동으로 제어하고 싶을 때
const client = new TalkFlowClient({
    apiKey: 'CLIENT_KEY',
    projectId: 'your-project-id',
    autoSubscribeRoomList: false  // 자동 구독 끄기
});
await client.subscribeRoomList(); // 원하는 시점에 직접 구독
client.unsubscribeRoomList();     // 구독 해제
client.isRoomListSubscribed();    // 구독 중인지 확인
```

#### 이벤트 처리

```javascript
// 새 메시지가 온 방을 목록 최상단으로 올리고 안 읽은 카운트 업데이트
client.on('roomListMessage', (event) => {
    moveRoomToTop(event.roomId);
    updateLastMessage(event.roomId, event.lastMessage, event.lastMessageAt);
    // 내가 보낸 메시지는 이미 읽은 것으로 처리 (카운트 증가 안 함)
    if (event.actorId !== currentUserId) {
        incrementUnread(event.roomId, event.unreadCountDelta);
    }
});

// 새 채팅방이 생성되면 목록에 추가
client.on('roomListCreated', (event) => {
    addRoomToList(event.roomId);
});

// 내가 새 방에 참가했을 때 목록에 추가
// 다른 사람이 입장했을 때는 참가자 수만 업데이트
client.on('roomListJoined', (event) => {
    if (event.actorId === currentUserId) {
        addRoomToList(event.roomId);
    } else {
        setParticipantCount(event.roomId, event.activeParticipantCount);
    }
});

// ★ 내가 방을 나갔을 때 목록에서 제거 (꼭 처리해야 합니다)
client.on('roomListSelfLeft', (event) => {
    removeRoomFromList(event.roomId);
});

// 메시지 보관 기간 만료 시 해당 메시지를 UI에서 제거
client.on('retentionCleanup', ({ roomId, cutoffTime }) => {
    removeMessagesBefore(roomId, cutoffTime);
});
```

#### 이벤트 페이로드 구조

```typescript
interface RoomListEvent {
    eventType: 'MESSAGE_RECEIVED' | 'ROOM_CREATED' | 'ROOM_JOINED' | 'ROOM_LEFT' | 'ROOM_UPDATED';
    roomId: string;
    actorId: string;                  // 이벤트를 일으킨 사용자 ID
    members?: MemberInfo[];            // 입장/퇴장 시 해당 사용자 정보
    activeParticipantCount?: number;  // 변경 후 정확한 참가자 수
    unreadCountDelta?: number;        // 안 읽은 메시지 증가량 (보통 1)
    lastMessage?: string;             // 가장 최근 메시지 내용 또는 미리보기
    lastMessageType?: string;         // 'TEXT' | 'IMAGE' | 'FILE' | 'VIDEO' | 'AUDIO' | 'SYSTEM'
    lastMessageAt?: string;           // 최근 메시지 시각 (ISO 8601)
    timestamp: string;                // 이벤트 발생 시각 (ISO 8601)
}
```

#### 방 안 구독 vs 리스트 구독 비교

두 구독은 역할이 다릅니다. 화면에 따라 알맞은 구독을 사용하세요.

| 구분 | `subscribeRoom(roomId)` | `subscribeRoomList()` |
|---|---|---|
| **어디서 쓰나** | 채팅방 내부 화면 | 채팅방 목록 화면 |
| **뭘 받나** | 메시지, 읽음, 타이핑, 멤버 변경 | 목록 갱신용 메타데이터 |
| **자동 읽음** | `setActiveRoom()` 설정 시 자동 처리 | 해당 없음 |
| **언제 호출** | 채팅방 열 때마다 | 앱 시작 시 1회 |

두 구독은 동시에 활성화할 수 있으며, 서로 독립적으로 동작합니다.

### 메시지 전송

#### sendMessage 파라미터

| 필드 | 타입 | 필수 | 설명 |
|---|---|---|---|
| `messageId` | `string` | 필수 | 클라이언트에서 직접 만드는 고유 ID. UUID 권장. 같은 ID로 재전송하면 서버가 중복을 자동으로 막아줍니다 |
| `message` | `string` | 조건부 | 텍스트 본문. 최대 5000자 |
| `fileInfos` | `FileInfo[]` | 조건부 | 첨부 파일 정보 배열. 최대 20개 |
| `separateFiles` | `boolean` | 선택 | 파일을 개별 메시지로 보낼지 여부 (기본값: `true`) |
| `replyToMessageId` | `string` | 선택 | 답글 대상 원본 메시지 ID |

> `message`와 `fileInfos` 중 하나는 반드시 있어야 합니다.

```javascript
// 가장 간단한 방법 — messageId를 SDK가 자동으로 만들어줍니다
await client.sendTextMessage('room-id', '안녕하세요!');

// messageId를 직접 지정하는 방법
// 전송 실패 시 같은 ID로 재시도하면 중복 없이 안전하게 처리됩니다
await client.sendMessage('room-id', {
    messageId: crypto.randomUUID(), // 브라우저 내장 UUID 생성기
    message: '안녕하세요!'
});

// 파일 첨부 메시지
// fileUrl은 미리 업로드된 파일의 URL이어야 합니다
await client.sendMessage('room-id', {
    messageId: crypto.randomUUID(),
    message: '첨부 파일입니다',
    fileInfos: [{
        fileName: 'image.jpg',
        fileUrl: 'https://your-storage.com/image.jpg',
        fileSize: 1024,              // 바이트 단위
        fileType: 'image/jpeg'       // MIME 타입
    }]
});

// 답글 보내기 — 원본 메시지 ID를 지정하면 서버가 원본 내용을 자동으로 첨부합니다
// 카카오톡의 "답장" 기능과 동일합니다
await client.sendReply('room-id', '저도 동의해요!', 'original-msg-id');
```

#### separateFiles 동작 방식

여러 파일을 한 번에 보낼 때 메시지를 어떻게 구성할지 결정하는 옵션입니다.

| 값 | 동작 | 생성되는 메시지 수 |
|---|---|---|
| `true` (기본) | 파일 하나당 메시지 하나씩 개별 전송 | 파일 개수만큼 |
| `false` | 같은 타입끼리 묶어서 그룹 메시지로 전송 | 파일 타입 종류만큼 |

- **카카오톡 스타일** (사진을 한 장씩 따로 보여줌) → `true` (기본값)
- **인스타그램 스타일** (여러 장을 갤러리 카드 하나로 묶음) → `false`

### 파일 업로드

SDK 자체가 **업로드 인프라**를 제공합니다. 고객사가 별도 스토리지 서버를 운영할 필요 없이 `client.uploadFile()` / `client.sendFileMessage()` 한 번 호출로 업로드 + 메타데이터 추출(이미지/비디오 크기, 영상 duration, 썸네일 등) + 메시지 전송이 끝납니다.

> 기존 방식(외부 업로드 후 `fileInfos` 직접 구성)도 그대로 동작합니다. SDK 업로드 기능은 선택이며 기존 연동을 깨지 않습니다.

#### API 요약

| 메서드 | 레벨 | 용도 |
|---|---|---|
| `client.uploadFile(roomId, file, options)` | Low-level | 업로드만. `FileMetaData` 반환 → `sendMessage`의 `fileInfos`에 넣어 수동 전송 |
| `client.sendFileMessage(roomId, files, options)` | High-level | 업로드 + 메시지 전송 통합. 단일 파일 또는 파일 배열 |
| `client.sendMessage(roomId, { fileInfos })` | 기존 방식 | 외부에서 업로드해 받은 메타를 직접 전송 (변경 없음) |

#### 서버 처리 (참고)

파일이 업로드되면 서버는 아래 순서로 처리합니다.

1. 방 참가자 권한 검증 — 비참가자 차단 (403)
2. 카테고리 판별 — `image/` / `video/` / `audio/` / PDF·문서류 화이트리스트
3. 크기 상한 — **이미지 10MB / 비디오 100MB / 오디오 20MB / 문서 20MB**
4. 매직넘버 검증 — 확장자 위조 차단
5. 메타 추출 — EXIF/rotation 반영 width/height, 비디오 duration, 1초 지점 썸네일 JPG 생성
6. S3 업로드 — `{projectId}/{roomId}/files/{uuid}.ext` 경로로 격리
7. `FileMetaData` 반환

지원 확장자: `png jpg jpeg gif webp` / `mp4 mov webm` / `mp3 wav ogg m4a aac` / `pdf doc docx xls xlsx ppt pptx zip`

#### `uploadFile()` — Low-level

업로드만 하고, 전송 타이밍은 직접 제어할 때 사용합니다. 업로드 완료 후 프리뷰를 먼저 보여주고 사용자가 전송 버튼을 누를 때 메시지를 보내는 UX에 적합해요.

```javascript
const meta = await client.uploadFile('room-id', fileInput.files[0], {
    onProgress: ({ loaded, total, percent }) => {
        console.log(`${percent}% (${loaded}/${total} bytes)`);
    }
});

// 반환된 meta를 fileInfos에 그대로 사용
await client.sendMessage('room-id', {
    messageId: crypto.randomUUID(),
    fileInfos: [meta],
    message: '첨부합니다'
});
```

#### `sendFileMessage()` — High-level

업로드와 메시지 전송을 한 번에 처리합니다. 가장 간단한 방법이에요.

```javascript
// 단일 파일
await client.sendFileMessage('room-id', file, {
    message: '첨부합니다',
    onUploadProgress: ({ percent }) => updateBar(percent)
});

// 다중 파일 (병렬 업로드)
await client.sendFileMessage('room-id', [file1, file2, file3], {
    message: '여러 파일',
    onUploadProgress: ({ fileIndex, percent }) => {
        updateBar(fileIndex, percent);  // 파일별 개별 프로그레스 바
    }
});
```

#### options 파라미터

| 필드 | 타입 | 설명 |
|---|---|---|
| `onProgress` / `onUploadProgress` | `(e) => void` | `e = { loaded, total, percent, fileIndex? }`. `fileIndex`는 다중 파일에서 0부터 시작 |
| `signal` | `AbortSignal` | 업로드 취소. 다중 파일 동시 업로드 시 모든 업로드에 전파 |
| `message` | `string` | (sendFileMessage) 함께 보낼 텍스트 본문 |
| `messageId` | `string` | (sendFileMessage) 메시지 ID (미지정 시 자동 생성) |
| `separateFiles` | `boolean` | (sendFileMessage) 파일별 개별 메시지 분리 여부 |
| `replyToMessageId` | `string` | (sendFileMessage) 답글 대상 원본 메시지 ID |

#### 업로드 취소 (AbortController)

```javascript
const controller = new AbortController();

cancelButton.onclick = () => controller.abort();

try {
    await client.sendFileMessage('room-id', file, {
        signal: controller.signal,
        onUploadProgress: ({ percent }) => updateBar(percent)
    });
} catch (error) {
    if (error.message === 'Upload cancelled') {
        // 사용자가 취소한 경우
    } else {
        // 기타 실패 (네트워크, 서버 에러 등)
    }
}
```

#### 주요 에러 코드

| 코드 | 상황 |
|---|---|
| `FILE_REQUIRED` | 파일이 비어 있거나 누락됨 |
| `FILE_SIZE_EXCEEDED` | 카테고리 크기 상한 초과 (413) |
| `UNSUPPORTED_FILE_TYPE` | 화이트리스트 밖의 파일 타입 |
| `INVALID_MAGIC_NUMBER` | 확장자와 실제 파일 내용 불일치 (위조 차단) |
| `FILE_UPLOAD_FAILED` | S3 업로드 실패 |
| `METADATA_EXTRACT_FAILED` | 메타데이터 추출 실패 |

> 다중 파일 전송 중 **한 파일이라도 실패하면** `sendFileMessage` 전체가 reject됩니다. 이미 S3에 올라간 파일은 라이프사이클 정책으로 주기적으로 정리됩니다.

### 메시지 조회, 수정, 삭제

```javascript
// 메시지 목록 조회 — 커서 기반 페이징 방식입니다
// lastId와 lastSortValue를 이전 응답에서 가져와 다음 페이지를 불러오세요
const messages = await client.getMessages('room-id', {
    size: 50,                         // 한 번에 가져올 메시지 수 (최대 100)
    lastId: 'last-message-id',        // 이전 페이지의 마지막 메시지 ID
    lastSortValue: 1673424645123      // 이전 페이지의 마지막 정렬값 (epoch ms)
});

// 다음 페이지 불러오기
if (messages.data.hasNext) {
    const nextPage = await client.getMessages('room-id', {
        size: 50,
        lastId: messages.data.lastId,
        lastSortValue: messages.data.lastSortValue
    });
}

// 내가 보낸 메시지 수정 — 상대방 화면에도 실시간으로 반영됩니다
await client.editMessage('room-id', 'msg-id', '수정된 내용입니다');

// 모두에게 삭제 — 상대방 화면에서도 "삭제된 메시지입니다"로 바뀝니다
await client.deleteMessage('room-id', 'msg-id');

// 나만 삭제 — 내 화면에서만 사라지고 상대방은 원본을 볼 수 있습니다
await client.deleteMessage('room-id', 'msg-id', 'SELF');
```

> 수정/삭제는 본인 메시지만 가능합니다. 수정 시 상대방 화면에 "수정됨" 라벨이 표시됩니다.

### 이모지 리액션

특정 메시지에 이모지로 반응할 수 있어요. 카카오톡의 이모지 반응 기능과 같습니다.

```javascript
// 리액션 추가
await client.addReaction('room-id', 'msg-id', '👍');

// 내가 누른 리액션 취소
await client.removeReaction('room-id', 'msg-id', '👍');

// 메시지 수신 시 reactions 필드 예시
// [{ emoji: '👍', userIds: ['user-1', 'user-2'] }, { emoji: '😂', userIds: ['user-3'] }]
```

### 메시지 고정

채팅방 상단에 중요한 메시지를 고정할 수 있어요. 방장만 가능합니다.

```javascript
// 메시지 고정 (방장 권한 필요)
await client.pinMessage('room-id', 'msg-id');

// 고정 해제 (방장 권한 필요)
await client.unpinMessage('room-id');

// 방 정보의 room.pinnedMessageId에서 고정된 메시지 ID를 확인할 수 있습니다
```

### 타이핑 표시

"홍길동 님이 입력 중..." 같은 실시간 타이핑 표시 기능입니다.

```javascript
// input 이벤트에 연결해두면 됩니다
// SDK 내부에서 3초 동안 입력이 없으면 자동으로 타이핑 중단을 서버에 알립니다
inputField.addEventListener('input', () => {
    client.startTyping('room-id');
});

// 메시지를 전송하면 sendMessage 내부에서 자동으로 타이핑을 중단시킵니다
client.stopTyping('room-id'); // 수동 중단이 필요할 때만 호출

// 상대방의 타이핑 이벤트 수신 (내 이벤트는 SDK가 자동 필터링)
client.on('typing', ({ roomId, userId, userName, typing }) => {
    if (typing) {
        showTypingIndicator(`${userName} 님이 입력 중...`);
    } else {
        hideTypingIndicator();
    }
});
```

> 타이핑 이벤트는 DB에 저장되지 않는 실시간 휘발성 정보입니다. `subscribeRoom()` 호출 시 타이핑 구독도 자동으로 함께 시작됩니다.

### 방 설정 수정 및 초대

```javascript
// 그룹 채팅방 정보 수정 — 방장만 가능, 전달한 필드만 변경됩니다
await client.updateGroupRoom('room-id', {
    roomName: '새로운 방 이름',
    description: '업데이트된 설명',
    messageRetentionHours: 48,
    anyoneCanInvite: true        // true면 참가자 누구나 초대 가능
});

// 비밀방 비밀번호 변경 (PRIVATE_GROUP만 해당)
await client.updateGroupRoom('room-id', { password: 'newPassword123' });

// 기존 방에 새 사람 초대하기
await client.inviteToGroupRoom('room-id', ['user-id-3', 'user-id-4']);
```

---

## 채팅 이벤트

`subscribeRoom()` 또는 `subscribeRoomList()` 호출 후 수신 가능한 이벤트입니다.

### 연결 이벤트

| 이벤트 | 설명 |
|---|---|
| `connected` | 서버 연결 완료 |
| `disconnected` | 서버 연결 끊김 |
| `reconnecting` | 자동 재연결 시도 중 |
| `connectionError` | 연결 실패 |
| `stateChange` | 연결 상태가 바뀔 때마다 발생 |
| `tokenSet` | JWT 토큰이 설정됐을 때 |
| `loggedOut` | 로그아웃 완료 |

### 채팅방 내부 이벤트

| 이벤트 | 설명 |
|---|---|
| `chatMessage` | 채팅 메시지 수신 (내 메시지 포함) |
| `newChatMessage` | 새 메시지 수신 (내가 보낸 것 제외) |
| `messageRead` | 상대방이 메시지를 읽었을 때 — 읽음 카운트 UI를 여기서 갱신하세요 |
| `typing` | 상대방이 입력 중이거나 입력을 멈췄을 때 |
| `memberJoined` | 채팅방에 누군가 입장했을 때 |
| `memberLeft` | 채팅방에서 누군가 나갔을 때 |
| `roomSubscribed` | 채팅방 구독 완료 |
| `roomUnsubscribed` | 채팅방 구독 해제 |

### 채팅방 리스트 이벤트

| 이벤트 | 설명 |
|---|---|
| `roomListUpdate` | 모든 리스트 이벤트를 하나로 받고 싶을 때 (eventType으로 분기) |
| `roomListMessage` | 새 메시지 수신 → 최상단 이동 처리 |
| `roomListCreated` | 새 방 생성 |
| `roomListJoined` | 누군가 방 입장 |
| `roomListLeft` | 누군가 방 퇴장 |
| `roomListSelfLeft` | 내가 방을 나간 경우 — 목록에서 해당 방 제거 |
| `retentionCleanup` | 메시지 보관 기간 만료 → 만료된 메시지 UI 제거 |

---

## 웹 푸시 알림

브라우저를 닫아도 알림을 받을 수 있는 기능입니다. Firebase Cloud Messaging(FCM)을 사용합니다.

### 설정 방법

**1단계: 서비스 워커 파일 배치 (최초 1회)**

서비스 워커는 브라우저 보안 정책상 도메인 루트에 있어야 합니다.

```bash
cp node_modules/@vibexnpm/talkflow/public/firebase-messaging-sw.js public/
```

**2단계: SDK에서 푸시 활성화**

```javascript
await client.connect();

// 푸시 활성화 — 백그라운드에서 비동기로 처리됩니다 (논블로킹)
client.enablePushNotifications();

// 활성화 완료 이벤트 (선택)
client.on('pushEnabled', () => {
    console.log('푸시 알림이 활성화됐습니다');
});

// 앱이 열려 있는 상태에서 푸시가 오면 이 이벤트로 처리하세요
// 현재 보고 있는 방의 알림은 SDK가 자동으로 무시합니다
client.on('pushNotification', ({ title, body, data }) => {
    showInAppNotification(title, body, data.roomId);
});
```

**3단계: 알림 클릭 시 해당 채팅방으로 이동**

```javascript
// 앱이 열려 있을 때 (warm start)
navigator.serviceWorker.addEventListener('message', (event) => {
    if (event.data.type === 'NAVIGATE_TO_ROOM') {
        router.push(`/chat/${event.data.roomId}`);
    }
});

// 앱이 완전히 닫혀 있을 때 (cold start)
const pendingRoomId = await client.consumePendingRoom();
if (pendingRoomId) {
    router.push(`/chat/${pendingRoomId}`);
}
```

### enablePushNotifications 옵션

| 옵션 | 타입 | 기본값 | 설명 |
|---|---|---|---|
| `firebaseConfig` | `object` | TalkFlow 내장 | Firebase 초기화 설정 |
| `vapidKey` | `string` | TalkFlow 내장 | FCM Web Push VAPID 공개키 |
| `serviceWorkerPath` | `string` | `/firebase-messaging-sw.js` | 서비스 워커 파일 경로 |

> ⚠️ **주의**: `firebaseConfig`나 `vapidKey`를 자체 Firebase 값으로 바꿔도 푸시가 발송되지 않습니다. TalkFlow 서버는 TalkFlow의 Firebase 프로젝트에서만 발송하기 때문이에요.

---

## WebRTC (영상/음성 통화)

### 그룹 통화

여러 명이 동시에 참여하는 통화 기능입니다.

```javascript
// 통화 시작 — 카메라와 마이크 권한 요청이 발생합니다
await client.startCall({
    roomId: 'call-room-id',
    isGroup: true,
    mediaConstraints: { video: true, audio: true }
});

// 상대방의 영상/음성 스트림이 들어오면 이 이벤트로 받습니다
// video 엘리먼트의 srcObject에 연결해서 화면에 표시하세요
client.on('remoteTrack', ({ peerId, track, streams }) => {
    const videoElement = document.getElementById(`video-${peerId}`);
    videoElement.srcObject = streams[0];
});

// 통화 종료
client.endCall();

// 현재 통화 중인지 확인
const inCall = client.isInCall();

// 현재 통화에 참여 중인 사람 목록
const participants = client.getParticipants();
```

### 통화방 관리

```javascript
// 통화방 생성 (채팅방과 연결도 가능)
const callRoom = await client.createCallRoom({
    roomName: '팀 회의',
    isGroup: true,
    chatRoomId: 'linked-chat-room-id'  // 선택
});

const roomInfo = await client.getCallRoom('call-room-id');
await client.joinCallRoomApi('call-room-id');
await client.leaveCallRoomApi('call-room-id');
```

### 1:1 통화

```javascript
// 상대방에게 통화 요청 (targetUserId는 TalkFlow 내부 UUID)
await client.callUser('target-user-id');

// 수신 통화 처리
client.on('incomingCall', async ({ callerId }) => {
    if (confirm('통화를 수락하시겠습니까?')) {
        await client.acceptCall(callerId);
    } else {
        client.rejectCall(callerId);
    }
});

client.on('callBusy', ({ userId }) => {
    alert('상대방이 통화 중입니다. 잠시 후 다시 시도해주세요.');
});
```

### 미디어 제어

```javascript
// 카메라/마이크 켜고 끄기 — 현재 상태 반대로 전환하고 새 상태를 반환합니다
const videoEnabled = client.toggleVideo();
const audioEnabled = client.toggleAudio();

client.setVideoEnabled(false);
client.setAudioEnabled(true);

// 화면 공유
await client.startScreenShare();
client.stopScreenShare();

// 연결된 카메라/마이크 목록 가져오기
const devices = await client.getDevices();
await client.switchDevice(devices.videoInputs[1].deviceId, 'video');

const localStream = client.getLocalStream();
```

### 비디오 품질 설정

```javascript
await client.applyVideoConstraints({ width: 1280, height: 720, frameRate: 30 });

const videoSettings = client.getVideoSettings();
const audioSettings = client.getAudioSettings();
```

### TURN 서버 설정

WebRTC는 방화벽 뒤에 있는 사용자끼리 연결할 때 TURN 서버가 필요합니다. SDK가 자동으로 처리해주므로 대부분의 경우 신경 쓰지 않아도 돼요.

```javascript
// 방법 1: 자동 설정 (권장)
await client.startCall({ roomId: 'room-id' });

// 방법 2: 미리 초기화 — 통화 시작 전에 준비해두면 연결이 더 빠릅니다
await client.initializeIceServers();
await client.startCall({ roomId: 'room-id' });

// 방법 3: 수동 설정 — 자체 TURN 서버가 있을 때만 사용
const client = new TalkFlowClient({
    apiKey: 'your-api-key',
    projectId: 'your-project-id',
    iceServers: [
        { urls: 'stun:stun.l.google.com:19302' },
        { urls: 'turn:your-turn.com:3478', username: 'user', credential: 'pass' }
    ]
});
```

### WebRTC 이벤트

| 이벤트 | 설명 |
|---|---|
| `localStreamStarted` | 내 카메라/마이크 스트림 시작됨 |
| `localStreamStopped` | 내 카메라/마이크 스트림 중지됨 |
| `remoteTrack` | 상대방의 영상/음성 스트림 수신 |
| `callStarted` | 통화 시작됨 |
| `callEnded` | 통화 종료됨 |
| `callRequested` | 통화 요청 완료 |
| `callAccepted` | 통화 수락됨 |
| `callRejected` | 통화 거절됨 |
| `callBusy` | 상대방이 이미 통화 중 |
| `incomingCall` | 1:1 수신 통화 |
| `incomingCallWhileBusy` | 통화 중 수신 요청 (자동 거절됨) |
| `callInvitation` | 그룹 통화 초대 받음 |
| `userJoined` | 통화방에 누군가 입장 |
| `userLeft` | 통화방에서 누군가 퇴장 |
| `participantLeft` | 참여자 퇴장 |
| `participantMediaState` | 참여자 미디어 상태 변경 |
| `peerConnected` | 피어 연결 완료 |
| `peerDisconnected` | 피어 연결 해제 |
| `peerClosed` | 피어 연결 종료 |
| `screenShareStarted` | 화면 공유 시작 |
| `screenShareEnded` | 화면 공유 종료 |
| `mediaStateChanged` | 카메라/마이크 켜짐/꺼짐 상태 변경 |
| `deviceChange` | 마이크/카메라 장치 변경 감지 |
| `error` | WebRTC 오류 발생 |

---

## 레퍼런스

### 설정 옵션

`TalkFlowClient` 생성 시 전달할 수 있는 모든 옵션입니다.

```javascript
const client = new TalkFlowClient({
    // 필수
    apiKey: 'your-api-key',
    projectId: 'your-project-id',

    // 인증
    jwtToken: 'your-jwt-token',

    // 환경 설정
    env: 'production',             // 'production' | 'staging' | 'development'

    // 연결 설정
    useSockJS: true,
    reconnectDelay: 5000,          // 재연결 대기 시간 (ms)
    maxReconnectAttempts: 10,      // 최대 재연결 시도 횟수

    // 채팅방 리스트
    autoSubscribeRoomList: true,

    // 로그
    logLevel: TalkFlowClient.LogLevel.WARN,

    // WebRTC ICE 서버 (선택 — 기본값은 자동 발급)
    iceServers: [
        { urls: 'stun:stun.l.google.com:19302' },
        { urls: 'turn:turn.example.com:3478', username: 'user', credential: 'pass' }
    ]
});
```

### 환경 설정

| 환경 | 값 | 서버 URL |
|---|---|---|
| 운영 | `production` (기본값) | `https://chat.vibe-x.app` |
| 스테이징 | `staging` | `https://stg-chat.vibe-x.app` |
| 개발 | `development` | `http://dev-chat.apiorbit.net` |

### 상태 확인

```javascript
client.hasToken();      // JWT가 설정돼 있는지 (boolean)
client.isReady();       // 연결 준비가 됐는지
client.isConnected();   // 현재 서버에 연결돼 있는지
client.getState();      // 'disconnected' | 'connecting' | 'connected' | 'reconnecting' | 'error'
client.getUserId();     // 현재 로그인된 사용자의 TalkFlow 내부 UUID
client.isInitialized(); // SDK가 초기화됐는지

// 전체 상태를 한 번에 확인 (디버깅용)
const status = client.getStatus();
```

### 로그 레벨

개발 중에는 `DEBUG`로 설정해두면 SDK 내부 동작을 자세히 볼 수 있습니다. 운영 환경에서는 `WARN`이나 `ERROR`로 낮추세요.

```javascript
import { LogLevel } from '@vibexnpm/talkflow';

client.setLogLevel(LogLevel.DEBUG);  // 모든 로그 출력 (개발 시 추천)
client.setLogLevel(LogLevel.INFO);   // 일반 정보 이상
client.setLogLevel(LogLevel.WARN);   // 경고 이상 (기본값)
client.setLogLevel(LogLevel.ERROR);  // 에러만
client.setLogLevel(LogLevel.NONE);   // 로그 완전히 끄기
```

### 입력 제약사항

서버에서 검증하는 제약입니다. 위반 시 400 에러가 반환됩니다.

#### 사용자

| API | 필드 | 제약 |
|---|---|---|
| `registerUser` | `userId` | 최대 255자 |
| `registerUser` | `nickname` | 최대 100자 |
| `updateMyInfo` | `nickname` | 최대 100자 |
| `updateMyInfo` | `profileImageUrl` | 최대 500자 |
| `searchUsers` | `keyword` | 최대 50자 |
| `searchUsers` | `limit` | 1~50 (기본 20) |
| `getUsers` | `size` | 1~100 (기본 50) |

#### 채팅방

| API | 필드 | 제약 |
|---|---|---|
| `createGroupRoom` | `roomName` | 최대 100자 |
| `createGroupRoom` | `description` | 최대 500자 |
| `createGroupRoom` | `invitedUserIds` | 최대 50명 |
| `createGroupRoom` | `password` | 4~50자 (PRIVATE_GROUP만 해당) |
| `createGroupRoom` | `messageRetentionHours` | 1~8760시간 (미설정 시 무제한) |
| `getRooms` | `size` | 1~100 (기본 50) |

#### 메시지

| API | 필드 | 제약 |
|---|---|---|
| `sendMessage` | `message` | 최대 5000자 |
| `sendMessage` | `fileInfos` | 최대 20개 |
| `editMessage` | `message` | 최대 5000자 |
| `getMessages` | `size` | 1~100 (기본 50) |

#### 리액션

| API | 필드 | 제약 |
|---|---|---|
| `addReaction` / `removeReaction` | `emoji` | 이모지 문자 (예: `'👍'`, `'😂'`) |

#### WebRTC

| API | 필드 | 제약 |
|---|---|---|
| `startCall` | `roomId` | 필수 |
| `startCall` | `isGroup` | 기본 `false` |
| `startCall` | `mediaConstraints` | 기본 `{ video: true, audio: true }` |
| `callUser` / `acceptCall` | `mediaConstraints` | 기본 `{ video: true, audio: true }` |
| `applyVideoConstraints` | `constraints` | `{ width, height, frameRate }` |
| `switchDevice` | `kind` | `'video'` \| `'audio'` |

#### 파일 업로드

| API | 필드 | 제약 |
|---|---|---|
| `uploadFile` / `sendFileMessage` | 이미지 | 최대 10MB |
| `uploadFile` / `sendFileMessage` | 비디오 | 최대 100MB |
| `uploadFile` / `sendFileMessage` | 오디오 | 최대 20MB |
| `uploadFile` / `sendFileMessage` | 문서 | 최대 20MB |

#### 푸시 알림

| API | 필드 | 제약 |
|---|---|---|
| `enablePushNotifications` | `firebaseConfig` | 선택. 기본은 TalkFlow 내장 (덮어써도 발송 안 됨) |
| `enablePushNotifications` | `vapidKey` | 선택. 기본은 TalkFlow 내장 |
| `enablePushNotifications` | `serviceWorkerPath` | 선택. 기본 `'/firebase-messaging-sw.js'` |

### 리소스 정리

앱을 종료하거나 컴포넌트가 언마운트될 때 꼭 호출하세요. 메모리 누수를 방지하고 서버 리소스를 정리합니다.

```javascript
// WebSocket 연결 종료, 진행 중인 통화 종료, 모든 구독/리스너 해제
await client.destroy();
```

### 브라우저 지원

| 브라우저 | 최소 버전 |
|---|---|
| Chrome | 70+ |
| Firefox | 65+ |
| Safari | 13+ |
| Edge | 79+ |

### TypeScript 지원

```typescript
import TalkFlowClient, { ConnectionState, ChatMessageType } from '@vibexnpm/talkflow';

const client = new TalkFlowClient({
    apiKey: 'ck-xxxx',
    projectId: 'your-project-id'
});
```

타입 정의는 `dist/index.d.ts`에 있습니다.
