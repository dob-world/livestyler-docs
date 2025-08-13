# WebSocket API

## 시작하기

기본 연결 주소는 `wss://bridge-paoqov032tbx.livestyler.ai/client` 입니다.

### **인증**

WebSocket 연결이 성공적으로 이루어지면, 가장 먼저 서버에 인증을 요청해야 합니다. 인증은 발급받은 `API 시크릿 키`를 `CREDENTIAL` 메시지에 담아 전송하는 방식으로 이루어집니다.

#### **인증 요청 (Client → Server)**

클라이언트는 아래와 같은 형식의 메시지를 서버로 전송합니다.

- **`type`**: `CREDENTIAL`
- **`payload.credential`**: 발급받은 `API 시크릿 키`

```json
{
  "type": "CREDENTIAL",
  "payload": {
    "credential": "sk_TEST01_my0Secret1Token2Value3"
  }
}
```

#### **인증 응답 (Server → Client)**

서버는 클라이언트의 인증 요청에 대해 다음과 같이 응답합니다.

!!! success "인증 성공 (Success)"
    인증 정보가 유효하면 서버는 아래와 같은 `auth_success` 메시지를 전송합니다. 이 메시지를 수신한 후부터 다른 API 메시지를 주고받을 수 있습니다.

    ```json
    {
      "type": "auth_success"
    }
    ```

!!! failure "인증 실패 (Failure)"
    인증 정보가 유효하지 않을 경우, 서버는 별도의 오류 메시지 없이 WebSocket 연결을 즉시 종료합니다.

---

## **WebSocket API**

WebRTC 통신을 위한 API입니다.

!!! info "SDK 사용자를 위한 안내"
    이 API는 SDK에 이미 통합되어 있으므로, SDK를 사용하면 이 API를 직접 호출할 필요 없이 WebRTC 기능을 바로 활용할 수 있습니다.

### **메시지 종류**

#### **`session_start`**

(클라이언트 → 서버) WebRTC 세션 시작 정보를 서버로 전송합니다.
```json
{
  "type": "session_start"
}
```

!!! information "세션 시작 정보"
    - 서버의 인스턴스 상태에 따라 다음과 같은 메시지가 전달됩니다.
      - `{ "type": "node_status", "status": "pending"}`: 서버가 프로비저닝 중임을 나타냅니다.
      - `{ "type": "node_status", "status": "full"}`: 서버의 리소스가 가득 차 있음을 나타냅니다.
      - `{ "type": "node_status", "status": "waiting"}`: 서버 인스턴스 준비가 완료된 상태를 나타냅니다.

#### **`offer`**

(클라이언트 → 서버) WebRTC offer 정보를 서버로 전송합니다.
```json
{
  "type": "offer",
  "sdp": "{offer sdp}"
}
```

!!! success "성공 시"
    - 서버가 프로비저닝될 경우 `{"type":"server_provisioning"}` 메시지가 전달됩니다.
    - 기존 서버를 사용할 경우, 별도 메시지가 없습니다.

!!! failure "실패 시"
    - 오류 메시지가 전달됩니다.
        - `Maximum pool size reached. Cannot assign a new node.`: 새 노드를 할당할 수 없을 때 발생합니다.
        - `Failed to start a stopped node.`: 정지된 노드를 시작하지 못했을 때 발생합니다.

#### **`answer`**

(서버 → 클라이언트) WebRTC answer 정보를 클라이언트로 전송합니다.
```json
{
  "type": "answer",
  "sdp": "{answer sdp}"
}
```


#### **`candidate`**

(양방향) WebRTC ICE candidate 정보를 양방향으로 교환합니다.
```json
{
  "type": "candidate",
  "candidate": {
    "sdpMid": "{ice candidate sdpMid}",
    "sdpMLineIndex": {ice candidate sdpMLineIndex},
    "candidate": "{ice candidate}"
  }
}
```
