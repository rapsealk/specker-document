# SPECKER API

* 서버 주소는 http://XXX.XXX.XXX.XXX:XXXX/ 으로 통일됩니다.
* Header에는 Firebase Token을 넣어주세요.
* 인증 과정에서 유저 정보가 데이터베이스에 없으면 자동으로 생성/저장합니다.
* (기기 로그인 시 일회성으로 호출하는 유저 생성/저장 API로 대체할 계획입니다.)
* 자체 데이터 구조 Feed, 등은 아래에 명세합니다.

## Feed
### POST /sendHomeFeed
* 피드를 등록할 때 사용합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        html: {
            markup: "<html>String</html>"
        }
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'FEED_FAILED'
}
```

### POST /getHomeFeed
* 피드를 불러올 때 사용합니다.
```
@Body
{
    body: {
        nextIndex: Date
    }
}
```
* 응답은 다음과 같습니다.
```
{
    data: [+Feed],  // TODO
    nextIndex: "String",
    result: 'AUTH_USER'
}
```

## Marker
### POST /addMarker
* 마커 정보를 등록할 때 사용합니다.
```
// @Header("Authorization") authorization
@Body
{
    body: {
        latitude: 37.3358,
        longitude: 126.5840,
        title: "String"
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'MARKER_FAILED'
}
```

### GET /getMarker
#### [TODO] POST 방식으로 수정될 예정입니다.
* 마커 정보를 가져올 때 사용합니다.
* A~B 사이의 구간에 한하여 가져옵니다.
```
// @Header("Authorization") authorization
@Params
{
    latitudeA: 0.0,
    longitudeA: 0.0,
    latitudeB: 0.0,
    longitudeB: 0.0
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'MARKER_FAILED',
    markers: [+Marker]  // TODO
}
```

## Chat
### POST /createChat
* 채팅방을 최초 생성할 때 호출합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        participants: ["String"]    // 본인을 제외한 참여자의 uid가 들어갑니다.
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'CREATE_CHATROOM_ERROR',
    _id: "String"   // ObjectId
}
```

### POST /getChatrooms
* 유저가 참여한 채팅방의 목록을 가져올 때 호출합니다.
```
@Header("Authorization") authorization
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'GET_CHATLIST_ERROR',
    chatrooms: [
        {
            _id: "String",  // ObjectId
            participants: 2,
            lastChat: "String",
            lastTimestamp: 1509778106267
        }
    ]
}
```

### POST /sendChat
* 채팅을 전달할 때 호출합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        chatroom: "String", // 채팅방의 ID가 들어갑니다.
        content: "String"   // 입력한 채팅 내용이 들어갑니다.
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'UPDATE_CHAT_ERROR'
}
```

### POST /getChat
* 해당 채팅방의 특정 시간 이후의 채팅 내용을 가져올 때 호출합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        chatroom: "String", // 채팅방의 ID가 들어갑니다.
        timestamp: Date
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'GET_CHAT_ERROR',
    logs: [+ChatLog]
}
```

## CUSTOM DATA CLASSES
* 각 데이터가 MongoDB에 저장되는 형식은 다음과 같습니다.

### FEED
```
{
    user: {
        _id: "String",  (ObjectId)
        name: "String",
        gravatar: "String"
    },
    content: "String"
}
```
### MARKER
```
{
    position: {
        latitude: 0.0,
        longitude: 0.0
    },
    content: {
        title: "String"
    },
    timestamp: Date
}
```
### CHATROOM
```
{
    participants: ["String"],   (ObjectId)
    logs: [+ChatLog]
}
```
### CHATLOG
```
{
    author: "String",   (ObjectId)
    content: "String",
    timestamp: Date
}
```