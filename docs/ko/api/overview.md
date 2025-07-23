# LiveStyler API

LiveStyler는 실시간 영상 처리를 위한 **WebSocket API**와 필터 같은 리소스를 관리하기 위한 **REST API**, 두 가지 형태의 API를 제공합니다.

- [REST API](./rest.md): 필터 목록을 조회하는 등 서비스의 메타데이터를 관리하는 데 사용됩니다.
- [WebSocket API](./websocket.md) : WebRTC를 통해 실시간으로 비디오 스트림을 주고받는 데 사용됩니다.

---

## 시작하기 전에

!!! info "API 시크릿 키 발급"

    이 API를 사용하기 위해서는 **API 시크릿 키**가 필요합니다. API 시크릿 키는 비즈니스 문의나 관리페이지를 통해 발급받을 수 있습니다.

### API 연동을 위한 기본 URL

- **WebSocket**: `wss://bridge-paoqov032tbx.livestyler.ai/client`
- **REST API**: `https://api-paoqov032tbx.livestyler.ai`
