# 03. API 명세서

## API 개요

### 기본 정보
- **Base URL**: `http://localhost:8080` (기본) / `http://localhost:8580` (dev)
- **Content-Type**: `application/json`
- **Authorization**: Bearer Token (JWT)
- **API 문서**: `/swagger-ui.html`

### 공통 응답 형식
모든 API는 다음과 같은 공통 응답 형식을 사용합니다:

```json
{
  "success": true,
  "message": "성공 메시지",
  "data": {
    // 실제 응답 데이터
  }
}
```

### 에러 응답 형식
```json
{
  "success": false,
  "message": "에러 메시지",
  "data": null,
  "errorCode": "ERROR_CODE"
}
```

### 페이지네이션 응답 형식
```json
{
  "success": true,
  "message": "조회 성공",
  "data": {
    "content": [...],
    "page": 1,
    "size": 10,
    "totalElements": 100
  }
}
```

## 인증 API

### 1. 회원가입
**POST** `/auth/signup`

#### Request Body
```json
{
  "email": "user@example.com",
  "password": "password123",
  "nickname": "사용자명"
}
```

#### Validation
- `email`: 필수, 이메일 형식
- `password`: 필수, 8-64자
- `nickname`: 필수, 1-30자

#### Response
```json
{
  "success": true,
  "message": "회원가입 성공",
  "data": {
    "userId": 1,
    "email": "user@example.com",
    "nickname": "사용자명",
    "status": "PENDING"
  }
}
```

### 2. 로그인
**POST** `/auth/login`

#### Request Body
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

#### Validation
- `email`: 필수, 이메일 형식
- `password`: 필수

#### Response
```json
{
  "success": true,
  "message": "로그인 성공",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "tokenType": "Bearer",
    "expiresIn": 1800,
    "user": {
      "id": 1,
      "email": "user@example.com",
      "name": "사용자명"
    }
  }
}
```

### 3. 로그아웃
**POST** `/auth/logout`
**Headers**: `Authorization: Bearer {accessToken}`

#### Request Body
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### Response
```json
{
  "success": true,
  "message": "로그아웃 되었습니다.",
  "data": null
}
```

## 템플릿 API

### 1. 템플릿 목록 조회
**GET** `/api/templates`
**Headers**: `Authorization: Bearer {accessToken}`

#### Query Parameters
| 파라미터 | 타입 | 필수 | 설명 | 예시 |
|----------|------|------|------|------|
| status | String | Y | 템플릿 상태 | APPROVED |
| page | Integer | N | 페이지 번호 (기본값: 1) | 1 |
| size | Integer | N | 페이지 크기 (기본값: 10) | 10 |

#### 허용되는 Status 값
- `APPROVE_REQUESTED`: 승인 요청됨
- `APPROVED`: 승인됨
- `REJECTED`: 거부됨

#### Response
```json
{
  "success": true,
  "message": "조회 성공",
  "data": {
    "content": [
      {
        "id": 1,
        "title": "마케팅 이메일 템플릿",
        "content": "<html>...</html>",
        "status": "APPROVED",
        "type": "EMAIL",
        "isPublic": true,
        "imageUrl": "https://example.com/image.jpg",
        "categoryId": 1,
        "createdAt": "2024-01-01T10:00:00",
        "updatedAt": "2024-01-01T10:00:00",
        "variables": [
          {
            "id": 1,
            "variableKey": "customer_name",
            "placeholder": "고객명을 입력하세요",
            "inputType": "text"
          }
        ],
        "buttons": [
          {
            "id": 1,
            "name": "자세히 보기",
            "ordering": 1,
            "linkPc": "https://example.com/pc",
            "linkAnd": "https://example.com/android",
            "linkIos": "https://example.com/ios"
          }
        ],
        "industries": [
          {
            "id": 1,
            "name": "IT/소프트웨어"
          }
        ],
        "purposes": [
          {
            "id": 1,
            "name": "마케팅"
          }
        ]
      }
    ],
    "page": 1,
    "size": 10,
    "totalElements": 25
  }
}
```

### 2. 템플릿 상세 조회
**GET** `/api/templates/{id}`
**Headers**: `Authorization: Bearer {accessToken}`

#### Path Parameters
| 파라미터 | 타입 | 설명 |
|----------|------|------|
| id | Long | 템플릿 ID |

#### Response
```json
{
  "success": true,
  "message": "조회 성공",
  "data": {
    "id": 1,
    "title": "마케팅 이메일 템플릿",
    "content": "<html>...</html>",
    "status": "APPROVED",
    "type": "EMAIL",
    "isPublic": true,
    "imageUrl": "https://example.com/image.jpg",
    "categoryId": 1,
    "rejectReason": null,
    "rejectReasonSummary": null,
    "createdAt": "2024-01-01T10:00:00",
    "updatedAt": "2024-01-01T10:00:00",
    "variables": [...],
    "buttons": [...],
    "industries": [...],
    "purposes": [...]
  }
}
```

### 3. 템플릿 생성 (AI 기반)
**POST** `/api/templates`
**Headers**: `Authorization: Bearer {accessToken}`

#### Request Body
```json
{
  "requestContent": "이커머스 사이트를 위한 주문 완료 이메일 템플릿을 만들어주세요. 고객 이름, 주문 번호, 상품 목록을 포함해야 합니다."
}
```

#### Response (성공)
```json
{
  "success": true,
  "message": "템플릿 생성 성공",
  "data": {
    "id": 2,
    "title": "이커머스 주문 완료 이메일",
    "content": "<html>...</html>",
    "status": "CREATED",
    "type": "EMAIL",
    "isPublic": false,
    "imageUrl": "https://ai-generated-image.com/template.jpg",
    "categoryId": 2,
    "createdAt": "2024-01-01T11:00:00",
    "updatedAt": "2024-01-01T11:00:00",
    "variables": [
      {
        "id": 2,
        "variableKey": "customer_name",
        "placeholder": "고객명",
        "inputType": "text"
      },
      {
        "id": 3,
        "variableKey": "order_number",
        "placeholder": "주문번호",
        "inputType": "text"
      }
    ],
    "buttons": [
      {
        "id": 2,
        "name": "주문 상세보기",
        "ordering": 1,
        "linkPc": "{{order_detail_url}}",
        "linkAnd": "{{order_detail_url}}",
        "linkIos": "{{order_detail_url}}"
      }
    ]
  }
}
```

#### AI 서버 연동 프로세스
1. 사용자 요청을 `UserTemplateRequest`에 저장 (상태: PENDING)
2. 외부 AI 서버 호출 (`http://3.38.180.216:8000/ai/templates`)
3. AI 응답을 바탕으로 `Template` 생성
4. 연관 데이터 생성 (Variables, Buttons, Industries, Purposes)
5. `TemplateHistory`에 상태 변경 기록
6. `UserTemplateRequest` 상태를 COMPLETED로 변경

#### 에러 처리
AI 서버 호출 실패 시:
```json
{
  "success": false,
  "message": "템플릿 생성 중 오류가 발생했습니다.",
  "data": null,
  "errorCode": "AI_SERVER_ERROR"
}
```

## 헬스체크 API

### 데이터베이스 헬스체크
**GET** `/health/db`

#### Response (정상)
```json
{
  "ok": true
}
```

#### Response (오류)
```json
{
  "ok": false,
  "error": "Connection failed"
}
```

## 에러 코드

### 템플릿 관련 에러
| 에러 코드 | HTTP 상태 | 메시지 | 설명 |
|-----------|-----------|--------|------|
| TEMPLATE_NOT_FOUND | 404 | 템플릿을 찾을 수 없습니다. | 존재하지 않는 템플릿 조회 |
| INVALID_STATUS | 400 | 유효하지 않은 상태값입니다. | 잘못된 상태 파라미터 |
| FORBIDDEN_STATUS | 403 | 접근할 수 없는 상태입니다. | 허용되지 않은 상태 조회 |
| ACCESS_DENIED | 403 | 접근 권한이 없습니다. | 타인의 템플릿 접근 시도 |

### 인증 관련 에러
| 에러 코드 | HTTP 상태 | 메시지 | 설명 |
|-----------|-----------|--------|------|
| UNAUTHORIZED | 401 | 인증이 필요합니다. | JWT 토큰 없음/만료 |
| FORBIDDEN | 403 | 접근 권한이 없습니다. | 권한 부족 |
| INVALID_CREDENTIALS | 401 | 잘못된 로그인 정보입니다. | 이메일/비밀번호 오류 |
| USER_LOCKED | 423 | 계정이 잠겨있습니다. | 로그인 실패 초과 |

## 인증 방식

### JWT 토큰
- **Access Token**: 30분 유효, API 호출 시 사용
- **Refresh Token**: 14일 유효, Access Token 갱신용

### 토큰 사용법
```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### 토큰 갱신 (구현 예정)
현재 구현되지 않은 기능이지만, 향후 추가될 예정입니다:
```http
POST /auth/refresh
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

## API 사용 예시

### cURL 예시

#### 1. 로그인
```bash
curl -X POST http://localhost:8080/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "password123"
  }'
```

#### 2. 템플릿 목록 조회
```bash
curl -X GET "http://localhost:8080/api/templates?status=APPROVED&page=1&size=5" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

#### 3. 템플릿 생성
```bash
curl -X POST http://localhost:8080/api/templates \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -d '{
    "requestContent": "고객 환영 이메일 템플릿을 만들어주세요"
  }'
```

## 주의사항

1. **Rate Limiting**: 현재 구현되지 않음 (향후 추가 예정)
2. **API 버전**: 현재 v1, URL에 버전 정보 없음
3. **CORS**: 개발 환경에서는 비활성화됨
4. **HTTPS**: 로컬 개발에서는 HTTP 사용
5. **파일 업로드**: 현재 지원하지 않음 (이미지는 AI에서 자동 생성)

이 API 명세서는 시스템의 모든 공개 엔드포인트를 다루며, 클라이언트 개발자가 효율적으로 통합할 수 있도록 상세한 정보를 제공합니다.