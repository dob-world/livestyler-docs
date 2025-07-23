# WebSocket API

## Getting Started

The default connection address is `wss://bridge-paoqov032tbx.livestyler.ai/client`.

### **Authentication**

Once a WebSocket connection is successfully established, you must first authenticate with the server. Authentication is done by sending the issued `API Secret Key` in a `CREDENTIAL` message.

#### **Authentication Request (Client → Server)**

The client sends a message to the server in the following format.

- **`type`**: `CREDENTIAL`
- **`payload.credential`**: The issued `API Secret Key`

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
    If the authentication information is valid, the server sends an `auth_success` message like the one below. After receiving this message, you can start exchanging other API messages.

    ```json
    {
      "type": "auth_success"
    }
    ```

!!! failure "Authentication Failure"
    If the authentication information is invalid, the server immediately terminates the WebSocket connection without a separate error message.

---

## **WebSocket API**

This is the API for WebRTC communication.

!!! info "For SDK Users"
    This API is already integrated into the SDK, so if you use the SDK, you can use the WebRTC features directly without calling this API.

### **Message Types**


#### **`offer`**

(Client → Server) Sends WebRTC offer information to the server.
```json
{
  "type": "offer",
  "payload": {
    "data": {}
  }
}
```

!!! success "On Success"
    - If a server is being provisioned, a `{"type":"server_provisioning"}` message will be sent.
    - If an existing server is used, there is no separate message.

!!! failure "On Failure"
    - An error message is sent.
        - `Maximum pool size reached. Cannot assign a new node.`: Occurs when a new node cannot be assigned.
        - `Failed to start a stopped node.`: Occurs when a stopped node could not be started.

#### **`answer`**

(Server → Client) Sends WebRTC answer information to the client.
```json
{
  "type": "answer",
  "payload": {
    "{answer data}"
  }
}
```


#### **`ice_candidate`**

(Bidirectional) Exchanges WebRTC ICE candidate information in both directions.
```json
{
  "type": "ice_candidate",
  "payload": {
    "{ice candidate data}"
  }
}
``` 