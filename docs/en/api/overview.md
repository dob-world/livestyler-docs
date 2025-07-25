# LiveStyler API

LiveStyler provides two types of APIs: a **WebSocket API** for real-time video processing and a **REST API** for managing resources like filters.

- [REST API](./rest.md): Used to manage service metadata, such as retrieving a list of filters.
- [WebSocket API](./websocket.md): Used to send and receive video streams in real-time via WebRTC.

---

## Before You Start

!!! info "Issuing an API Secret Key"

    To use this API, you need an **API Secret Key**. The API Secret Key can be issued through a business inquiry or the management page.

### Base URLs for API Integration

- **WebSocket**: `wss://bridge-paoqov032tbx.livestyler.ai/client`
- **REST API**: `https://api-paoqov032tbx.livestyler.ai` 