# 🤝 서브에이전트 협업 프로토콜

## 에이전트 구성
| 에이전트 | 역할 | 산출물 |
|---------|------|--------|
| 🏗️ 건설 전문 | 데이터 수집 + 분석 + 시나리오 | `data/*.json`, `report/construction-analysis.md` |
| 💻 개발 전문 | 웹 대시보드 구축 | `web/index.html` |
| 🔍 평가 전문 | 결과물 품질 평가 | `report/evaluation.md` |

## 워크플로우 (순차 실행)

```
Phase 1: 건설 에이전트
  ├─ 레미콘·철근 시세 데이터 수집
  ├─ 변동 원인 분석
  ├─ 시나리오 설계 (낙관/기본/비관)
  └─ data/*.json + report/construction-analysis.md 산출

Phase 2: 개발 에이전트
  ├─ data/*.json 읽어서 차트 구성
  ├─ 대시보드 웹페이지 제작
  └─ web/index.html 산출

Phase 3: 평가 에이전트
  ├─ 보고서 + 대시보드 통합 검토
  ├─ 간결성·합리성·전문성 채점
  └─ report/evaluation.md 산출
      ├─ 70점 이상 → ✅ 승인
      └─ 70점 미만 → 🔄 Phase 1부터 개선 지시와 함께 재실행
```

## 인터페이스 규약

### 건설 → 개발 (데이터 전달)
```json
// data/remicon-prices.json
{
  "unit": "원/m³",
  "period": "monthly",
  "data": [
    { "month": "2025-04", "price": 72000, "change_pct": 0 },
    ...
  ]
}

// data/rebar-prices.json
{
  "unit": "원/ton",
  "period": "monthly",
  "data": [
    { "month": "2025-04", "price": 820000, "change_pct": 0 },
    ...
  ]
}

// data/analysis.json
{
  "causes": [
    { "factor": "...", "category": "supply|demand|external", "impact": "high|mid|low", "desc": "..." }
  ],
  "scenarios": {
    "optimistic": { "remicon_change_pct": -3, "rebar_change_pct": -5, "total_cost_impact_억": -2.1 },
    "base":       { "remicon_change_pct": 5,  "rebar_change_pct": 8,  "total_cost_impact_억": 3.5 },
    "pessimistic":{ "remicon_change_pct": 15, "rebar_change_pct": 20, "total_cost_impact_억": 9.8 }
  },
  "cost_structure": {
    "total_cost_억": 100,
    "remicon_pct": 10,
    "rebar_pct": 12
  }
}
```

### 평가 → 건설/개발 (피드백)
- `report/evaluation.md`에 기준별 점수 + 구체적 개선사항 기술
- 반려 시 어떤 에이전트의 어떤 부분을 수정해야 하는지 명시

## 원칙
1. **데이터는 JSON으로 통일** — 사람이 읽는 건 MD, 기계가 읽는 건 JSON
2. **단방향 흐름** — 건설 → 개발 → 평가 (역방향 요청 없음, 반려 시에만 루프)
3. **스키마 변경 금지** — 위 JSON 포맷을 임의 변경하지 않음
