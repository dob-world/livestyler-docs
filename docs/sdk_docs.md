# TaskFlow JavaScript SDK

TaskFlow API를 위한 공식 JavaScript SDK입니다. Node.js와 브라우저 환경에서 모두 사용할 수 있습니다.

## 설치

### npm

```bash
npm install @taskflow/sdk
```

### yarn

```bash
yarn add @taskflow/sdk
```

### CDN

```html
<script src="https://cdn.jsdelivr.net/npm/@taskflow/sdk@latest/dist/taskflow.min.js"></script>
```

## 빠른 시작

### 초기화

```javascript
import { TaskFlow } from '@taskflow/sdk';

const taskflow = new TaskFlow({
  apiKey: 'your-api-key',
  baseUrl: 'https://api.taskflow.com/v1' // 선택사항
});
```

### 환경 변수 사용

```javascript
// .env 파일
TASKFLOW_API_KEY=your-api-key

// 코드
const taskflow = new TaskFlow({
  apiKey: process.env.TASKFLOW_API_KEY
});
```

## 구성 옵션

```javascript
const taskflow = new TaskFlow({
  apiKey: 'your-api-key',
  baseUrl: 'https://api.taskflow.com/v1',
  timeout: 30000, // 30초 (기본값)
  retries: 3, // 재시도 횟수 (기본값)
  debug: false // 디버그 모드 (기본값: false)
});
```

## 프로젝트 관리

### 프로젝트 목록 조회

```javascript
try {
  const projects = await taskflow.projects.list({
    page: 1,
    limit: 20,
    status: 'active'
  });
  
  console.log('프로젝트 목록:', projects.data);
  console.log('페이지네이션:', projects.pagination);
} catch (error) {
  console.error('오류:', error.message);
}
```

### 프로젝트 생성

```javascript
try {
  const project = await taskflow.projects.create({
    title: '새 프로젝트',
    description: '프로젝트 설명',
    status: 'active',
    owner_id: 'user_456',
    due_date: '2025-12-31T23:59:59Z'
  });
  
  console.log('생성된 프로젝트:', project);
} catch (error) {
  console.error('프로젝트 생성 실패:', error.message);
}
```

### 프로젝트 상세 조회

```javascript
try {
  const project = await taskflow.projects.get('proj_123');
  console.log('프로젝트 상세:', project);
} catch (error) {
  if (error.code === 'NOT_FOUND') {
    console.error('프로젝트를 찾을 수 없습니다.');
  }
}
```

### 프로젝트 수정

```javascript
try {
  const updatedProject = await taskflow.projects.update('proj_123', {
    title: '수정된 프로젝트 제목',
    status: 'completed'
  });
  
  console.log('수정된 프로젝트:', updatedProject);
} catch (error) {
  console.error('프로젝트 수정 실패:', error.message);
}
```

### 프로젝트 삭제

```javascript
try {
  await taskflow.projects.delete('proj_123');
  console.log('프로젝트가 삭제되었습니다.');
} catch (error) {
  console.error('프로젝트 삭제 실패:', error.message);
}
```

## 작업 관리

### 작업 목록 조회

```javascript
try {
  const tasks = await taskflow.tasks.list({
    project_id: 'proj_123',
    status: 'todo',
    assignee_id: 'user_789'
  });
  
  console.log('작업 목록:', tasks.data);
} catch (error) {
  console.error('오류:', error.message);
}
```

### 작업 생성

```javascript
try {
  const task = await taskflow.tasks.create({
    title: '새 작업',
    description: '작업 설명',
    project_id: 'proj_123',
    assignee_id: 'user_789',
    priority: 'high',
    due_date: '2025-07-10T18:00:00Z'
  });
  
  console.log('생성된 작업:', task);
} catch (error) {
  console.error('작업 생성 실패:', error.message);
}
```

### 작업 상태 업데이트

```javascript
try {
  const task = await taskflow.tasks.update('task_101', {
    status: 'completed'
  });
  
  console.log('작업 완료:', task);
} catch (error) {
  console.error('작업 업데이트 실패:', error.message);
}
```

### 작업 완료 처리

```javascript
try {
  const task = await taskflow.tasks.complete('task_101');
  console.log('작업이 완료되었습니다:', task);
} catch (error) {
  console.error('작업 완료 실패:', error.message);
}
```

## 사용자 관리

### 현재 사용자 정보

```javascript
try {
  const user = await taskflow.users.me();
  console.log('현재 사용자:', user);
} catch (error) {
  console.error('사용자 정보 조회 실패:', error.message);
}
```

### 사용자 목록 조회

```javascript
try {
  const users = await taskflow.users.list({
    role: 'admin',
    page: 1,
    limit: 50
  });
  
  console.log('사용자 목록:', users.data);
} catch (error) {
  console.error('오류:', error.message);
}
```

## 오류 처리

SDK는 구조화된 오류 객체를 제공합니다:

```javascript
try {
  const project = await taskflow.projects.get('invalid-id');
} catch (error) {
  console.log('오류 코드:', error.code);
  console.log('오류 메시지:', error.message);
  console.log('HTTP 상태:', error.status);
  console.log('상세 정보:', error.details);
}
```

### 주요 오류 코드

- `VALIDATION_ERROR`: 요청 데이터 검증 실패
- `UNAUTHORIZED`: 인증 토큰이 유효하지 않음
- `FORBIDDEN`: 접근 권한이 없음
- `NOT_FOUND`: 리소스를 찾을 수 없음
- `RATE_LIMIT_EXCEEDED`: 속도 제한 초과
- `NETWORK_ERROR`: 네트워크 연결 문제
- `TIMEOUT_ERROR`: 요청 시간 초과

## 이벤트 및 콜백

### 이벤트 리스너

```javascript
// 요청 전 이벤트
taskflow.on('request', (config) => {
  console.log('요청 시작:', config.method, config.url);
});

// 응답 후 이벤트
taskflow.on('response', (response) => {
  console.log('응답 완료:', response.status);
});

// 오류 이벤트
taskflow.on('error', (error) => {
  console.error('API 오류:', error.message);
});
```

### 요청 인터셉터

```javascript
// 요청 인터셉터
taskflow.interceptors.request.use((config) => {
  // 모든 요청에 사용자 정의 헤더 추가
  config.headers['X-Client-Version'] = '1.0.0';
  return config;
});

// 응답 인터셉터
taskflow.interceptors.response.use(
  (response) => {
    // 성공 응답 처리
    return response;
  },
  (error) => {
    // 오류 응답 처리
    if (error.status === 401) {
      // 토큰 갱신 로직
      return refreshTokenAndRetry(error.config);
    }
    return Promise.reject(error);
  }
);
```

## TypeScript 지원

SDK는 완전한 TypeScript 지원을 제공합니다:

```typescript
import { TaskFlow, Project, Task, User } from '@taskflow/sdk';

const taskflow = new TaskFlow({
  apiKey: process.env.TASKFLOW_API_KEY!
});

// 타입 안전성이 보장됩니다
const project: Project = await taskflow.projects.get('proj_123');
const tasks: Task[] = await taskflow.tasks.list({ project_id: project.id });
```

### 인터페이스

```typescript
interface Project {
  id: string;
  title: string;
  description: string;
  status: 'active' | 'completed' | 'archived';
  created_at: string;
  updated_at: string;
  owner: User;
  tasks_count: number;
  progress: number;
}

interface Task {
  id: string;
  title: string;
  description: string;
  status: 'todo' | 'in_progress' | 'completed';
  priority: 'low' | 'medium' | 'high';
  created_at: string;
  updated_at: string;
  due_date: string;
  project: Project;
  assignee: User;
}
```

## 고급 기능

### 배치 작업

```javascript
// 여러 작업을 한 번에 생성
const tasks = await taskflow.tasks.createBatch([
  { title: '작업 1', project_id: 'proj_123' },
  { title: '작업 2', project_id: 'proj_123' },
  { title: '작업 3', project_id: 'proj_123' }
]);

// 여러 작업을 한 번에 업데이트
const updatedTasks = await taskflow.tasks.updateBatch([
  { id: 'task_101', status: 'completed' },
  { id: 'task_102', status: 'in_progress' }
]);
```

### 페이지네이션 도우미

```javascript
// 모든 페이지를 자동으로 순회
const allProjects = await taskflow.projects.listAll({
  status: 'active'
});

// 또는 이터레이터 사용
for await (const project of taskflow.projects.iterate({ status: 'active' })) {
  console.log('프로젝트:', project.title);
}
```

### 캐싱

```javascript
// 캐시 활성화
const taskflow = new TaskFlow({
  apiKey: 'your-api-key',
  cache: {
    enabled: true,
    ttl: 300000 // 5분
  }
});

// 캐시된 결과 반환
const project = await taskflow.projects.get('proj_123');
```

## 웹훅 처리

### Express.js 예제

```javascript
const express = require('express');
const { TaskFlow } = require('@taskflow/sdk');

const app = express();
app.use(express.json());

const taskflow = new TaskFlow({
  apiKey: process.env.TASKFLOW_API_KEY
});

app.post('/webhook', async (req, res) => {
  const { event, data } = req.body;
  
  switch (event) {
    case 'task.completed':
      console.log('작업 완료:', data.task.title);
      // 완료된 작업에 대한 추가 처리
      break;
      
    case 'project.created':
      console.log('새 프로젝트:', data.project.title);
      // 새 프로젝트에 대한 초기 설정
      break;
  }
  
  res.status(200).json({ received: true });
});
```

## 테스트

SDK는 테스트를 위한 목(Mock) 기능을 제공합니다:

```javascript
import { TaskFlow } from '@taskflow/sdk';
import { TaskFlowMock } from '@taskflow/sdk/mock';

// 테스트 환경에서 Mock 사용
const taskflow = new TaskFlowMock();

// 가짜 데이터 설정
taskflow.mock.projects.get.mockResolvedValue({
  id: 'proj_123',
  title: '테스트 프로젝트'
});

// 테스트 실행
const project = await taskflow.projects.get('proj_123');
expect(project.title).toBe('테스트 프로젝트');
```

## 마이그레이션 가이드

### v1.x에서 v2.x로

```javascript
// v1.x
const taskflow = new TaskFlow('your-api-key');

// v2.x
const taskflow = new TaskFlow({
  apiKey: 'your-api-key'
});
```

### 주요 변경사항

- 생성자 매개변수가 문자열에서 객체로 변경
- 모든 메서드가 Promise 기반으로 변경
- 오류 처리 방식 개선
- TypeScript 지원 추가

## 문제 해결

### 일반적인 문제

**Q: 인증 오류가 발생합니다**
A: API 키가 올바른지 확인하고, 키에 적절한 권한이 있는지 확인하세요.

**Q: 속도 제한에 걸립니다**
A: 요청 간격을 늘리거나 배치 작업을 사용하세요.

**Q: 네트워크 오류가 발생합니다**
A: 재시도 설정을 확인하고 네트워크 연결을 점검하세요.

### 디버깅

```javascript
const taskflow = new TaskFlow({
  apiKey: 'your-api-key',
  debug: true // 디버그 모드 활성화
});
```

## 지원 및 커뮤니티

- **GitHub**: https://github.com/taskflow/js-sdk
- **문서**: https://docs.taskflow.com/sdk/javascript
- **이슈 트래커**: https://github.com/taskflow/js-sdk/issues
- **디스코드**: https://discord.gg/taskflow
- **이메일**: sdk-support@taskflow.com

## 라이선스

MIT License - 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.