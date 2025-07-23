# REST API

## Getting Started
The base URL is `https://api-paoqov032tbx.livestyler.ai`.

### **Authentication**

The REST API uses **Basic Authentication**. You must send requests with your API Secret Key in the `Authorization` header.

**Authorization Header Creation Process:**

1.  Create a string by appending a colon (`:`) to your **`API Secret Key`**. Leave the password part empty.
    -   Example: `your_api_key_here:`

2.  **Base64-encode** the created string.
    -   Example: `eW91cl9hcGlfa2V5X2hlcmU6`
    !!! tip "Tip: How to Base64-encode using a script"
        You can easily encode it in a Unix/Linux/macOS terminal with the following command.
        ```bash
        echo -n 'your_api_key_here:' | base64
        ```

3.  Complete the `Authorization` header value by prefixing the encoded string with `Basic `.
    -   Example: `Authorization: Basic eW91cl9hcGlfa2V5X2hlcmU6`

## **REST API**

This API manages metadata for LiveStyler, such as filters. The base URL for all REST APIs is `https://api-paoqov032tbx.livestyler.ai`.

### **Endpoints**

#### `GET /filter-category/active`

- **Description**: Retrieves all currently active filter categories along with the list of filters belonging to each category.
!!! success "**Success Response**: `200 OK`"
    ```json
    [
      {
        "filter_category_id": "01JXYDX55DHRY0T0RSDH068BYK",
        "organization_id": "01JXY3YG23J6D9PEHBN3DTGFQX",
        "name_ko": "로맨틱",
        "name_en": "romantic",
        "description": "romantic category",
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
        "description": "cartoon category",
        "is_active": true,
        "created_at": "2025-06-17T07:51:43.261Z",
        "updated_at": "2025-06-17T07:51:43.261Z",
        "deleted_at": null,
        "filters": []
      }
    ]
    ```
!!! failure "**Failure Response**: `401 Unauthorized`"
    ```json
    {
      "message": "Invalid credentials"
    }
    ```
    
**cURL Example:**

The following is a complete example of calling the API to retrieve the filter list using `cURL`.

```bash
curl --location 'https://api-paoqov032tbx.livestyler.ai/filter-category/active' \
--header 'Authorization: Basic eW91cl9hcGlfa2V5X2hlcmU6'
``` 