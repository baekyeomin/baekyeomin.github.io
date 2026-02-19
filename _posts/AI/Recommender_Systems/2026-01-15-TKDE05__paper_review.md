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

> <span style="color: red">여기서 교수님께서 질문한 부분: </span> <br> 
> **Q. 1,2,3은 이어지는 흐름인가요?**<br> 
> A. 1~3 과정은 추천시스템의 공통적인 흐름이나 heuristic based인지 model based인지에 따라 1,2를 구현하는 방식에서 차이가 납니다. <br> model based 같은 경우 어떤 모델을 정의해서 파라미터를 조정도 하고, MSE 같은 loss를 최소화하는 과정을 거칩니다. <br> 반면 heuristic based는 명시적인 학습과정이 없을 수도 있습니다. <br> 경험적인 규칙이 합리적인지 검증하는 과정만 필요할 수 있기에 검증하거나 학습 (1또는2)라고 하는 것입니다. <br> 그런 다음 3번 과목은 모든 방식에서 공통적인 부분입니다. 

<br> 

> **Q. "경험적으로" (Heuristic)의 의미가 뭐라고 생각하시나요?** <br> 
> A. (이 부분은 내가 답한 후에 추후 더 공부해라고 하셔서 다시 생각해낸 답변) <br> 
> 추천 시스템에서 heuristic (경험적인)의 의미는 <span style="background-color: #fff3cd"> 복잡한 모델을 학습하기 보다는 관찰된 데이터와 직관에 기반해서 효용을 근사적으로 계산하는 방식</span>이라고 생각합니다. <br> 그래서 경험적이라고 하는 이유는 이론적으로 이것이 정말 최적이라고는 증명되지 않았으나 현실의 데이터와 반복된 실험 경험을 통해 대체로 잘 작동한다고 알려진 방법을 의미하기 때문이라고 생각합니다. <br> 예를들어 평점 패턴이 비슷한 사용자는 취향도 비슷할 것이다, 유사한 아이템은 비슷한 평점을 받을 것이다와 같은 가정에서 출발해서 어느정도 의미있는 결과까지 내지만, 그 가정이 항상 참임을 수학적으로 증명하지는 않는 것입니다.




## 2. Content Based Methods (CB)
### 🐾 Content Based Methods - Heuristic
Content-Based 방법은 <span style="background-color: #fff3cd">사용자 c가 과거에 높게 평가했던 아이템과 유사한 아이템 s의 효용(rating)을 추정하여 추천하는 방식</span>입니다. <br>
이 방법은 다른 사용자의 정보는 사용하지 않고 **해당 사용자 본인의 과거 행동 기록** 과 **item의 내용 (Content)** 만을 사용합니다. <br> <br> 

#### Item Profile과 TF-IDF
CB에서 각 아이템은 보통 **키워드 기반의 벡터**로 표현되며, 논문에서는 이를 item profile 또는 content(s)라고 부릅니다. <br> <br> 
예를 들어,
- **Fab 시스템**: 웹페이지를 중요 단어 100개로 표현
- **Syskill & Webert**: 정보량이 큰 단어 128개로 문서 표현
<br> <br> 
이처럼 문서를 문서를 키워드들의 집합으로 표현한 뒤, 각 단어에 가중치를 부여하는 대표적인 방법이 <span style="color: blue">TF-IDF</span>입니다 .
- **TF (Term Frequency)**  
  : 특정 단어가 한 문서 안에서 얼마나 자주 등장하는지  
$ TF_{i,j} = \frac{f_{i,j}}{\max_z f_{z,j}} $

- **IDF (Inverse Document Frequency)**  
  : 해당 단어가 전체 문서 집합에서 얼마나 흔한지  
  $ IDF_i = \log \frac{N}{n_i} $

- **TF-IDF**

$ w_{i,j} = TF_{i,j} \times IDF_i $ <br>
<span style="background-color: #fff3cd">한 문서에 자주 등장하지만 다른 문서에는 잘 등장하지 않는 단어일수록 그 문서를 잘 대표하는 키워드</span>가 됩니다. 
<br> <br>
아이템은 다음과 같은 벡터로 표현됩니다. 
$ Content(d_j) = (w_{1j}, \dots, w_{kj}) $

#### Content-Based Profile
사용자는 과거에 보거나 높게 평가한 콘텐츠들을 기반으로 <br> **ContentBasedProfile**이라는 사용자 선호 벡터를 가지게 됩니다. <br>

이때 사용되는 대표적인 방법에는 아래와 같은 것들이 있다고 소개하지만, 자세하게는 설명하지 않습니다. 
- Rocchio 알고리즘
- Naive Bayes 분류기 
- Winnow 알고리즘  

이렇게 구성된 사용자 프로필과 아이템 콘텐츠 벡터 사이의 유사도를 계산하여 효용을 정의합니다.

$ u(c, s) = score(ContentBasedProfile(c), Content(s)) $

즉,  
<span style="color: blue">content-based 추천에서 u(c, s)는 “얼마나 비슷한가”를 나타내는 유사도 기반 점수</span>라고 볼 수 있습니다.

정보검색 스타일의 접근에서는 **코사인 유사도**가 자주 사용됩니다.

> <span style="color: red">여기서 교수님께서 질문한 부분: </span> <br> 
> **Q. Content 벡터는 tfidf로 구현했다면, Content based profile은 어떻게 구현하나요?** <br> 
> A. user profile은 사용자가 선호하는 item들의 content 벡터를 가중 평균한 벡터로 만들어야할 것 같습니다. <br> 평균을 내는 이유는 먼저 user 의 선호도 관련 정보를 하나의 user 벡터로 요약할 때 가장 보편적이면서 쉬운 방법이 평균을 사용하는 것이기 때문이라고 생각했고, 평균을 냄으로써 어떤 키워드가 여러 item에서 반복적으로 강하게 나오면 평균에서도 값이 커지고, 어떤 키워드가 몇몇 item에서만 우연히 나온 단어면 평균을 내면서 희석될 수 있기 때문에 사용하면 좋다고 생각합니다. <br> 
> 식으로 표현하면 아래와 같을 것 같습니다. <br>
> $p_c = \frac{\sum_{s \in I_c} w_{c,s}\, v_s}{\sum_{s \in I_c} w_{c,s}}$
> <br>
> 여기서  
> $I_c$는 사용자 $c$가 보거나 평가한 item들의 집합이고,  <br> 
> $v_s$는 TF-IDF로 표현된 item $s$의 content 벡터입니다.  <br> 
> $w_{c,s}$는 사용자 $c$가 item $s$를 얼마나 선호하는지를 나타내는 가중치입니다 <br>
> <br>
> 가중치의 합으로 나누어 주는 것은 벡터의 크기를 정규화하여, item 개수에 상관없이 사용자 간 비교가 가능하도록 하기 위함입니다.
>가중치 $w_{c,s}$는 데이터 형태에 따라 다음과 같이 두는 것이 자연스럽다고 생각합니다.
> 
> - **explicit rating**이 있는 경우에는 $w_{c,s} = \max(r_{c,s} - \bar r_c, 0)$
>와 같이 사용자 평균보다 높게 평가한 item만을 사용자 선호로 반영할 수 있습니다.  <br>
> 이를 통해 사용자가 명확히 좋아한 item들의 특징이 프로필에 더 강하게 반영됩니다.
>
> - **implicit feedback**만 존재하는 경우에는 
클릭, 조회, 북마크 여부를 1로 두거나, 체류 시간과 같은 행동 정보를 정규화하여 가중치로 사용할 수 있습니다.
> <br>
> 이렇게 구성된 사용자 프로필 벡터 $p_c$와  
> 각 item의 content 벡터 $v_s$ 사이의 유사도를 계산하여  추천 점수를 정의합니다. 보통은 코사인 유사도를 사용하여, $u(c, s) = \cos(p_c, v_s)$ 와 같이 효용을 계산합니다. 

<br> 

> **Q. "경험적으로" (Heuristic)의 의미가 뭐라고 생각하시나요?** <br> 
> A. (이 부분은 내가 답한 후에 추후 더 공부해라고 하셔서 다시 생각해낸 답변) <br> 
> 추천 시스템에서 heuristic (경험적인)의 의미는 <span style="background-color: #fff3cd"> 복잡한 모델을 학습하기 보다는 관찰된 데이터와 직관에 기반해서 효용을 근사적으로 계산하는 방식</span>을 쓸 때 경험적이라고 말하는 것이라고 생각합니다. <br> 경험적이라고 할 때는 이론적으로 이것이 정말 최적이라고는 증명되지 않았으나 현실의 데이터와 반복된 실험 경험을 통해 대체로 잘 작동한다고 알려진 방법을 사용할 때를 의미한다고 생각합니다. <br> 예를들어 평점 패턴이 비슷한 사용자는 취향도 비슷할 것이다, 유사한 아이템은 비슷한 평점을 받을 것이다와 같은 가정에서 출발해서 어느정도 의미있는 결과까지 내지만, 그 가정이 항상 참임을 수학적으로 증명하지는 않을 때 경험적이라고 할 수 있을 것 같습니다. 


### 🐾 Content Based Methods - Model based
heuristic 방법과 달리 model based 방법은 머신러닝 모델들을 사용합니다. 
<br>
이 논문에서는 **Naive Bayes classifier** 를 대표적인 예로 듭니다. <br>
$ P(C_i \mid k_{1,j}, \dots, k_{n,j}) $
각 키워드가 독립이라는 가정하에, 아래와 같이 표현하기도 합니다. <br>
$ P(C_i) \prod_x P(k_{x,j} \mid C_i) $ <br>
논문에서는 키워드 독립 가정이 완벽하지 않더라도 실험적으로 높은 정확도를 보였다고 언급합니다. 

> <span style="color: red">여기서 교수님께서 질문한 부분: </span> <br> 
> **Q. 베이지안 분류기를 어떻게 활용할 수 있는지 식에 대해 더 자세히 설명 가능한가요?** <br> 
> A. 데이터과학에서 배운 바로는 베이지안 분류기는 P(클래스 | 데이터) 형태로, 주어진  데이터가 특정 클래스일 확률을 구하는 모델입니다. <br>
> 예를 들어 데이터과학에서 배웠던 스팸 메일 분류의 경우, 과거 메일들을 보고 각 키워드 마다 스팸 메일에서 얼마나 자주 나왔는지 확률을 계산해서 학습하고 새 메일이 왔을 때 어떤 키워드로 구성되어 있는지 보고 스팸일 확률을 계산합니다. 이후 확률이 가장 큰 클래스로 분류합니다. <br> 이를 추천시스템에 그대로 적용한다면, 어떤 item을 이루고 있는 키워드들을 각각의 feature들로 보고, 사용자가 특정 item을 좋아할 확률 P(like | item features)을 베이즈 정리를 통해 계산하는 방식으로 계산하는 방식으로 활용할 수 있습니다. 즉, 사용자가 과거에 선호했던 아이템들의 특징을 바탕으로, 새로운 아이템이 주어졌을 때 그 아이템을 좋아할 확률을 추정하는 것입니다. 그런데 이때 나이브 베이즈 같은 경우 키워드(feature)들끼리 서로 독립이라고 가정해서 계산을 더 단순화한것입니다. 

 <br>

>**Q. model based에서 TFIDF 는 언제 쓰일 수 있나요?**
> A. 스팸 메일 분류에서 했듯이 키워드 전처리를 할 때 사용될 수도 있고, 최종적으로 결과를 도출할 때 가중치로써 곱할 수도 있는 값이라고 생각했습니다. 
> -> 여기서 말이 많이 꼬였다. 교수님이 원했던 답은 아마 전처리에서 쓰인다고 일관성있게 답하는거였는듯... 가중치로써 쓰인다는 발상은 교수님께서도 못해본 발상이라고 하셨다. 
> <br>

### 🐾 Content-Based의 장점과 한계
**장점** <br>
1. 텍스트 기반 도메인(문서, 뉴스, URL)에 매우 효과적 <br>
2. 사용자 선호를 명시적인 프로필로 표현 → 높은 개인화 가능
<br>
<br>

**한계** <br>
1. **Limited Content Analysis**  
   - 키워드처럼 명시적으로 표현된 특징에만 의존하기에 영상이나 이미지 같은 데이터가 입력일 땐 활용하기 어려운 방법이라는 점 
2. **Overspecialization**  
   - 너무 기존에 시청했던 것과 비슷한 아이템만 반복 추천가능하다는 점 
3. **New User Problem**  
   - 충분한 평가 데이터가 없는 신규 사용자에 취약하다는 점

<br> <br> <br>

## 3. Collaborative Filtering Methods (CF)
### 🐾 Collaborative Filtering Methods - Heuristic (Memory based)
Collaborative filtering 방법은 <span style="background-color: #fff3cd">사용자 c와 비슷한 다른 사용자들이 선호한 아이템을 기반으로 사용자에게 아이템을 추천하는 방식 </span>입니다. <br>
앞에서 설명한 content-based 방법은 item의 정보를 중심으로 추천했다면, **collaborative filtering은 아이템의 내용은 고려하지 않고 사용자–아이템 평점 행렬에 나타난 과거 사용자들의 행동 패턴**만을 사용한다는 점에서 차이가 있습니다.   
<br> <br> 
<span style="background-color: #fff3cd"> 정리하면, 앞에서 설명한 CB가 item의 내용 (content)을 중심으로 추천을 수행했다면, CF는 item이 무엇인지에는 관심을 두지 않고, 누가 무엇을 어떻게 평가했는지에만 집중합니다 </span>

### 사용자 간 유사도 (similarity)
Memory based CF에서는 <span style="color: blue"> 사용자 c와 평점 패턴이 비슷한 이웃 사용자 (neighborhood) </span> 를 먼저 찾습니다. <br><br>
이를 위해 사용자 간 유사도를 계산하는데, 논문에서는 대표적으로 다음과 같은 방법들이 사용된다고 소개합니다. <br>
- **피어슨 상관계수 (Pearson Correlation)**
  ![Pearson Correlation](/assets/images/Pearson_Correlation.png)  
- **코사인 유사도 (Cosine Similarity)**
  ![Cosine Similarity](/assets/images/Cosine_Similarity.png)  

이렇게 계산된 유사도를 바탕으로, 사용자 c와 가장 비슷한 사용자 집합을 이웃으로 정의합니다. 
<br>

> <span style="color: red">여기서 교수님께서 질문한 부분: </span> <br> 
> **Q. CF에서는 item에 대한 정보 중에 무엇이 활용될 수 있나요?**<br>
> A. [미팅 시에 했던 대답] <br> CF에서는 item의 정보는 사용하지 않는다고 이해했습니다. <br> 영화 같은 경우, 장르나 출연 배우, 감독과 같은 정보는 필요하지 않다고 생각했습니다. 다만 item 간 구분은 필요하니 title 같은 정보는 필요하다고 생각합니다. <br> 
> [미팅 끝나고 다시 생각해보니..] <br>
> 진짜 개멍청한 대답이 따로 없었다. <br>
> 교수님께서 내가 이해는 해서 아는 것 같긴한데 자기가 묻는 질문에서 무얼 원하는지를 한번에 잘 파악 못하는 경우가 있다고 그랬다.. 그래서 곰곰히 생각해보았는데, 이 질문에 대해서 <span style="color: blue"> CF가 item의 content 정보 (장르..등)은 사용하지 않지만, item이 어떤 user들에 의해, 어떤 패턴으로 평가되었는지에 대한 정보 </span>는 활용한다고 대답했어야한다는 생각이 들었습니다. <br>
> 구체적으로는, 각 item이 사용자–아이템 평점 행렬에서 차지하는 한 열(column)로서 사용자들의 평점, 클릭 여부, 시청 여부와 같은**사용자 반응(interaction) 정보**가 활용될 수 있다는 답이 이 질문에 대한 올바른 답이 아니었을..까..싶네요.. 흑 흑 ㅜㅜ 😭

 <br>

> **Q. 그렇다면 title이 같은 item 같은 경우에는 어떻게 구분 가능할까요?** <br>
> A. PK (Primary Key)로 구분이 가능할 것 같습니다. 논문에서는 PK라는 용어 대신 ID 라는 용어를 사용하고 있습니다. 

<br>

### Rating Prediction 방식
Memory based CF에서는 item s에 대한 사용자 c의 에측 평점 (rating)을 $r_{c,s}$를 이웃 사용자들의 평점을 이용해 계산합니다. <br> 

<u>(a) 단순 평균 방식</u>

아이템 s를 평가한 모든 사용자들의 평균 평점을 사용자 c의 예측 평점으로 사용  

<u>(b) 유사도 가중 평균 방식</u>

사용자 간 유사도를 가중치로 사용하여 이웃 사용자들의 평점을 가중 평균  

<u> (c) 평점 기준 보정 방식</u>

사용자마다 다른 평점 기준(rating scale)을 고려하여 각 사용자의 평균 평점 대비 **편차(deviation)** 를 사용 

<br>

예를 들어, 항상 후하게 점수를 주는 사용자와  
상대적으로 박하게 점수를 주는 사용자가 섞여 있을 경우,  단순 평균 방식은 평점을 왜곡할 수 있습니다. <br>
이를 완화하기 위해 각 사용자의 평균 평점에서의 차이를 사용하여 사용자별 평점 성향을 보정합니다.

<br> <br> 

### 🐾 Collaborative Filtering Methods - Model based
Heuristic 방법과 달리 model based collaborative filtering은 통계적·확률적·머신러닝 모델을 학습하여 사용자–아이템 평점을 예측하는 방식입니다. <br> 
논문에서는 **베이지안 분류기, 확률적 모델, NN, 행렬분해 (matrix factorization)기법, clustering** 등과 같은 모델 기반의 접근들을 소개했습니다. 

<span style="color: blue"> 이러한 모델들은 사용자와 아이템을 잠재 요인(latent factor) 공간에 임베딩하여 관측되지 않은 평점을 예측합니다. </span> <br>

**-> 이에 대해서 다음 논문에서 다루겠습니다 !**
<br>
Model-based 방법은 대규모 데이터에서도 비교적 효율적으로 동작하고 노이즈에 강하다는 장점이 있지만, 모델 학습이 필요하다는 점에서 구현 및 계산 복잡도가 높아질 수 있습니다.

<br> <br>

### 🐾 Collaborative Filtering의 장점과 한계

**장점** <br>
1. 아이템의 콘텐츠 정보와 무관하게 적용 가능  
   (CB와 달리 텍스트, 이미지, 음악 등 도메인 제약이 적음 = 도메인에 대해 독립적임) <br>
2. 사용자가 과거에 소비하지 않았던 새로운 유형의 아이템 추천 가능 <br>
3. CB 방식의 **overspecialization 문제**를 완화 

<br>

**한계** <br>
1. **New User Problem**  
신규 사용자는 평점 데이터가 거의 없어, 정확한 추천이 어려움  

2.  **New Item Problem**  
새로운 아이템은 충분한 평가가 쌓이지 않아 추천되기 어려움  

3. **Sparsity Problem**  
실제 사용자–아이템 평점 행렬은 매우 희소한 sparse matrix이기 때문에 유사도 계산 및 모델 학습이 어려움  

<br>

이러한 한계를 보완하기 위해 content-based 방법과 collaborative filtering 방법을 결합한  
**hybrid 추천 방법**이 제안되었습니다. 

<br> <br> <br>

## 4. Hybid Methods 
Hybrid 방법은 앞에서 설명한 **content-based 방법**과 **collaborative filtering 방법**이 각각 서로 다른 장점과 한계점을 가지기 때문에, 두 방법을 적절하게 결합한 접근 방식입니다. <br>
<br>
Hybrid 방법은 두 추천 방식을 **어떤 방식으로 결합하느냐**에 따라 여러 유형으로 분류할 수 있습니다.

<br> <br>

### 🐾 Hybrid Methods - CB , CF 각각의 예측을 결합하는 방식
첫 번째 유형은 content-based와 collaborative filtering의 <span style="background-color: #fff3cd">예측 결과를 직접 결합하는 방식</span>입니다.
<br>
이 경우 두 방법은 각각 독립적으로 추천 점수 또는 순위를 계산한 뒤, 그 결과를 하나의 최종 추천 결과로 통합합니다. <br>

결합하는 방식에는 두 방식의 결과를 가중 합으로 결합하는 **선형결합 (linear combination)**, 각 방법의 추천 결과를 **투표(voting)**하는 형태로 결합하는 방식**, 상황에 따라 더 신뢰도가 높은 방법의 결과를 **선택(selection)** 하는 방식 등이 소개 되었습니다. 
<br> <br>

### 🐾 Hybrid Methods - CF에 CB 특성 추가
두 번째 유형은 collaborative filtering에 content-based의 특성을 추가하는 방식입니다. <br>
이 방법에서는 기존의 사용자–아이템 평점 행렬뿐 아니라,  아이템의 장르, 키워드, 속성과 같은 **콘텐츠 정보**를 함께 사용하여 CF 모델이  
아이템이 어떤 특징을 가지는지도 동시에 학습하도록 합니다. <br><br>
예시로는 **filterbot**이 있습니다. <br>
filterbot이란 콘텐츠 분석만 하는 가상의 사용자입니다. (예 : “SF 영화 필터봇”, “로맨스 코미디 필터봇”...) <br>
이러한 filterbot들은 item들에 대해 CB 기반의 평점을 매기도록하고, filerbot들을 하나의 user처럼 취급해서 실제 사용자와 filterbot의 rating이 잘 맞으면 추천을 하도록 합니다. <br>
또 다른 예시로는 **CB predictor**가 언급되었습니다. <br>
기존 CF에서 쓰면 user - item rating matirx에 CB 모델이 예측한 평점을 덧붙이는 것입니다. <br> 이렇게 해서 공통적으로 rating된 아이템이 없어도 유사도 계산이 가능하게 하는 것입니다. 

> <span style="color: red">여기서 교수님께서 질문한 부분: </span> <br> 
> **Q. CF에 CB 특성이 어떻게 추가되는지 더 구체적으로 설명 가능한가요? 본인이 구현을 한다고 생각하고...**
> A. 일단 기본구조는 CF입니다. 하지만 <span style="color: blue">user 간 유사도를 계산할 때 user들이 공통으로 평가한 아이템(user–item 행렬) 대신 각 사용자의 content-based profile을 사용합니다. </span> <br> 이 방법이 필요한 이유는 현실에서는 두 사용자가 공통으로 평가한 아이템 수보다 평가하지 않은 아이템 수가 훨씬 많아 단순 CF 모델 활용할 경우 sparsity 문제가 있기 때문입니다. <br>
> 이렇기에 content-based profile를 활용한다면 사용자가 평가한 아이템들의 콘텐츠 정보를 요약한 벡터이기 때문에, 어떤 item이 user들에 의해 공통적으로 rating되지 않았더라도 사용자 간 비교가 가능해져, sparsity 문제를 완화할 수 있습니다. <br> <br>
> 이를 제가 구현한다고 생각한다면...<br>
> 1) 먼저 각 user들이 높은 rating을 준 item들의 content 벡터를 기반으로 하나의 content-based profile을 만들 것 같습니다. <br>
> 2) 그 다음 content-based profile을 활용하여 사용자간 유사도를 계산합니다. <br>
> 3) 이후에 CF방식으로 할 것 같습니다 .<br>
> <br> 논문에서 이 방법은 <span style="color: blue"> 비슷한 content profile을 가진 사용자들이 높게 평가한 아이템이 추천될 수 있고 </span>, <span style="color: blue"> item 자체가 사용자의 content-based profile과 직접적으로 잘 맞는 경우에도 추천(다른 사용자가 아직 많이 평가하지 않았어도 추천 가능하게 하는 부분)이 가능하기 때문에 장점을 가진다고 말했습니다. 

 <br>

> **Q. 이때 CF는 model based인가요? memory based인가요?**<br>
> A. 유사도 계산을 하고 neighbor인 user를 찾는 점에서 memory based인 것 같습니다. 

 <br>

> **Q. 그럼 model based CF에 CB 결합하는 방식은 쓸 수 없나요?**<br>
> A. 쓸 수 있을 것 같습니다. model based는 머신러닝 모델을 활용하는 것이기 때문에 콘텐츠 벡터를 입력으로해서 학습하여 파라미터를 업데이트해가는 추천을 한다면... model based CF에 CB를 결합한 방식으로 추천이 이뤄질 수 있을 것 같습니다. 

 <br> 
이외에도 여러 질문을 하셨던 것으로 기억하는데.. 사실 짧게 넘어간 질문도 있고 모두 다 메모를 하면서 발표를 하는게 쉽지 않았어서... 내 답이 기억 안 나는 것도 있고... 해서 여기까지만 적겠으요..

### 🐾 Hybrid Methods - CB에 CF 특성 추가
세 번째 유형은 content-based에 collaborative filtering의 특성을 추가하는 방식입니다. <br>
이 방법은 content-based 추천 과정에서  
아이템의 콘텐츠 정보뿐 아니라, 다른 비슷한 사용자들의 평가 정보나 아이템의 인기도(popularity)와 같은 정보를 하나의 feature로써 함께 사용하는 방법입니다. <br> 
논문에서 소개된 대표적인 접근은 여러 사용자들의 content-based profile 집합에 차원 축소(dimensionality reduction, Latent Semantic Indexing(LSI)라는 기법을 언급) 기법을 적용해서 학습하는 방식입니다. <br>
이를 통해 CB 기반의 추천임에도 불구하고 여러 사용자들의 패턴이 반영되어서 collaborative view를 가지는 추천이 가능해집니다. <br>

> <span style="color: red">여기서 교수님께서 질문한 부분: </span> <br> 
> > **Q. "학습"이라고 했는데, 이건 model based CB에만 적용가능한 방법인건가요?**<br>
> A. (해당 질문을 듣고 뇌정지되었습니다. 정적이 몇초간 흘렀던 것으로 기억..대답제대로 못했던 것 같은데..잘 모르겠는데 찍어서 대답하기도 이상할 것 같아서..그랬던걸로 기억합니다) <br> 
> 미팅이 끝나고 검색해보니 차원축소하려거든 학습하는 과정이 필요하기에 모델 기반의 CB에 적용 가능한 방법이라고 한다. GPT 왈 heuristic content-based 방법만으로는 구현하기 어렵다는군뇨.... 그렇구Lㅏ....

<br>

> **Q. 이 방식을 활용할 때 content 벡터는 어떻게 만들 수 있을까요?**<br>
> A. TFIDF 벡터화를 하거나 장르 같은 데이터를 원핫인코딩..할 수 있을 것 같습니다...라고 답했던 것 같다. 이런 대답을 원했던 질문 같지는 않다는 생각을 하면서 대답했는데.. 다른게 떠오르지 않았고.. 어영부영.. 넘어가게되었어요 ㅜㅜ

<br>

> **Q. CF에 CB를 추가하는 방식과 CB에 CF를 추가하는 방식을 정리해서 말해주세요.**<br>
> A. CF에 CB를 추가하는 방식은 collaborative filtering을 기본으로 하되, 사용자 간 유사도를 계산하거나 평점을 예측하는 과정에서 아이템의 콘텐츠 정보를 함께 활용하는 접근하는 방식이고, CB에 CF를 추가하는 방식은 content-based 추천을 기본으로 하면서, 여러 사용자들의 패턴을 하나의 feature처럼 활용하는 접근방식입니다.  

### 🐾 Hybrid Methods - 단일통합모델
마지막 유형은 content-based와 collaborative filtering을 명확히 구분하기 보다는 <span style="background-color: #fff3cd">하나의 통합된 모델로 함께 학습하는 방식</span>입니다. <br><br>
논문에서 언급된 예시로는 **규칙 기반 분류기, 확률 모델, 베이지안 혼합 효과 회귀 모델** 등이 있습니다. 
<br> <br>

### Hybrid Methods의 효과
논문에서는 이러한 하이브리드 접근 방식이 CB나 CF 를 단독으로 활용하는 방식에 비해 <span style="color: blue">전반적으로 더 나은 성능을 보인다는 점이 여러 연구를 통해 경험적으로 입증되었다</span>고 말합니다. <br> <br>


## 결론 및 공헌
이 논문은 새로운 추천 알고리즘을 제안하거나 특정 기법을 상세히 소개하기보다는, 기존 추천 시스템들을 잘 분류하고 정리한 서베이 논문이었습니다. 
<br> <br>

논문에서는 추천 시스템을 크게 **content-based**, **collaborative**, **hybrid** 방법으로 구분하고, 각 접근 방식에서의 heuristic 기반 기법과 model-based 기법들을 함께 정리하며 각 방법이 가지는 장점과 한계를 설명했습니다. 

이러한 점에서 본 논문은 추천 시스템 연구 및 공부를 시작할 때 방향을 잡는데에 있어 도움이 될 수 있는 좋은 논문이라고 생각합니다. 


![CBCFTable](/assets/images/CBCFTable.png) 


<br>

    개인 공부 기록용 블로그입니다.
    오류나 틀린 부분이 있을 경우 언제든지 글이나 메일로 지적해주시면 감사하겠습니다! ☺

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}