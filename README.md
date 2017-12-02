# SPECKER API

* 서버 주소는 http://XXX.XXX.XXX.XXX:XXXX/ 으로 통일됩니다.
* Header에는 Firebase Token을 넣어주세요.
* 클라이언트에서 처리할 때는 uid, 서버에서 처리할 때는 ObjectId로 구분하는 방향으로 진행합니다.
* 자체 데이터 구조 Feed, 등은 아래에 명세합니다.
// * 인증 과정에서 유저 정보가 데이터베이스에 없으면 자동으로 생성/저장합니다.
// * (기기 로그인 시 일회성으로 호출하는 유저 생성/저장 API로 대체할 계획입니다.)

## User

### POST /signUp
*
```
@Header("Authorization") authorization
@Body
{
    body: {
        email: "String",
        name: "String",
        // optional below
        password: "String",
        age: 0,
        sex: "String",
        phone: "String",
        address: "String"
    }
}
```
*
```
{
    result: 'ok'
}
```

### POST /createUser - deprecated ("signUp")
* Firebase 인증 후 최초 1회 호출합니다.
* 서버 데이터베이스(MongoDB)에 유저 정보를 생성합니다.
```
@Header("Authorization") authorization
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'FAILED_CHECK_USER',
    error: 
}
```

### POST /getObjectId
* 자신의 ObjectId를 가져올 때 호출합니다.
```
@Header("Authorization") authorization
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'GET_OBJECTID_FAILED',
    objectId: "String"  // ObjectId
}
```

## Friend
### POST /getFriendsList    - 2017/11/24 01:11
* 친구 목록을 불러올 때 호출합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        timestamp: 0
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'FAILED_GET_FRIENDS_LIST',
    friends: [
        {
            _id: "String",  // ObjectId
            name: "String",
            gravatar: "String",
            timestamp: 0
        }
    ]
}
```

### POST /searchUser
* 친구 추가를 위해 유저를 검색할 때 호출합니다.
* 이메일을 이용하여 유저를 검색합니다. (v0.1)
```
@Header("Authorization") authorization
@Body
{
    body: {
        keyword: "String"
    }
}
```
* 응답은 다음과 같습니다.
```
// On Success
{
    result: 'ok',
    users: [
        {
            uid: "String",      // Firebase.uid
            name: "String",
            email: "String",
            gravatar: "String"
        }
    ]
}
// On Failure
{
    result: 'FAILED_SEARCH_USER',
    error: error
}
```

### POST /addFriend
* 친구를 추가할 때 호출합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        friend: "String",       // Firebase.uid
        timestamp: 0
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'FAILED_ADD_FRIEND'
}
```

### POST /removeFriend      - 2017/11/26 15:08
* 친구를 삭제할 때 호출합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        friend: "String"        // Firebase.uid
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'FAILED_REMOVE_FRIEND'
}
```

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
* 마커를 등록하는 과정에서 팀도 생성합니다. (2017-11-21 22:55)
```
@Header("Authorization") authorization
@Body
{
    body: {
        latitude: 37.3358,
        longitude: 126.5840,
        title: "String",
        max_count: 0,
        // members: ["String"]  // Firebase uid
        snippet: "String",
        detail: "String"
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'MARKER_FAILED',
    team: "String",     // ObjectId
    marker: "String"    // ObjectId
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
    markers: [
        {
            team: "String",     // ObjectId
            leader: "String",   // ObjectId
            teammates: {
                max: 0,
                count: 0,
                male: 0,
                female: 0
            },
            position: {
                latitude: 0.0,
                longitude: 0.0
            },
            content: {
                title: "String",
                snippet: "String"
            },
            timestamp: 0
        }
    ]
}
```

### POST /deleteMarker
* 마커를 삭제할 때 호출합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        id: "String"    // ObjectId
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'DELETE_MARKER_FAILED'
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

### POST /createChatWithObjectId
*
```
@Header("Authorization") authorization
@Body
{
    body: {
        participants: ["String"]    // 본인을 제외한 참여자의 ObjectId가 들어갑니다.
    }
}
```
*
```
{
    result: 'ok' || error,
    _id: "String"
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

### POST /removeChatroom
*
```
@Header("Authorization") authorization
@Body
{
    body: {
        chatroom: "String"  // ObjectId
    }
}
```
*
```
{
    result: 'ok' || 'REMOVE_CHATROOM_ERROR'
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
    logs: [
        {
            _id: "String",      // ObjectId
            author: "String",   // Firebase.uid
            content: "String",
            timestamp: 0
        }
    ]
}
```

## Team
### POST /createTeam - deprecated 2017/11/30 13:23
* 팀을 생성할 때 호출합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        name: "String",
        members: ["String"],    // uid
        room: "String"          // ObjectId
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'FAILED_CREATE_TEAM'
}
```

### POST /searchTeam
* 팀을 검색할 때 호출합니다.
```
@Header("Authorization") authorization
@Body
{
    body: {
        keyword: "String"
        // optional - 범위가 주어질 경우
        latitudeA: 0.0,
        longitudeA: 0.0,
        latitudeB: 0.0,
        longitudeB: 0.0
    }
}
```
* 응답은 다음과 같습니다.
```
{
    result: 'ok' || 'FAILED_SEARCH_TEAM',
    teams: [
        name: "String",
        leader: "String",
        members: ["String"],    // uid
        room: "String"
    ]
}
```

### POST /getTeam
*
```
@Header("Authorization") authorization
@Body
{
    body: {
        team: "String"  // ObjectId
    }
}
```
*
```
{
    result: 'ok' || 'FAILED_GET_TEAM',
    team: {

    }
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