# REST API

## 시작하기
기본 URL은 `https://api-paoqov032tbx.livestyler.ai` 입니다.

### **인증**

REST API는 **Basic Authentication**을 사용합니다. `Authorization` 헤더에 API 시크릿 키를 담아 요청을 보내야 합니다.

**인증 헤더 생성 과정:**

1.  **`API 시크릿 키`** 뒤에 콜론(`:`)을 붙여 문자열을 만듭니다. 비밀번호 부분은 비워둡니다.
    - 예: `your_api_key_here:`

2.  생성한 문자열을 **Base64로 인코딩**합니다.
    - 예: `eW91cl9hcGlfa2V5X2hlcmU6`
    !!! tip "Tip: 스크립트를 이용한 Base64 인코딩 방법"
        Unix/Linux/macOS 터미널에서 다음 명령어로 쉽게 인코딩할 수 있습니다.
        ```bash
        echo -n 'your_api_key_here:' | base64
        ```

3.  인코딩된 문자열 앞에 `Basic `을 붙여 `Authorization` 헤더 값을 완성합니다.
    - 예: `Authorization: Basic eW91cl9hcGlfa2V5X2hlcmU6`

## **REST API**

LiveStyler의 필터 등 메타데이터를 관리하는 API입니다. 모든 REST API의 기본 URL은 `https://api-paoqov032tbx.livestyler.ai` 입니다.

### **엔드포인트**

#### `GET /filter-category/active`

- **설명**: 현재 활성화된 모든 필터 카테고리와 각 카테고리에 속한 필터 목록을 함께 조회합니다.
!!! success "**성공 응답**: `200 OK`"
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
!!! failure "**실패 응답**: `401 Unauthorized`"
    ```json
    {
      "message": "Invalid credentials"
    }
    ```
    
**cURL 예시:**

다음은 `cURL`을 사용하여 필터 목록을 조회하는 API를 호출하는 전체 예제입니다.

```bash
curl --location 'https://api-paoqov032tbx.livestyler.ai/filter-category/active' \
--header 'Authorization: Basic eW91cl9hcGlfa2V5X2hlcmU6'
```