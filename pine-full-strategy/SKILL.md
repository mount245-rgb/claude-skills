---
name: pine-full-strategy
description: 유튜브 링크, 스크립트, 또는 전략 설명을 입력받아 Pine Script v6 원본 전략 생성과 POA 자동매매 봇 변환을 한 번에 처리하는 통합 스킬. 전략 만들어줘, 파인스크립트 만들어줘, 자동매매 전략 키워드가 포함된 요청에 사용. 사용자에게 되묻지 않고 원본+변환 코드를 즉시 출력.
---

# Pine Script 전략 생성 + POA 변환 통합 스킬

유튜브 링크, 스크립트 내용, 또는 전략 설명을 받아 두 단계를 자동으로 순서대로 처리한다.

---

## 입력 처리

### 유튜브 URL이 제공된 경우
`youtube-transcript` MCP(`get_transcript`)로 자막 취득:
```
get_transcript(url=<URL>, lang="ko")
```
- 한국어 자막 없으면 `lang="en"` 으로 재시도
- 자막 실패 시 사용자에게 내용 붙여넣기 요청

### 텍스트/전략 설명이 직접 제공된 경우
바로 STEP 1 진행

---

## STEP 1 — Pine Script v6 생성

### Role
시니어 퀀트 개발자 및 Pine Script 전문가

### Process

**1단계 — 전략 논리 추출**
- 진입 조건(Long/Short), 청산 조건(Exit), 손절가(Stop Loss), 목표가(Take Profit), 보조지표 및 설정값 파악
- 구체적 수치 없으면 표준값 적용 후 주석 명시

**2단계 — Pine Script v6 코딩**
- 최상단에 `//@version=6` 명시
- `indicator()` 대신 `strategy()` 사용
- 모든 주요 변수는 `input()` 함수로 처리
- 진입/청산 로직은 `if` 조건문으로 작성
- `plot()` 으로 진입/청산 지점 및 주요 지표 시각화
- **자금 관리(Money Management)** 로직 기본 포함
- 각 주요 섹션에 **한글 주석** 작성

**3단계 — 검증**
- 구문 오류 없이 즉시 컴파일 가능한 코드 생성
- 모호한 내용은 트레이딩 관점에서 합리적으로 해석 후 완성 (되묻지 않음)

---

## STEP 2 — POA 자동매매 봇 변환

STEP 1 생성 코드를 기반으로 즉시 변환. **사용자에게 묻지 않고 바로 처리.**

### 규칙 1: `//@version=6` 바로 다음 줄에 POA 헤더 블록 삽입
```pine
//@strategy_alert_message {{strategy.order.alert_message}}
// ************************************** POA ************************************** //
import dokang/POA/17 as POA

// 시작시간, 종료시간 설정
start_time = input.time(timestamp("2015-01-01T00:00:00+09:00"), title="자동매매 시작")
end_time   = input.time(timestamp("2030-12-31T23:59:59+09:00"), title="자동매매 종료")

// 포아봇 설정
pstrategy = POA.bot.new("YOUR_BOT_ID", start_time, end_time, kis_number = 1)
pstrategy.start_trade()
// ********************************************************************************* //
```

### 규칙 2: `strategy()` 함수에 마진 파라미터 추가
```pine
strategy("전략명", ..., margin_long=0, margin_short=0)
```

### 규칙 3: `strategy.entry()` → `pstrategy.entry()` 교체
- `strategy.long` → `"strategy.long"` (문자열)
- `strategy.short` → `"strategy.short"` (문자열)
- 원본 줄은 `//` 주석 처리

```pine
pstrategy.entry("Long", "strategy.long", qty_percent=riskPct)
// strategy.entry("Long", strategy.long, qty_percent=riskPct)
```

### 규칙 4: `strategy.exit()` → `pstrategy.exit()` 교체
원본 줄 `//` 주석 처리

### 규칙 5: `strategy.close()` → `pstrategy.close()` 교체
원본 줄 `//` 주석 처리

---

## 출력 형식

**전략 요약** (3줄): 진입 조건 / 청산 조건 / 주요 설정

**[원본] Pine Script v6 코드** (백테스팅용):
```pine
(원본 코드)
```

**[변환] POA 봇 적용 코드** (자동매매용):
```pine
(변환된 코드)
```

**사용 안내**:
- `YOUR_BOT_ID` → 실제 POA 봇 ID로 교체 필요
- `kis_number` → 사용 증권사 계좌 번호에 맞게 수정 필요
- `end_time` → 자동매매 종료일 조정 가능

---

## TradingView MCP 연동 (선택)

사용자가 원하면 TradingView MCP로 직접 적용:
- `pine_set_source` → 코드 주입
- `pine_smart_compile` → 컴파일 및 오류 확인
- `pine_get_errors` → 오류 확인 후 수정
