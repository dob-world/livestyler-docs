# WebSocket API

## Getting Started

The base connection address is `wss://bridge-paoqov032tbx.livestyler.ai/client`.

### **Authentication**

Once the WebSocket connection is successfully established, you must first request authentication from the server. Authentication is done by sending the issued `API Secret Key` in a `CREDENTIAL` message.

#### **Authentication Request (Client → Server)**

The client sends a message to the server in the following format.

- **`type`**: `CREDENTIAL`
- **`payload.credential`**: Your issued `API Secret Key`

```json
{
  "type": "CREDENTIAL",
  "payload": {
    "credential": "sk_TEST01_my0Secret1Token2Value3"
  }
}
```

#### **Authentication Response (Server → Client)**

The server responds to the client's authentication request as follows.

!!! success "Authentication Success"
    If the authentication information is valid, the server sends an `auth_success` message as shown below. After receiving this message, you can start exchanging other API messages.

    ```json
    {
      "type": "auth_success"
    }
    ```

!!! failure "Authentication Failure"
    If the authentication information is invalid, the server will immediately close the WebSocket connection without sending a separate error message.

---

## **WebSocket API**

This is the API for WebRTC communication.

!!! info "For SDK Users"
    This API is already integrated into the SDK, so if you are using the SDK, you can use the WebRTC features directly without needing to call this API yourself.

### **Message Types**

#### **`session_start`**

(Client → Server) Sends WebRTC session start information to the server.
```json
{
  "type": "session_start"
}
```

!!! information "Session Start Information"
    This message is used to initiate a WebRTC session. The server will respond with the necessary information to proceed with the session.
    The following messages may be sent by the server in response:
    - `{ "type": "node_status", "status": "pending"}`: Indicates that the server is provisioning.
    - `{ "type": "node_status", "status": "full"}`: Indicates that the server's resources are full.
    - `{ "type": "node_status", "status": "waiting"}`: Indicates that the server instance is ready.

#### **`offer`**

(Client → Server) Sends WebRTC offer information to the server.
```json
{
  "type": "offer",
  "sdp": "{offer sdp}"
}
```

!!! success "On Success"
    - If a server is being provisioned, a `{"type":"server_provisioning"}` message is sent.
    - If an existing server is used, there is no separate message.

!!! failure "On Failure"
    - An error message is sent.
        - `Maximum pool size reached. Cannot assign a new node.`: Occurs when a new node cannot be assigned.
        - `Failed to start a stopped node.`: Occurs when a stopped node fails to start.

#### **`answer`**

(Server → Client) Sends WebRTC answer information to the client.
```json
{
  "type": "answer",
  "sdp": "{answer sdp}"
}
```


#### **`candidate`**

(Bidirectional) Exchanges WebRTC ICE candidate information in both directions.
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