---
name: pine-script-from-youtube
description: 유튜브 링크 또는 영상 스크립트/내용을 입력받아 TradingView Pine Script v6 전략 코드를 자동 생성하는 스킬. 트레이딩 전략 설명, 유튜브 URL, 또는 영상 자막/요약을 제공하면 즉시 백테스팅 가능한 Pine Script 코드를 반환함. 트레이딩 전략, Pine Script, 유튜브 전략 영상, 퀀트, 백테스팅 키워드가 포함된 요청에 사용.
---

# Pine Script From YouTube

유튜브 영상 또는 텍스트 전략 설명을 Pine Script v6 코드로 생성하는 스킬.

## 입력 처리 순서

### 1. 유튜브 URL이 제공된 경우
`youtube-transcript` MCP 도구(`get_transcript`)로 자막을 가져온다:
```
get_transcript(url=<유튜브 URL>, lang="ko")
```
- 한국어 자막 없으면 영어(`lang="en"`)로 재시도
- 자막 취득 실패 시 사용자에게 직접 내용을 붙여넣어 달라고 요청

### 2. 텍스트(스크립트/요약)가 직접 제공된 경우
가져온 자막 또는 제공된 텍스트를 아래 지시에 따라 바로 처리

---

## Pine Script v6 생성

### Role
시니어 퀀트 개발자 및 Pine Script 전문가

### Process

**1단계 — 전략 논리 추출**
- 진입 조건(Long/Short), 청산 조건(Exit), 손절가(Stop Loss), 목표가(Take Profit), 사용 보조지표 및 설정값 파악
- 구체적 수치가 없으면 표준값 적용 후 주석 명시

**2단계 — Pine Script v6 코딩**
- 최상단에 `//@version=6` 명시
- `indicator()` 대신 `strategy()` 사용 (백테스팅 필수)
- 모든 주요 변수는 `input()` 함수로 처리
- 진입/청산 로직은 `if` 조건문으로 작성
- `plot()` 으로 진입/청산 지점 및 주요 지표 시각화
- **자금 관리(Money Management)** 로직 기본 포함
- 각 주요 섹션에 **한글 주석** 작성

**3단계 — 검증**
- 구문 오류 없이 즉시 컴파일 가능한 코드 생성
- 모호한 내용은 트레이딩 관점에서 가장 합리적으로 해석 후 완성 (되묻지 않음)

---

## 출력 형식

1. **전략 요약** (3줄): 진입 조건 / 청산 조건 / 주요 설정
2. **Pine Script v6 코드**: 백테스팅용 코드 블록

---

## TradingView MCP 연동 (선택)

코드 생성 후 사용자가 원하면 TradingView MCP로 직접 적용:
- `pine_new` → 새 스크립트 생성
- `pine_set_source` → 코드 주입
- `pine_smart_compile` → 컴파일 및 오류 확인
- `pine_get_errors` → 오류 확인 후 수정
