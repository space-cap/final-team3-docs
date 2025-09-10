# 02. API 사용법

## 🚀 FastAPI 서버 시작

### 로컬 개발 환경
```bash
# 개발 모드 (자동 리로드)
uvicorn server:app --reload --host 0.0.0.0 --port 8000

# 또는 Python으로 직접 실행
python server.py
```

### 프로덕션 환경
```bash
# 프로덕션 모드
uvicorn server:app --host 0.0.0.0 --port 8000 --workers 4
```

서버 실행 후 다음 URL에서 확인 가능합니다:
- **API 문서**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc
- **헬스 체크**: http://localhost:8000/health

## 📡 API 엔드포인트

### 1. 템플릿 생성 API

**`POST /ai/templates`**

기업 시스템에서 AI 템플릿 생성을 요청하는 메인 엔드포인트입니다.

#### 요청 형식
```json
{
  "userId": 101,
  "requestContent": "쿠폰 발급 안내"
}
```

#### 요청 파라미터
| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `userId` | integer | ✅ | 사용자 ID |
| `requestContent` | string | ✅ | 템플릿 생성 요청 내용 |

#### 응답 형식
```json
{
  "id": 1,
  "userId": 101,
  "categoryId": 9101,
  "title": "쿠폰 발급 안내",
  "content": "안녕하세요, #{고객명}님.\n#{쿠폰명} 쿠폰 발급을 안내드립니다.\n\n🎁 혜택: #{할인율}% 할인\n📅 사용기한: #{만료일}까지\n\n#{브랜드명}에서 특별히 준비한 혜택을 놓치지 마세요!\n\n감사합니다.",
  "imageUrl": null,
  "type": "MESSAGE",
  "buttons": [],
  "variables": [
    {
      "id": 1,
      "variableKey": "고객명",
      "placeholder": "고객 이름",
      "inputType": "text"
    },
    {
      "id": 2,
      "variableKey": "쿠폰명", 
      "placeholder": "쿠폰 이름",
      "inputType": "text"
    },
    {
      "id": 3,
      "variableKey": "할인율",
      "placeholder": "할인 퍼센트",
      "inputType": "number"
    },
    {
      "id": 4,
      "variableKey": "만료일",
      "placeholder": "쿠폰 만료 날짜",
      "inputType": "date"
    },
    {
      "id": 5,
      "variableKey": "브랜드명",
      "placeholder": "브랜드 이름", 
      "inputType": "text"
    }
  ],
  "industries": [],
  "purposes": []
}
```

#### 응답 필드
| 필드 | 타입 | 설명 |
|------|------|------|
| `id` | integer | 템플릿 ID |
| `userId` | integer | 사용자 ID |
| `categoryId` | integer | 업종 카테고리 ID (9101~9105) |
| `title` | string | 템플릿 제목 |
| `content` | string | 생성된 템플릿 내용 |
| `imageUrl` | string/null | 이미지 URL (현재 null) |
| `type` | string | 메시지 타입 ("MESSAGE" 고정) |
| `buttons` | array | 버튼 정보 (현재 빈 배열) |
| `variables` | array | 추출된 변수 목록 |
| `industries` | array | 산업 분류 (현재 빈 배열) |
| `purposes` | array | 목적 분류 (현재 빈 배열) |

### 2. 헬스 체크 API

**`GET /health`**

서버 상태를 확인하는 엔드포인트입니다.

#### 응답 예시
```json
{
  "status": "healthy",
  "timestamp": "2025-09-04T10:30:00Z",
  "version": "2.1.0",
  "dependencies": {
    "gemini_api": "connected",
    "faiss_index": "loaded"
  }
}
```

## 🔧 cURL 사용 예제

### 템플릿 생성 요청
```bash
curl -X POST http://localhost:8000/ai/templates \
  -H "Content-Type: application/json" \
  -d '{
    "userId": 101,
    "requestContent": "쿠폰 발급 안내"
  }'
```

### 다양한 템플릿 유형 예제
```bash
# 부동산 상담 예약
curl -X POST http://localhost:8000/ai/templates \
  -H "Content-Type: application/json" \
  -d '{
    "userId": 102,
    "requestContent": "부동산 상담 예약 안내"
  }'

# 교육 강의 일정
curl -X POST http://localhost:8000/ai/templates \
  -H "Content-Type: application/json" \
  -d '{
    "userId": 103,
    "requestContent": "다음 주 화요일 오전 10시 영어 수업"
  }'

# 서비스업 PT 결과
curl -X POST http://localhost:8000/ai/templates \
  -H "Content-Type: application/json" \
  -d '{
    "userId": 104,
    "requestContent": "PT 운동 결과 및 다음 일정 안내"
  }'
```

## 🏷️ 카테고리 ID 매핑

| 카테고리 ID | 업종 | 설명 |
|-------------|------|------|
| 9101 | 소매업 | 쿠폰, 할인, 상품 관련 |
| 9102 | 부동산 | 예약, 상담, 매물 관련 |
| 9103 | 교육 | 강의, 학습, 교육 관련 |
| 9104 | 서비스업 | 뷰티, 건강, 관리 관련 |
| 9105 | 기타 | 회원, 이벤트, 적립금 관련 |

## 📝 변수 타입 설명

| Input Type | 설명 | 예시 |
|------------|------|------|
| `text` | 일반 텍스트 | 고객명, 브랜드명 |
| `number` | 숫자 | 할인율, 금액 |
| `date` | 날짜 | 만료일, 예약일 |
| `email` | 이메일 | 연락처 |
| `phone` | 전화번호 | 문의 번호 |

## 🚨 에러 처리

### HTTP 상태 코드
| 코드 | 설명 | 대응 방법 |
|------|------|-----------|
| 200 | 성공 | 정상 처리 |
| 409 | 중복 템플릿 | 기존 템플릿 사용 권장 |
| 422 | 유효하지 않은 요청 | 요청 형식 확인 |
| 500 | 서버 내부 오류 | 로그 확인 및 재시도 |

### 에러 응답 예시
```json
{
  "detail": {
    "error": "Template generation failed",
    "code": "GENERATION_ERROR",
    "message": "AI 템플릿 생성 중 오류가 발생했습니다."
  }
}
```

## 🔐 환경 변수 설정

API 사용 전 다음 환경 변수가 설정되어 있어야 합니다:

```bash
# .env 파일
GOOGLE_API_KEY=your_gemini_api_key_here
GEMINI_API_KEY=your_gemini_api_key_here  # 대체 키
ENVIRONMENT=development
```

## 🧪 테스트 도구

### 백엔드 통합 테스트
```bash
python test_backend_integration.py
```

### Postman 컬렉션
Postman에서 다음 컬렉션을 import하여 사용할 수 있습니다:
```json
{
  "name": "JOBER AI API",
  "requests": [
    {
      "name": "Generate Template",
      "method": "POST",
      "url": "{{base_url}}/ai/templates",
      "body": {
        "userId": 101,
        "requestContent": "쿠폰 발급 안내"
      }
    }
  ],
  "variables": {
    "base_url": "http://localhost:8000"
  }
}
```

---

**이전 문서**: [01_프로젝트_개요.md](./01_프로젝트_개요.md)  
**다음 문서**: [03_시스템_아키텍처.md](./03_시스템_아키텍처.md)