---
name: sparrow-mcp-full-audit
description: 프로젝트 전체에 대한 종합 보안 감사를 수행하고 상세 보고서를 생성합니다
---

# Sparrow Full Audit

프로젝트 전체 폴더를 대상으로 종합적인 보안 감사를 수행합니다.

## 사용 시나리오

사용자가 다음과 같이 요청할 때 이 skill을 적용하세요:
- "이 프로젝트 전체 보안 감사해줘"
- "릴리즈 전 보안 점검 해줘"
- "전체 코드 보안 분석해줘"
- "보안 감사 보고서 만들어줘"

## 실행 단계

### 1단계: 폴더 분석 시작
`analyze_folder_sast` 도구를 호출합니다.

```
도구: analyze_folder_sast
입력:
  - folderPath: 분석할 프로젝트 경로
  - zipFileName: (선택) 분석용 ZIP 파일명
출력: analysisId, skippedFolders (제외된 폴더 목록)
```

> **참고**: node_modules, .git, dist 등은 자동으로 제외됩니다.

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
`get_sast_analysis_results` 도구로 상세 결과를 확인합니다.

```
도구: get_sast_analysis_results
입력:
  - analysisId: 분석 ID
출력: 취약점 목록, 파일별 위치, 컴플라이언스 정보
```

### 4단계: 보고서 작성
결과를 바탕으로 종합 보고서를 작성합니다.

## 결과 보고 형식

```markdown
# 🔐 보안 감사 보고서

**프로젝트**: [폴더명]
**분석일시**: [날짜]
**분석 ID**: [analysisId]

## 📊 Executive Summary

| 항목 | 수치 |
|------|------|
| 분석 파일 수 | N개 |
| 발견된 취약점 | N개 |
| Critical/High | N개 |
| 제외된 폴더 | [목록] |

## 🚨 주요 발견사항

### Critical & High 취약점
1. [파일:라인] **취약점명** - 설명
   - 위험도: Critical
   - 권장 조치: [조치 내용]

### Medium & Low 취약점
[목록으로 간략히 표시]

## 📋 컴플라이언스 현황
[OWASP, CWE 등 매핑 정보]

## ✅ 권장 조치사항
1. [우선순위 높은 조치]
2. [중간 우선순위 조치]
3. [참고 사항]
```

## 🌐 언어 정책 (Language Policy)

- 결과 언어는 사용자가 입력한 프롬프트의 언어를 따릅니다.
- 모든 출력 및 보고서는 사용자의 언어로 표시하여 가독성을 높입니다.

## 추가 작업

Critical/High 취약점이 발견된 경우:
- 각 취약 파일에 대해 `generate_secure_code` (입력: analysisId, fileContent, fileName)를 호출하여 수정 코드 제공
- 수정 우선순위를 제시하여 단계적 개선 가이드 제공
