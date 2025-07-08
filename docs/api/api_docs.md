# TaskFlow API Documentation

## Overview

TaskFlow API는 작업 관리 시스템을 위한 RESTful API입니다. 이 API를 통해 프로젝트, 작업, 사용자를 관리할 수 있습니다.

## Base URL

```
https://api.taskflow.com/v1
```

## Authentication

모든 API 요청은 Bearer 토큰 인증을 사용합니다.

```http
Authorization: Bearer YOUR_API_TOKEN
```

## Response Format

모든 응답은 JSON 형식으로 제공됩니다.

### Success Response

```json
{
  "success": true,
  "data": {
    // 응답 데이터
  },
  "meta": {
    "timestamp": "2025-07-04T12:00:00Z",
    "version": "1.0"
  }
}
```

### Error Response

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "요청 데이터가 올바르지 않습니다.",
    "details": [
      {
        "field": "title",
        "message": "제목은 필수 항목입니다."
      }
    ]
  }
}
```

## Rate Limiting

API는 다음과 같은 속도 제한이 있습니다:

- **일반 요청**: 분당 100회
- **인증 요청**: 분당 10회

속도 제한에 도달하면 `429 Too Many Requests` 상태 코드가 반환됩니다.

## Endpoints

### Projects

#### Get All Projects

프로젝트 목록을 조회합니다.

```http
GET /projects
```

**Query Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | 페이지 번호 (기본값: 1) |
| `limit` | integer | 페이지당 항목 수 (기본값: 20, 최대: 100) |
| `status` | string | 프로젝트 상태 (`active`, `completed`, `archived`) |

**Response**

```json
{
  "success": true,
  "data": {
    "projects": [
      {
        "id": "proj_123",
        "title": "웹사이트 리뉴얼",
        "description": "회사 웹사이트 전면 리뉴얼 프로젝트",
        "status": "active",
        "created_at": "2025-07-01T10:00:00Z",
        "updated_at": "2025-07-04T15:30:00Z",
        "owner": {
          "id": "user_456",
          "name": "김철수",
          "email": "kimcs@example.com"
        },
        "tasks_count": 12,
        "progress": 65
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 45,
      "total_pages": 3
    }
  }
}
```

#### Create Project

새 프로젝트를 생성합니다.

```http
POST /projects
```

**Request Body**

```json
{
  "title": "새 프로젝트",
  "description": "프로젝트 설명",
  "status": "active",
  "owner_id": "user_456",
  "due_date": "2025-12-31T23:59:59Z"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "project": {
      "id": "proj_789",
      "title": "새 프로젝트",
      "description": "프로젝트 설명",
      "status": "active",
      "created_at": "2025-07-04T16:00:00Z",
      "updated_at": "2025-07-04T16:00:00Z",
      "owner": {
        "id": "user_456",
        "name": "김철수",
        "email": "kimcs@example.com"
      },
      "tasks_count": 0,
      "progress": 0
    }
  }
}
```

#### Get Project

특정 프로젝트의 상세 정보를 조회합니다.

```http
GET /projects/{project_id}
```

**Path Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `project_id` | string | 프로젝트 ID |

**Response**

```json
{
  "success": true,
  "data": {
    "project": {
      "id": "proj_123",
      "title": "웹사이트 리뉴얼",
      "description": "회사 웹사이트 전면 리뉴얼 프로젝트",
      "status": "active",
      "created_at": "2025-07-01T10:00:00Z",
      "updated_at": "2025-07-04T15:30:00Z",
      "owner": {
        "id": "user_456",
        "name": "김철수",
        "email": "kimcs@example.com"
      },
      "tasks": [
        {
          "id": "task_101",
          "title": "UI 디자인",
          "status": "completed",
          "assignee": {
            "id": "user_789",
            "name": "이영희"
          }
        }
      ],
      "progress": 65
    }
  }
}
```

### Tasks

#### Get All Tasks

작업 목록을 조회합니다.

```http
GET /tasks
```

**Query Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `project_id` | string | 프로젝트 ID로 필터링 |
| `assignee_id` | string | 담당자 ID로 필터링 |
| `status` | string | 작업 상태 (`todo`, `in_progress`, `completed`) |
| `page` | integer | 페이지 번호 |
| `limit` | integer | 페이지당 항목 수 |

**Response**

```json
{
  "success": true,
  "data": {
    "tasks": [
      {
        "id": "task_101",
        "title": "UI 디자인",
        "description": "메인 페이지 UI 디자인 작업",
        "status": "completed",
        "priority": "high",
        "created_at": "2025-07-01T10:00:00Z",
        "updated_at": "2025-07-03T14:30:00Z",
        "due_date": "2025-07-05T18:00:00Z",
        "project": {
          "id": "proj_123",
          "title": "웹사이트 리뉴얼"
        },
        "assignee": {
          "id": "user_789",
          "name": "이영희",
          "email": "leeyh@example.com"
        }
      }
    ]
  }
}
```

#### Create Task

새 작업을 생성합니다.

```http
POST /tasks
```

**Request Body**

```json
{
  "title": "새 작업",
  "description": "작업 설명",
  "project_id": "proj_123",
  "assignee_id": "user_789",
  "priority": "medium",
  "due_date": "2025-07-10T18:00:00Z"
}
```

#### Update Task

작업을 수정합니다.

```http
PUT /tasks/{task_id}
```

**Request Body**

```json
{
  "title": "수정된 작업 제목",
  "status": "in_progress",
  "priority": "high"
}
```

### Users

#### Get Current User

현재 인증된 사용자의 정보를 조회합니다.

```http
GET /users/me
```

**Response**

```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_456",
      "name": "김철수",
      "email": "kimcs@example.com",
      "role": "admin",
      "created_at": "2025-06-01T09:00:00Z",
      "last_login": "2025-07-04T08:30:00Z"
    }
  }
}
```

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `VALIDATION_ERROR` | 400 | 요청 데이터 검증 실패 |
| `UNAUTHORIZED` | 401 | 인증 토큰이 없거나 유효하지 않음 |
| `FORBIDDEN` | 403 | 접근 권한이 없음 |
| `NOT_FOUND` | 404 | 요청한 리소스를 찾을 수 없음 |
| `RATE_LIMIT_EXCEEDED` | 429 | 속도 제한 초과 |
| `INTERNAL_ERROR` | 500 | 서버 내부 오류 |

## Webhooks

TaskFlow API는 다음 이벤트에 대한 웹훅을 지원합니다:

- `project.created`
- `project.updated`
- `task.created`
- `task.updated`
- `task.completed`

웹훅 설정은 대시보드에서 관리할 수 있습니다.

## SDK 및 도구

- [JavaScript SDK](https://github.com/taskflow/js-sdk)
- [Python SDK](https://github.com/taskflow/python-sdk)
- [Postman Collection](https://documenter.getpostman.com/view/taskflow)

## 지원

문의사항이 있으시면 다음 채널을 통해 연락해주세요:

- 이메일: support@taskflow.com
- 문서: https://docs.taskflow.com
- GitHub Issues: https://github.com/taskflow/api/issues