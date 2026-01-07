# Sirloin OMS Frontend - Project Context

이 문서는 Claude Code가 프로젝트를 이해하기 위한 컨텍스트를 제공합니다.

## 프로젝트 개요

**프로젝트명**: Sirloin OMS Frontend
**설명**: 주문 관리 시스템(Order Management System)의 프론트엔드 애플리케이션

**기술 스택**:

- **React 18**: UI 라이브러리
- **TypeScript**: 정적 타입 시스템
- **Apollo Client**: GraphQL 클라이언트 및 캐시 관리
- **Recoil**: 전역 상태 관리
- **React Hook Form + Formik**: 폼 관리
- **Yup**: 스키마 검증 및 Validation
- **MUI (Material-UI)**: UI 컴포넌트 라이브러리
- **Emotion**: CSS-in-JS 스타일링
- **React Router v6**: 클라이언트 사이드 라우팅
- **Vite**: 빌드 도구 및 개발 서버
- **Axios**: HTTP 클라이언트
- **date-fns**: 날짜/시간 유틸리티

## 코딩 원칙 및 가이드라인

### Frontend Design Guidelines

프로젝트는 4가지 핵심 원칙을 따릅니다:

1. **Readability (가독성)**: 명확하고 이해하기 쉬운 코드
2. **Predictability (예측 가능성)**: 이름만으로 동작을 예측 가능
3. **Cohesion (응집도)**: 관련 코드를 함께 배치
4. **Coupling (결합도)**: 컴포넌트 간 의존성 최소화

**전체 가이드라인**: `.claude/skills/frontend-design-guide/GUIDELINES.md` 참조

### 주요 코딩 규칙

#### ✅ 권장 사항

- Magic number는 명명된 상수로 추출
- 복잡한 로직은 별도 컴포넌트/HOC로 추상화
- 조건부 렌더링이 복잡하면 컴포넌트 분리
- Props drilling 대신 Component Composition 활용
- Apollo Client hooks는 일관된 패턴 사용
- Validation은 Yup 스키마로 정의
- Form은 요구사항에 맞게 React Hook Form으로 통일
- **함수 네이밍**: `get~` 사용 (데이터 조회 및 파생 값 반환)
  - 예: `getTotalPrice()`, `getNetAvailableQty()`

#### ❌ 피해야 할 사항

- Magic number 직접 사용
- 복잡한 중첩 삼항 연산자
- 함수에 숨겨진 side effect
- 불필요한 props drilling
- 성급한 추상화
- 너무 넓은 범위의 상태 관리
- GraphQL 쿼리/뮤테이션을 컴포넌트에 직접 작성 (별도 파일로 분리)
- **중복 접두사**: `calculate~` 사용 지양 (함수는 본질적으로 계산을 수행하므로)

## 프로젝트 구조

```
src/
├── components/     # 공유 컴포넌트
├── hooks/          # 공유 hooks
├── utils/          # 유틸리티 함수
├── types/          # 타입 정의
├── services/       # API 서비스
├── constants/      # 상수 정의
├── graphql/        # GraphQL 쿼리/뮤테이션 정의
│   └── __generated__/  # GraphQL Codegen 생성 파일
├── recoil/         # Recoil atoms & selectors
└── domains/        # 도메인별 구조 (필요시)
```

## 비즈니스 도메인

### 주문 관리 (Order)

- 주문 생성, 조회, 수정, 취소
- 주문 상태 관리
- 매핑 상품 (Mapped Goods) 관리

### 재고 관리 (Inventory)

- 재고 수준 추적
- 재주문 포인트 관리
- 품절/재입고 처리

### 상품 관리 (Product)

- 상품 카탈로그
- 가격 및 할인 관리
- 카테고리 관리

## 개발 워크플로우

### 개발 서버 실행

```bash
yarn dev              # development 환경
yarn start:staging    # staging 환경
```

### 빌드

```bash
yarn build           # 프로덕션 빌드
yarn start           # 빌드된 결과 미리보기
```

### GraphQL Codegen

```bash
yarn codegen         # GraphQL 타입 생성 + Prettier
```

### 코드 작성 시

1. Frontend Design Guidelines 확인
2. 기존 도메인의 패턴 참조
3. GraphQL 쿼리는 별도 `.graphql` 파일로 작성
4. 컴포넌트 작성 (MUI 컴포넌트 활용)
5. Yup 스키마로 Validation 정의

### 코드 리뷰 시

1. `code-reviewer` 에이전트 사용
2. Readability, Predictability, Cohesion, Coupling 체크
3. 보안, 성능, 테스트 확인

### 버그 수정 시

1. `bug-fixer` 에이전트 사용
2. 근본 원인 분석
3. 최소한의 수정으로 해결
<!-- 4. 회귀 방지 테스트 추가 -->

### 비즈니스 로직 검증 시

1. `business-logic-validator` 에이전트 사용
2. 비즈니스 규칙 준수 확인
3. 데이터 흐름 검증
4. 엣지 케이스 처리 확인

## Custom Agents

프로젝트는 3개의 커스텀 에이전트를 사용합니다:

1. **code-reviewer** (`.claude/agents/code-reviewer.md`)

   - 코드 품질, 보안, 성능 리뷰
   - Frontend Design Guidelines 자동 적용

2. **bug-fixer** (`.claude/agents/bug-fixer.md`)

   - 버그 근본 원인 분석
   - 프로젝트 특화 디버깅
   - TypeScript, GraphQL, React 에러 처리

3. **business-logic-validator** (`.claude/agents/business-logic-validator.md`)
   - 비즈니스 요구사항 검증
   - 데이터 흐름 및 규칙 확인

## Custom Skills

프로젝트는 다음 Skill을 사용합니다:

1. **frontend-design-guide** (`.claude/skills/frontend-design-guide/`)
   - 모든 코드 작성 시 자동 적용
   - 4가지 핵심 원칙 준수 강제

## Git 워크플로우

- **Main Branch**: `develop`
- **Feature Branches**: `feature/<feature-name>`
- **Fix Branches**: `fix/<fix-name>`
- **Release Branches**: `release/<version>`

### 커밋 메시지 규칙

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Type**: feat, fix, docs, style, refactor, perf, test, chore

## 기술 스택 특화 가이드

### Apollo Client

- 쿼리는 `.graphql` 파일로 분리하고 codegen으로 타입 생성
- Cache 정책 명시적 설정
- Error handling은 Apollo Error Policies 활용

### Recoil

- Atom은 최소 단위로 분리
- Selector는 파생 상태 계산에만 사용
- Atom key는 명확하고 고유하게 작성

### React Hook Form vs Formik

- **React Hook Form**: 간단한 폼, 성능이 중요한 경우
- **Formik**: 복잡한 폼, 중첩된 객체, 배열 필드가 많은 경우

### MUI (Material-UI)

- 테마 커스터마이징은 중앙에서 관리
- sx prop을 활용한 스타일링 권장
- 컴포넌트 재사용 시 MUI 컴포넌트 확장

### Yup Validation

- 재사용 가능한 스키마는 별도 파일로 분리
- 에러 메시지는 한글로 명확하게 작성
- Custom validation은 `.test()` 메서드 활용

## 알려진 이슈 및 제약사항

### 매핑 상품 (Mapped Goods)

- 매핑 관계 무결성 유지 필요
- 수량 일관성 체크 필수
- 상태 동기화 주의

### GraphQL

- 쿼리/뮤테이션 변경 시 반드시 `yarn codegen` 실행
- Fragment 사용으로 중복 필드 정의 최소화
- N+1 쿼리 문제 주의 (DataLoader 활용)

### 성능 고려사항

- 긴 리스트는 MUI DataGrid 가상화 활용
- 이미지 로딩 최적화
- 불필요한 리렌더링 방지 (useMemo, useCallback)
- Apollo Client 캐시 정책 최적화

### 테스트

- 단위 테스트: 비즈니스 로직, 유틸리티 함수
- 통합 테스트: GraphQL 쿼리/뮤테이션
- E2E 테스트: 주요 워크플로우

## 참고 자료

- **Frontend Design Guidelines**: `.claude/skills/frontend-design-guide/GUIDELINES.md`
- **Code Review Checklist**: `.claude/agents/code-reviewer.md`
- **Debugging Guide**: `.claude/agents/bug-fixer.md`
- **Business Logic Validation**: `.claude/agents/business-logic-validator.md`
- **GraphQL Schema**: GraphQL 서버 스키마 참조
- **MUI Documentation**: https://mui.com/

## 팀 정보

- **코드 리뷰**: Pull Request 필수
- **브랜치 보호**: `develop`, `main`은 직접 push 금지
- **배포**: CI/CD 파이프라인 사용
- **GraphQL 스키마 변경**: 백엔드 팀과 협의 후 진행

---

**마지막 업데이트**: 2026-01-05
**문서 관리자**: 프로젝트 팀
