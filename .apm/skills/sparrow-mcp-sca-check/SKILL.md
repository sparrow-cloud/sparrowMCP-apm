---
name: sparrow-mcp-sca-check
description: 오픈소스 라이브러리의 취약점 및 라이선스 이슈를 검사합니다
---

# Sparrow SCA Check

프로젝트의 오픈소스 의존성을 분석하여 취약한 라이브러리와 라이선스 이슈를 찾습니다.

## 사용 시나리오

사용자가 다음과 같이 요청할 때 이 skill을 적용하세요:
- "package.json 라이브러리 취약점 있어?"
- "오픈소스 보안 점검해줘"
- "의존성 보안 검사해줘"
- "라이선스 이슈 확인해줘"
- "SCA 분석해줘"

## 지원 파일 형식

| 패키지 관리자 | 파일명 |
|--------------|--------|
| npm/Node.js | package.json, package-lock.json |
| Maven/Java | pom.xml |
| Gradle | build.gradle |
| Python | requirements.txt, Pipfile |
| .NET | *.csproj, packages.config |

## 실행 단계

### 단일 파일 분석

#### 1단계: SCA 분석 시작
```
도구: analyze_file_sca
입력:
  - filePath: 의존성 파일 경로 (예: "package.json")
출력: analysisId
```

### 폴더 전체 분석

#### 1단계: 폴더 SCA 분석 시작
```
도구: analyze_folder_sca
입력:
  - folderPath: 프로젝트 경로
출력: analysisId
```

### 공통 단계

#### 2단계: 진행률 추적
```
도구: track_analysis_progress
입력:
  - analysisId: 분석 ID
  - intervalMs: (선택) 진행 상태 확인 주기(ms)
출력: 완료 상태
```

#### 3단계: SCA 결과 조회
```
도구: get_sca_analysis_results
입력:
  - analysisId: 분석 ID
출력: 컴포넌트 목록, 취약점, 라이선스 정보
```

#### 4단계: SBOM 파일 다운로드 (선택)
```
도구: download_sbom_files
입력:
  - analysisId: 분석 ID
출력: 다운로드된 SBOM 파일 목록 (SPDX, CycloneDX, SWID)
```

## 결과 보고 형식

```markdown
# 📦 오픈소스 보안 점검 결과

## 요약
| 항목 | 수치 |
|------|------|
| 검사된 컴포넌트 | N개 |
| 취약점 발견 | N개 |
| Critical | N개 |
| High | N개 |

## 🚨 취약한 라이브러리

### 1. lodash@4.17.15
- **취약점**: CVE-2021-23337 (CVSS: 7.2)
- **심각도**: High
- **설명**: Prototype Pollution 취약점
- **권장 조치**: `4.17.21` 이상으로 업데이트

### 2. express@4.16.0
- **취약점**: CVE-2022-24999 (CVSS: 5.3)
- **심각도**: Medium
- **권장 조치**: `4.18.2` 이상으로 업데이트

## 📋 라이선스 현황

| 라이브러리 | 버전 | 라이선스 |
|-----------|------|---------|
| react | 18.2.0 | MIT |
| lodash | 4.17.15 | MIT |

## ✅ 권장 조치

```bash
# Critical/High 취약점 해결
npm update lodash
npm update express

# 또는 특정 버전으로 업그레이드
npm install lodash@4.17.21
```
```

## 🌐 언어 정책 (Language Policy)

- 결과 언어는 사용자가 입력한 프롬프트의 언어를 따릅니다.
- 모든 출력 및 보고서는 사용자의 언어로 표시하여 가독성을 높입니다.

## 추가 안내

SCA 분석은 코드 자체가 아닌 **의존성 라이브러리**를 분석합니다.
- SAST: 작성한 코드의 취약점 (SQL Injection, XSS 등)
- SCA: 사용하는 라이브러리의 알려진 취약점 (CVE)

두 가지를 함께 수행하면 더 완벽한 보안 점검이 가능합니다.
