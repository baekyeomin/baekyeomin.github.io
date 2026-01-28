---
title: "[Paper Review] Toward the Next Generation of Recommender Systems: A Survey of the State-of-the-Art and Possible Extensions"
categories: [Activities, Recommender Systems]
tags:
  - AI
  - Recommender Systems
  - Content-Based
  - Collaborative Filtering
  - Hybrid
layout: single
toc : true
toc_sticky: true
comments: true
use_math: true
---

**Toward the Next Generation of Recommender Systems: A Survey of the State-of-the-Art and Possible Extensions**  
(Gediminas Adomavicius, Alexander Tuzhilin, IEEE TKDE, 2005)
{: .notice--info}

## 0. 이 논문에 대하여..

본 논문은 추천 시스템 분야를 전반적으로 정리한 survey 논문입니다. 
<br> <br>
국민대학교 배홍균 교수님 연구실에 들어가 처음 읽게 된 논문으로, 기존 추천 시스템의 접근 방식을 CB, CF, Hybrid 3가지로 분류하고, 각 방법이 가지는 한계를 소개한 뒤, 추천시스템이 앞으로 확장될 수 있는 방향을 제시합니다. <br>


## 1. Introduction

논문에서 추천 시스템이란, <span style="color: blue">사용자 집합 C가 있을 때, 각 사용자 c에 대해 효용 함수 u(c, s)를 최대화하는 아이템 s를 추천하는 문제</span>라고 정의합니다. <br>
<br>
이를 수식으로 표현하면 다음과 같습니다. <br>
$\forall c \in C,\; s'_c = \arg\max_{s \in S} u(c, s)$

- C: 사용자 집합  
- S: 아이템 집합  
- u(c, s): 사용자 c에게 아이템 s가 얼마나 유용한지를 나타내는 함수 
  
여기서 <span style="background-color: #fff3cd">효용 함수 u(c, s)는 대부분의 경우 사용자가 아이템에 매긴 평점, 즉 rating으로 표현된다고 합니다.</span>

다음은 논문에서 제시된 예시 표입니다. <br>
![TKDE05](/assets/images/TKDE05_표.png)  <br>
위 표를 보면 예를 들어 Cindy라는 사용자는 "Notorious"라는 item에 대해 아직 rating을 매기지 않은 상태임을 확인할 수 있습니다. <br>
<br>
이처럼 모든 사용자–아이템 쌍에 대해 평점이 항상 존재하지는 않기 때문에,  <span style="background-color: #fff3cd">추천 시스템의 핵심은 이미 존재하는 rating을 기반으로, 아직 평가되지 않은 rating을 얼마나 잘 예측하는가</span>라고 볼 수 있습니다.
<br>
따라서 추천이 만들어지는 전체적인 흐름은 다음과 같이 정리할 수 있습니다. 

### 🐾 추천이 만들어지는 흐름
1. 경험적으로 효용함수 u (rating)를 정의 후 검증하거나
2. MSE 같은 기준을 최소화하는 등의 방식으로 함수 / 모델 학습
3. 예측한 평점 중 가장 큰 것 (또는 Top N개) 추천

> <span style="color: red">여기서 교수님께서 질문한 부분 </span>
> **Q. 1,2,3은 이어지는 흐름인가요?**
> A. 1~3 과정은 추천시스템의 공통적인 흐름이나 heuristic based인지 model based인지에 따라 1,2를 구현하는 방식에서 차이가 납니다. <br> model based 같은 경우 어떤 모델을 정의해서 파라미터를 조정도 하고, MSE 같은 loss를 최소화하는 과정을 거칩니다. <br> 반면 heuristic based는 명시적인 학습과정이 없을 수도 있습니다. <br> 경험적인 규칙이 합리적인지 검증하는 과정만 필요할 수 있기에 검증하거나 학습 (1또는2)라고 하는 것입니다. <br> 그런 다음 3번 과목은 모든 방식에서 공통적인 부분입니다. 
> <br>
> **Q. "경험적으로" (Heuristic)의 의미가 뭐라고 생각하시나요?**
> A. (이 부분은 내가 답한 후에 추후 더 공부해라고 하셔서 다시 생각해낸 답변) <br> 
> 추천 시스템에서 heuristic (경험적인)의 의미는 <span style="background-color: #fff3cd"> 복잡한 모델을 학습하기 보다는 관찰된 데이터와 직관에 기반해서 효용을 근사적으로 계산하는 방식</span>이라고 생각합니다. <br> 그래서 경험적이라고 하는 이유는 이론적으로 이것이 정말 최적이라고는 증명되지 않았으나 현실의 데이터와 반복된 실험 경험을 통해 대체로 잘 작동한다고 알려진 방법을 의미하기 때문이라고 생각합니다. 예를들어 평점 패턴이 비슷한 사용자는 취향도 비슷할 것이다, 유사한 아이템은 비슷한 평점을 받을 것이다와 같은 가정에서 출발해서 어느정도 괜찮은 결과까지 내지만, 그 가정이 항상 참임을 수학적으로 증명하지는 않는 것입니다. 