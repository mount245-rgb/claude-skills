---
name: pine-poa-converter
description: 기존 Pine Script v6 전략 코드를 POA 자동매매 봇 형식으로 변환하는 스킬. 원본 파인스크립트 코드를 붙여넣으면 POA 봇 적용 코드로 즉시 변환함. 파인스크립트 변환, POA 변환, 자동매매 봇, 포아봇 변환 키워드가 포함된 요청에 사용.
---

# Pine Script → POA 자동매매 봇 변환기

기존 Pine Script v6 전략 코드를 POA 자동매매 봇 형식으로 변환하는 스킬.

## 입력

사용자가 Pine Script v6 코드를 붙여넣으면 아래 규칙에 따라 변환한다.

---

## 변환 규칙

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

> `YOUR_BOT_ID`는 사용자의 실제 봇 ID로 교체 필요함을 안내할 것

---

### 규칙 2: `strategy()` 함수에 마진 파라미터 추가

변환 전:
```pine
strategy("전략명", overlay=true, default_qty_type=strategy.percent_of_equity, default_qty_value=100)
```

변환 후:
```pine
strategy("전략명", overlay=true, default_qty_type=strategy.percent_of_equity, default_qty_value=100, margin_long=0, margin_short=0)
```

---

### 규칙 3: `strategy.entry()` → `pstrategy.entry()` 로 교체

- `strategy.long` → 문자열 `"strategy.long"` 으로 변환
- `strategy.short` → 문자열 `"strategy.short"` 으로 변환
- 원본 줄은 `//` 주석 처리

예시:
```pine
// 변환 전
strategy.entry("Long", strategy.long, qty_percent=riskPct)

// 변환 후
pstrategy.entry("Long", "strategy.long", qty_percent=riskPct)
// strategy.entry("Long", strategy.long, qty_percent=riskPct)
```

---

### 규칙 4: `strategy.exit()` → `pstrategy.exit()` 로 교체

원본 줄은 `//` 주석 처리

예시:
```pine
// 변환 전
strategy.exit("SL/TP", from_entry="Long", stop=stopPrice, limit=tpPrice)

// 변환 후
pstrategy.exit("SL/TP", from_entry="Long", stop=stopPrice, limit=tpPrice)
// strategy.exit("SL/TP", from_entry="Long", stop=stopPrice, limit=tpPrice)
```

---

### 규칙 5: `strategy.close()` → `pstrategy.close()` 로 교체

원본 줄은 `//` 주석 처리

예시:
```pine
// 변환 전
strategy.close("Long", comment="청산")

// 변환 후
pstrategy.close("Long", comment="청산")
// strategy.close("Long", comment="청산")
```

---

## 출력 형식

1. **[변환 전] 원본 코드**: 원본 그대로 코드 블록으로 출력
2. **[변환 후] POA 봇 적용 코드**: 변환된 코드 블록으로 출력
3. **사용 안내**:
   - `YOUR_BOT_ID` → 실제 POA 봇 ID로 교체 필요
   - `kis_number` → 사용 증권사 계좌 번호에 맞게 수정 필요
   - `end_time` → 자동매매 종료일 조정 가능

---

## TradingView MCP 연동 (선택)

코드 생성 후 사용자가 원하면 TradingView MCP로 직접 적용:
- `pine_set_source` → 코드 주입
- `pine_smart_compile` → 컴파일 및 오류 확인
- `pine_get_errors` → 오류 확인 후 수정
