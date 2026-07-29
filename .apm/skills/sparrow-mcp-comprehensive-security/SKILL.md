---
name: sparrow-mcp-comprehensive-security
description: SAST와 SCA를 모두 실행하여 소스코드와 오픈소스 라이브러리를 종합적으로 점검합니다
---

# Sparrow Comprehensive Security

프로젝트의 **소스코드 취약점(SAST)**과 **오픈소스 라이브러리 취약점(SCA)**을 함께 검사하여 종합적인 보안 점검 결과를 제공합니다.

## 사용 시나리오

사용자가 다음과 같이 요청할 때 이 skill을 적용하세요:
- "이 프로젝트의 전반적인 보안을 다 검토해줘"
- "코드랑 라이브러리 둘 다 보안 점검해줘"
- "종합 보안 점검해줘"
- "완전한 보안 분석 해줘"

## 실행 단계

### 1단계: SAST 분석 시작
```
도구: analyze_folder_sast
입력:
  - folderPath: 프로젝트 경로
출력: sast_analysisId
```

### 2단계: SCA 분석 시작 (병렬 실행)
```
도구: analyze_folder_sca
입력:
  - folderPath: 프로젝트 경로
출력: sca_analysisId
```

### 3단계: 두 분석 진행률 추적
SAST와 SCA 분석이 모두 완료될 때까지 추적합니다.

```
도구: track_analysis_progress
입력:
  - analysisId: sast_analysisId
  - intervalMs: (선택) 진행 상태 확인 주기(ms)
---
도구: track_analysis_progress
입력:
  - analysisId: sca_analysisId
  - intervalMs: (선택) 진행 상태 확인 주기(ms)
```

### 4단계: SAST 결과 조회
```
도구: get_sast_analysis_results
입력:
  - analysisId: sast_analysisId
출력: 코드 취약점 목록
```

### 5단계: SCA 결과 조회
```
도구: get_sca_analysis_results
입력:
  - analysisId: sca_analysisId
출력: 라이브러리 취약점, 라이선스 정보
```

### 6단계: 통합 보고서 작성
두 결과를 종합하여 하나의 보고서로 작성합니다.

## 결과 보고 형식

```markdown
# 🛡️ 종합 보안 점검 보고서

**프로젝트**: [폴더명]
**분석일시**: [날짜]

---

## 📊 Executive Summary

| 구분 | 항목 | Critical | High | Medium | Low |
|------|------|----------|------|--------|-----|
| SAST | 코드 취약점 | N | N | N | N |
| SCA | 라이브러리 취약점 | N | N | N | N |
| **합계** | | **N** | **N** | **N** | **N** |

---

## 🔍 Part 1: 소스코드 취약점 (SAST)

### 발견된 취약점
1. [파일:라인] **취약점명** - 설명
   - 심각도: Critical
   - 권장 조치: [조치 내용]

### 컴플라이언스 현황
[OWASP, CWE 매핑 정보]

---

## 📦 Part 2: 오픈소스 라이브러리 (SCA)

### 취약한 라이브러리
| 라이브러리 | 버전 | CVE | CVSS | 권장 버전 |
|-----------|------|-----|------|----------|
| lodash | 4.17.15 | CVE-2021-23337 | 7.2 | 4.17.21 |

### 라이선스 현황
| 라이브러리 | 라이선스 |
|-----------|---------|
| react | MIT |

---

## ✅ 종합 권장 조치사항

### 즉시 조치 필요 (Critical/High)
1. [SAST] SQL Injection 수정
2. [SCA] lodash 업데이트

### 중기 조치 (Medium)
1. ...

### 참고사항 (Low)
1. ...
```

## 🌐 언어 정책 (Language Policy)

- 결과 언어는 사용자가 입력한 프롬프트의 언어를 따릅니다.
- 모든 출력 및 보고서는 사용자의 언어로 표시하여 가독성을 높입니다.

## 추가 작업

- SAST 취약점은 `generate_secure_code` (입력: analysisId, fileContent, fileName)로 수정 코드 제공
- SCA 취약점은 업데이트 명령어 제공 (npm update 등)
- 우선순위별로 조치 가이드 정리

## 장점

이 통합 skill을 사용하면:
- ✅ 코드 취약점과 라이브러리 취약점을 한번에 파악
- ✅ 전체 보안 상태를 한눈에 볼 수 있는 통합 보고서
- ✅ 우선순위에 따른 조치 가이드 제공
