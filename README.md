# MLB Footwear Trend Dashboard - Data

Supabase 대신 GitHub 저장소를 정적 데이터 소스로 사용하는 구조입니다.
(GitHub Pages에서 fetch()로 바로 읽어서 대시보드에 연결하는 방식 — 서버/DB 불필요)

## 폴더 구조

```
mlb-footwear-trend-data/
├── data/
│   └── mlb_footwear_items.json     # 분류된 606건 데이터 (record 배열)
├── schema/
│   ├── MLB_Musinsa_8Line_Footwear_Taxonomy_v2_2.json   # mlb_line/subcategory 정의
│   ├── MLB_Footwear_Attribute_Labeling_Schema_v1.json  # 속성/판정 스키마 (output_record_template 기준)
│   └── MLB_Footwear_Lifestyle_DNA_Config_v1_1.json     # lifestyle 4축 정의
└── README.md
```

## data/mlb_footwear_items.json 레코드 형식

`MLB_Footwear_Attribute_Labeling_Schema_v1.json`의 `output_record_template` +
`supabase_storage.fixed_columns`/`jsonb_columns`을 그대로 따릅니다.
(Supabase 테이블 스키마와 필드 1:1 동일 — 나중에 DB로 옮기고 싶어지면 그대로 이관 가능)

- `mlb_line`: LINER / RUNNER / SNEAKERS / CHUNKY / OUTDOOR / SUMMER ITEM / WINTER ITEM / NEW LINE
- `primary_lifestyle` / `secondary_lifestyle`: PREMIUM SPORTIVE / STREET / FEMININE / WELLNESS
- `review_status`: AI_CONFIRMED / REVIEW_REQUIRED / USER_CONFIRMED / OUT_OF_SCOPE

## 대시보드에서 불러오는 방법 (예시)

GitHub Pages로 이 저장소를 배포하면, 같은 조직의 다른 정적 사이트(trend_agent_2 등)에서
아래처럼 fetch로 바로 읽을 수 있습니다.

```js
const res = await fetch('https://consumerfnf-eng.github.io/mlb-footwear-trend-data/data/mlb_footwear_items.json');
const { items } = await res.json();
```

Private 저장소로 유지하고 싶다면 raw.githubusercontent.com + GitHub Personal Access Token 조합으로
fetch 헤더에 인증을 넣어 접근하는 방식도 가능합니다.

## 다음 단계

1. 이 폴더를 새 저장소(`mlb-footwear-trend-data`) 또는 기존 `trend_agent_2` 저장소 안 서브폴더로 push
2. 나머지 605건을 위 스키마 형식으로 채워서 `data/mlb_footwear_items.json`에 추가 (Claude API 배치 스크립트로 자동화 예정)
3. `trend_agent_2`의 대시보드 화면에서 이 JSON을 fetch해서 라인별/라이프스타일별 트렌드 차트 렌더링
