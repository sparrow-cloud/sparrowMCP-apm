---
name: sparrow-mcp-fix-vulnerabilities
description: 발견된 보안 취약점에 대한 수정 코드를 자동 생성하고 적용합니다
---

# Sparrow Fix Vulnerabilities

보안 분석 결과를 바탕으로 취약점을 자동으로 수정합니다.

## 사용 시나리오

사용자가 다음과 같이 요청할 때 이 skill을 적용하세요:
- "취약점 발견했으면 수정해줘"
- "보안 패치 자동 적용해줘"
- "이 취약점 고쳐줘"
- "수정 코드 만들어줘"

## 전제 조건

이 skill은 **이전에 보안 분석이 완료된 상태**에서 사용합니다.
- `analysisId`가 필요합니다
- 분석 상태가 'COMPLETE'이어야 합니다

## 실행 단계

### 1단계: 분석 결과 확인
`get_sast_analysis_results`로 취약점 목록을 확인합니다.

```
도구: get_sast_analysis_results
입력:
  - analysisId: 이전 분석 ID
출력: 취약점 목록 (파일, 라인, 유형)
```

### 2단계: 수정 코드 생성
각 취약 파일에 대해 `generate_secure_code`를 호출합니다.

```
도구: generate_secure_code
입력:
  - analysisId: 분석 ID
  - fileContent: 원본 파일 내용 (view_file로 읽기)
  - fileName: 파일명 (예: "UserService.java")
출력:
  - secureCode: 수정된 전체 코드
  - secureCodeBrief: 수정 내용 설명
  - diff: 변경사항 Diff
```

### 3단계: 변경사항 적용
사용자 승인 후 코드를 적용합니다.

```
1. diff를 사용자에게 보여주고 확인 요청
2. 승인 시 replace_file_content 또는 write_to_file로 적용
3. 변경 내용 요약 제공
```

## 결과 보고 형식

```markdown
## 🔧 취약점 수정 결과

### 수정된 파일: [파일명]

**변경 내용:**
[secureCodeBrief 내용]

**Diff:**
```diff
[diff 내용]
```

✅ 수정이 완료되었습니다. 다음 취약점이 해결되었습니다:
- [취약점 1]: SQL Injection → Prepared Statement 적용
- [취약점 2]: XSS → 입력값 검증 추가
```

## 워크플로우 옵션

### 옵션 A: 대화형 (기본)
1. 각 파일별로 diff 보여주기
2. 사용자 확인 후 적용
3. 다음 파일로 진행

### 옵션 B: 일괄 적용
사용자가 "전부 적용해줘"라고 하면:
1. 모든 수정 코드를 한번에 생성
2. 전체 변경 요약 보여주기
3. 일괄 적용 후 결과 보고

## 🌐 언어 정책 (Language Policy)

- 결과 언어는 사용자가 입력한 프롬프트의 언어를 따릅니다.
- 모든 출력 및 보고서는 사용자의 언어로 표시하여 가독성을 높입니다.

## 주의사항

- 수정 코드 적용 전 **반드시 사용자 확인**을 받으세요
- 원본 코드 백업 권장을 안내하세요
- 자동 생성된 코드는 테스트가 필요함을 알려주세요
