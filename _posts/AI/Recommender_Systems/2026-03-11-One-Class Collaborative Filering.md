---
title: "[Paper Review] #03 One-Class Collaborative Filtering"
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

**One-Class Collaborative Filtering**  
(Rong Pan, Yunhong Zhou, Bin Cao, Nathan N. Liu, Rajan Lukose, Martin Scholz, Qiang Yang, IEEE ICDM, 2008)  
{: .notice--info}

## 1. OCCF 문제
실제 추천 시스템에서는 explicit feedback (rating 등)보다 implicit feedback (클릭, 구매, 시청 기록 등)이 더 많이 존재한다. <br>
<br>
이렇게 implicit data의 경우, explicit과 달리(explicit은 명시적으로 positive, negative 있음) <span style="color: blue"> negative example </span>이 없다. <br>
대신 관찰된 postive와 missing data 만 있다. <br> <br>
문제는 이 missing 값이 모호하다는 것이다. <br>
missing data는 <br>1. 사용자가 관심이 없음 (negative)<br>
2. 사용자가 아직 보지 못함 (unknown positive)<br> 이렇게 두 가지 가능성이 있다. 
   

**missing 데이터 안에는 negative와 positive가 섞여 있기 때문에 missing 중에서 실제 positive를 찾아내야한다**


## 2. 기존의 접근 방법들 (BAD)
### 1) AMAN (All Missing As Negative)
missing을 전부 negative로 간주하는 아이디어 <br>
- 계산이 단순함
- 실제 positive도 negative로 처리 → bias 발생가능

### 2) AMAU (All Missing As Unknown)
missing 완전 무시 <br>
- trivial solution 발생  (똑같이 bias O)
- 모든 missing을 positive로 예측하는 모델 가능

따라서 OCCF의 핵심 문제는 <br>
<span style="color: red"> missing을 weak negative로 모델링하는 것 </span>이다. 

## 3. Weighted Low-Rank Approximation (WLRA)
AMAN, AMAU 는 너무 극단적인 방법이니 
positive → 높은 가중치(=1), missing → 낮은 가중치(weak) negative(0~1사이 값) 준다는 아이디어
<br> <br>

### MF
사용자–아이템 행렬 <br>
$R \in \mathbb{R}^{m \times n}$ 를 다음처럼 분해한다. <br>
<br>

$X = UV^T$
- $U$ : 사용자 latent vector
- $V$ : 아이템 latent vector

### Loss Function
Weighted Frobenius Loss : $L(X) = \sum_{ij} W_{ij}(R_{ij} - X_{ij})^2$ 인데, <br>
$X = UV^T$ 를 대입하면 <br>
$L(U,V) = \sum_{ij} W_{ij}(R_{ij} - U_i V_j^T)^2$이 된다. 

<br> 
그런데 이제 과적합 방지를 위해 정규화항을 추가한다. <br>

$L(U,V) = \sum_{ij} W_{ij}(R_{ij} - U_i V_j^T)^2 + \lambda (||U||_F^2 + ||V||_F^2)$

여기서

- $\lambda$ : regularization parameter
- $||\cdot||_F$ : Frobenius norm

### wALS (Weighted Alternating Least Squares)
사용자 벡터와 item 벡터가 따로 있기 때문에 ALS 이용해서 최적화를 한다. <br>
U를 고정시키고 V를 먼저 업데이트한 뒤, <br>
$U_i = R_i W_i V (V^T W_i V + \lambda \sum_j W_{ij} I)^{-1}$
V를 고정시키고 U를 업데이트하기를 반복한다. <br>
$V_j = R_j^T W_j U (U^T W_j U + \lambda \sum_i W_{ij} I)^{-1}$
![wALS](/assets/images/OCCF_wALS.png) 


### weighting scheme
![OCCF_weighting_scheme](/assets/images/OCCF_weighting_scheme.png) 


1) Uniform : 모든 사용자와 아이템에 대해 negative example이 나타날 확률을 동일하게 가정       <br>
2) user oriented  :   사용자가 많은 positive example을 가지고 있다면, 다른 아이템을 좋아하지 않을 가능성이 크다고 가정   <br>
3) item oriented : 많은 사용자에게 선택받지 못한 아이템은 다른 사용자에게도 선택 받지 못 할 가능성이 크다는 가정<br>

## 4. Negative Example Sampling
![OCCF_weighting_scheme](/assets/images/OCCF_Negative_Sampling.png) 

R에 있는 positive는 유지하고 경험적으로 얻은 확률 분포에 기반하여 missing 중 일부만 negative로 샘플링한 후, ALS 학습하고 여러 모델 평균내는 아이디어(Bagging). <br> <br>

### Sampling Scheme
1) Uniform : 모든 missing 동일한 확률주기 ($P_{ij} \propto 1$)       <br>
2) user oriented : user가 많은 item을 봤다면 남은 item은 negative일 가능성이 높다는 아이디어에서 기반해서 $P_{ij} \propto \sum_i I(R_{ij}=1)$ <br>
3) item oriented : popular하지 않은 item은 negative일 가능성이 높다는 가정 $P_{ij} \propto \frac{1}{\sum_j I(R_{ij}=1)}$
   
### Bagging (앙상블)
negative sampling은 확률분포에 기반해서 (=stochastic) 불안정하다. <br>
그래서 여러 모델을 학습하고 평균을 내는 과정이 필요하다(Bagging). 

![OCCF_Bagging](/assets/images/OCCF_Bagging.png) 


## 5. 결론
기존 baseline(AMAU, AMAN)에 비해서는 샘플링 기법과 가중치 주는 기법 둘 다 약 8%가량 더 나은 성능을 보였다. <br>
(자세한 내용은 생략)

> **[교수님께서 질문하신 부분]**
> **1. loss 계산 측면에서 학습과정에서 가중치를 부여하는 것이 왜 더 정확한 학습을 돕는지** <br>
> A. loss 함수에서 가중치는 각 example의 오차가 전체 학습에 얼마나 크게 반영되는지를 결정하는 역할을 합니다.
> OCCF에서는 관찰된 positive example은 신뢰도가 높지만, missing example은 대부분 negative일 가능성이 높더라도 잠재적인 positive가 섞여 있기 때문에 신뢰도가 낮습니다.<br>만약 가중치에 차이를 두지 않으면 모든 오차항이 동일하게 반영되는데, OCCF 문제에서는 missing = 0으로 두고 학습하기 때문에 사실상 AMAN과 유사해져 bias가 발생할 수 있습니다.<br>따라서 positive에는 큰 가중치를, missing에는 더 작은 가중치를 주어 데이터의 신뢰도 차이에 따라 loss에서의 영향을 주는 정도를 다르게 하는 것은 더 정확한 학습을 돕는다고 생각합니다.<br><br>
> **missing의 가중치가 커지는 것이 무엇을 의미하는지** <br>
> A. 가중치는 일단 지난 논문 (2번 논문 리뷰 참고, MF 논문)에서 confidence level과 유사하다고 생각했습니다. 그리고 missing의 가중치가 크다는 것은 loss 함수에서 missing example의 오차항이 더 크게 반영된다는 뜻이며, 학습 과정에서는 missing을 negative처럼 보는 경향이 더 강해지는 것으로 이해했습니다. <br> 논문에서 observed positive =1, missing은 0으로 기본적으로 두는 점을 보고 이와 같이 생각했습니다.
<br>
<br>
위 두 질문은 발표를 논문을 읽은지 오랜 시간이 흐른 후 발표를 진행하여 기억이 많이 휘발된 탓에<br>
발표 중 헷갈리는 부분이 생겨 잘못 설명한 바람에 교수님께서 질문하신 부분이었다. <br>
논문을 읽는 것에 그치지 않고, 블로깅과 한 장 정리를 하는 것도 좋으나 더 머리 속에 콱! 아주 콱! 박히게 걍 내꺼로 만들 수 있게 노력해야겠다 ㅜㅜ<br> <br>

또한 사용자-아이템 행렬에서 sparsity 문제를 어떻게 해결할 것인지에 대해 논하는 논문이었는데, 읽으면서 수식부분은 어렵긴 하였으니 흥미로웠다. <br>
따라서 가능하다면 implicit data를 가지고 하는 recommender system 관련 프로젝트를 하고 싶어졌다. 


<br>

    개인 공부 기록용 블로그입니다.
    오류나 틀린 부분이 있을 경우 언제든지 글이나 메일로 지적해주시면 감사하겠습니다! ☺

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}
