---
name: sparrow-mcp-quick-scan
description: 단일 파일 또는 특정 코드 조각의 빠른 보안 점검을 수행합니다
---

# Sparrow Quick Scan

단일 파일의 보안 취약점을 빠르게 진단하고 수정 코드를 제공합니다.

## 사용 시나리오

사용자가 다음과 같이 요청할 때 이 skill을 적용하세요:
- "이 파일 보안 검사해줘"
- "UserController.java 취약점 있어?"
- "이 코드 보안 분석해줘"
- 특정 파일을 지정하여 보안 점검 요청 시

## 실행 단계

### 1단계: 분석 시작
`analyze_file_sast` 도구를 호출하여 분석을 시작합니다.

```
도구: analyze_file_sast
입력:
  - fileContent: 분석할 파일 내용
  - fileName: 파일명 (예: "UserController.java")
출력: analysisId
```

### 2단계: 진행률 추적
`track_analysis_progress` 도구로 분석 완료를 기다립니다.

```
도구: track_analysis_progress
입력:
  - analysisId: 1단계에서 받은 ID
  - intervalMs: (선택) 진행 상태 확인 주기(ms)
출력: 분석 완료 상태
```

### 3단계: 결과 조회
`get_sast_analysis_results` 도구로 취약점 목록을 확인합니다.

```
도구: get_sast_analysis_results
입력:
  - analysisId: 분석 ID
출력: 취약점 목록, 심각도, 위치
```

### 4단계: 수정 코드 생성 (취약점 발견 시)
`generate_secure_code` 도구로 안전한 코드를 생성합니다.

```
도구: generate_secure_code
입력:
  - analysisId: 분석 ID
  - fileContent: 원본 파일 내용
  - fileName: 파일명 (예: "UserController.java")
출력: 수정된 코드, 변경 설명, Diff
```

## 결과 보고 형식

분석 완료 후 다음 형식으로 보고하세요:

```markdown
## 🔒 보안 분석 결과

**분석 파일**: [파일명]
**발견된 취약점**: [N]개

### 심각도별 요약
- 🔴 Critical: N개
- 🟠 High: N개
- 🟡 Medium: N개
- 🟢 Low: N개

### 수정 조치
[generate_secure_code 결과의 Diff 표시]
```

## 🌐 언어 정책 (Language Policy)

- 결과 언어는 사용자가 입력한 프롬프트의 언어를 따릅니다.
- 모든 출력 및 보고서는 사용자의 언어로 표시하여 가독성을 높입니다.

## 주의사항

- 취약점이 발견되면 반드시 `generate_secure_code`로 수정 코드를 제시하세요
- "취약점이 발견되었습니다" 대신 **"보안 이슈를 해결했습니다"**라고 긍정적으로 표현하세요
