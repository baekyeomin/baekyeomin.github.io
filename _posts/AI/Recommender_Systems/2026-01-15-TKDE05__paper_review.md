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