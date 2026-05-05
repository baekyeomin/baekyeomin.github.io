---
title: "[Paper Review] #05 Let Me Do It For You: Towards LLM Empowered Recommendation via Tool Learning"
categories: [Activities, Recommender Systems]
tags:
  - AI
  - Recommender Systems
layout: single
toc : true
toc_sticky: true
comments: true
use_math: true
---

**Let Me Do It For You: Towards LLM Empowered
Recommendation via Tool Learning**  
()
{: .notice--info}

intro 예시 
## 🎬 ToolRec 예시 (간단 정리)

### 👤 사용자
- 액션 영화 선호
- 최신 영화 좋아함
- 유명 배우 선호

---

### 🔁 Round 1: 장르 기준 탐색
- Action: Retrieval [genre=action, top=5]
- 결과: 액션 영화 5개

👉 문제: 오래된 영화 많음

---

### 🔁 Round 2: 개봉 연도 보정
- Action: Retrieval [release_year=recent, top=3]
- 결과: 최신 영화 추가

👉 문제: 배우 인지도 낮음

---

### 🔁 Round 3: 배우 기준 정렬
- Action: Rank [actor popularity, top=4]
- 결과: 유명 배우 중심 재정렬

---

### ✅ Round 4: 종료
- 조건 (장르 + 최신성 + 배우) 만족 → Finish

---

## 🔥 핵심
- LLM이 바로 추천하지 않음
- 속성(attribute) 단위로 반복 탐색
- Retrieval + Ranking 도구 사용
- 점진적으로 추천 리스트 개선

👉 한 줄 요약:
"추천을 한 번에 하지 않고, 여러 조건을 맞춰가며 완성하는 방식"