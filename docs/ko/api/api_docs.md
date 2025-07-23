# LiveStyler API 참조 문서

LiveStyler는 실시간 영상 처리를 위한 **웹소켓(WebSocket) API**와 필터 같은 리소스를 관리하기 위한 **REST API**, 두 가지 형태의 API를 제공합니다.

- **웹소켓 API**: WebRTC를 통해 실시간으로 비디오 스트림을 주고받는 데 사용됩니다. SDK에 이미 통합되어 있어 대부분의 경우 직접 다룰 필요가 없습니다.
- **REST API**: 필터 목록을 조회하는 등 서비스의 메타데이터를 관리하는 데 사용됩니다.

---

## **인증 (Authentication)**

LiveStyler API를 사용하기 위해서는 **API 시크릿 키**가 필요합니다. API 시크릿 키는 비즈니스 문의나 관리 페이지를 통해 발급받을 수 있습니다.

### **웹소켓 API 인증**

웹소켓 연결이 성공적으로 이루어지면, 가장 먼저 서버에 인증을 요청해야 합니다. 인증은 발급받은 `API 시크릿 키`를 `CREDENTIAL` 메시지에 담아 전송하는 방식으로 이루어집니다.

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
    인증 정보가 유효하지 않을 경우, 서버는 별도의 오류 메시지 없이 웹소켓 연결을 즉시 종료합니다.

### **REST API 인증**

REST API는 **Basic Authentication**을 사용합니다. `Authorization` 헤더에 API 시크릿 키를 담아 요청을 보내야 합니다.

**인증 헤더 생성 과정:**

1.  **`API 시크릿 키`** 뒤에 콜론(`:`)을 붙여 문자열을 만듭니다. 비밀번호 부분은 비워둡니다.
    - 예: `your_api_key_here:`

2.  생성한 문자열을 **Base64로 인코딩**합니다.
    - 예: `eW91cl9hcGlfa2V5X2hlcmU6`
    > **Tip:** Unix/Linux/macOS 터미널에서 다음 명령어로 쉽게 인코딩할 수 있습니다.
    > ```bash
    > echo -n 'your_api_key_here:' | base64
    > ```

3.  인코딩된 문자열 앞에 `Basic `을 붙여 `Authorization` 헤더 값을 완성합니다.
    - 예: `Authorization: Basic eW91cl9hcGlfa2V5X2hlcmU6`

**cURL 예시:**

다음은 `cURL`을 사용하여 필터 목록을 조회하는 API를 호출하는 전체 예제입니다.

```bash
curl --location 'https://api-dhgf98v28j6i.livestyler.ai/filter-category/active' \
--header 'Authorization: Basic eW91cl9hcGlfa2V5X2hlcmU6'
```

---

## **웹소켓 API**

WebRTC 통신을 위한 API입니다. 기본 연결 주소는 `wss://bridge-dhgf98v28j6i.livestyler.ai/client` 입니다.

> 이 API는 SDK에 이미 통합되어 있으므로, SDK를 사용하면 이 API를 직접 호출할 필요 없이 WebRTC 기능을 바로 활용할 수 있습니다.

### **메시지 종류**

#### 클라이언트 → 서버

- **`offer`**: WebRTC offer 정보를 서버로 전송합니다.
    ```json
    {
      "type": "offer",
      "payload": {
        "data": {}
      }
    }
    ```
  - 성공 시,
      - 서버가 프로비저닝될 경우 `{"type":"server_provisioning"}` 메시지가 전달됩니다.
      - 기존 서버를 사용할 경우, 별도 메시지가 없습니다.
  - 실패 시, `error` 타입 메시지가 전달됩니다.
      - `Maximum pool size reached. Cannot assign a new node.`: 새 노드를 할당할 수 없을 때 발생합니다.
      - `Failed to start a stopped node.`: 정지된 노드를 시작하지 못했을 때 발생합니다.

#### 서버 → 클라이언트

- **`answer`**: WebRTC answer 정보를 클라이언트로 전송합니다.
    ```json
    {
      "type": "answer",
      "payload": {
        "{answer data}"
      }
    }
    ```

#### 양방향

- **`ice_candidate`**: WebRTC ICE candidate 정보를 양방향으로 교환합니다.
    ```json
    {
      "type": "ice_candidate",
      "payload": {
        "{ice candidate data}"
      }
    }
    ```

---

## **REST API**

LiveStyler의 필터 등 메타데이터를 관리하는 API입니다. 모든 REST API의 기본 URL은 `https://api-dhgf98v28j6i.livestyler.ai` 입니다.

### **엔드포인트 (Endpoints)**

#### `GET /filter-category/active`

- **설명**: 현재 활성화된 모든 필터 카테고리와 각 카테고리에 속한 필터 목록을 함께 조회합니다.
- **성공 응답 (Success Response)**: `200 OK`
    ```json
    [
      {
        "filter_category_id": "01JXYDX55DHRY0T0RSDH068BYK",
        "organization_id": "01JXY3YG23J6D9PEHBN3DTGFQX",
        "name_ko": "로맨틱",
        "name_en": "romantic",
        "description": "로맨틱 카테고리",
        "is_active": true,
        "created_at": "2025-06-17T07:50:51.313Z",
        "updated_at": "2025-06-17T07:50:51.313Z",
        "deleted_at": null,
        "filters": [
          {
            "filter_id": "01JXYE1C805H1YAY7DTY1K6PHW",
            "organization_id": "01JXY3YG23J6D9PEHBN3DTGFQX",
            "filter_category_id": "01JXYDX55DHRY0T0RSDH068BYK",
            "code": "filter_01",
            "name_ko": "로맨틱 1",
            "name_en": "romantic 1",
            "image_url": "https://example.com/image.jpg",
            "is_active": true,
            "is_recommended": false,
            "created_at": "2025-06-17T07:53:09.634Z",
            "updated_at": "2025-06-17T07:53:09.634Z",
            "deleted_at": null
          }
        ]
      },
      {
        "filter_category_id": "01JXYDYQWWG8SKNK1ENV4EKVCE",
        "organization_id": "01JXY3YG23J6D9PEHBN3DTGFQX",
        "name_ko": "만화",
        "name_en": "cartoon",
        "description": "만화 카테고리",
        "is_active": true,
        "created_at": "2025-06-17T07:51:43.261Z",
        "updated_at": "2025-06-17T07:51:43.261Z",
        "deleted_at": null,
        "filters": []
      }
    ]
    ```
- **실패 응답 (Error Response)**: `401 Unauthorized`
    ```json
    {
      "message": "Invalid credentials"
    }
    ```