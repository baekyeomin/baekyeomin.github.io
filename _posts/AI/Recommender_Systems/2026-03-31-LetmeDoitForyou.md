---
title: "[Paper Review] #05 Let Me Do It For You: Towards LLM Empowered Recommendation via Tool Learning"
categories: [AI, Recommender Systems]
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

## ToolRec

### 1. 연구 문제

기존 추천시스템은 사용자의 과거 상호작용 데이터를 바탕으로 선호를 예측하고 item을 추천한다.  
하지만 이 방식에는 한계가 있다.

- 사용자의 세밀한 선호를 충분히 포착하기 어렵다.
- 기존 추천시스템은 특정 데이터에만 의존하는 narrow expert에 가깝다.
- LLM을 추천에 직접 사용하면 hallucination 문제가 발생할 수 있다.
- LLM을 단순 보조 정보 생성기로 사용하면 semantic space와 behavior space가 어긋날 수 있다.
- LLM을 controller로 사용하는 기존 방식은 control strategy가 단순하거나 사용자 개입이 많이 필요하다.

<span style="color: blue">ToolRec은 LLM을 추천 결과를 직접 생성하는 모델이 아니라, 추천 과정을 조율하는 surrogate user이자 controller로 활용한다.</span>

즉, LLM이 실제 사용자의 입장에서 현재 추천 목록을 보고, 부족한 attribute를 판단한 뒤, 적절한 tool을 호출하여 추천 목록을 점진적으로 개선하는 방식이다.

### 2. ToolRec의 핵심 아이디어

ToolRec은 추천 과정을 사용자의 관심사를 attribute 단위로 탐색하는 과정으로 본다.

예를 들어 영화 추천이라면 LLM은 다음과 같이 판단할 수 있다.

1. 사용자가 액션 영화를 좋아하는 것 같으므로 genre 기준으로 item을 검색한다.
2. 검색된 영화들이 사용자의 과거 영화와 release year 측면에서 다르다고 판단한다.
3. release year 기준으로 추가 item을 검색한다.
4. actor 기준으로 현재 후보 item을 다시 정렬한다.
5. 추천 목록이 충분하다고 판단하면 Finish 한다.

<span style="background-color: #fff3cd">핵심은 LLM이 한 번에 추천 결과를 내는 것이 아니라, Thought → Action → Observation 과정을 여러 round 반복하면서 추천 목록을 개선한다는 점이다.</span>

![ToolRec1](/assets/images/ToolRec1.png)

### 3. Problem Formulation

순차 추천 상황에서 사용자 $u$의 과거 상호작용 sequence가 주어진다.

$H = \{i_1, i_2, \cdots, i_{n-1}\}$

목표는 사용자가 다음에 관심을 가질 item $i_n \in I$를 예측하는 것이다.  
여기서 $I$는 전체 item pool이다.

기존 추천시스템은 사용자 $u$의 선호를 예측하여 후보 item 집합 $I_c \subseteq I$를 검색한다.  
하지만 이 과정에서 실제 target item $i_n$이 $I_c$ 안에 포함되지 않을 수 있다.

ToolRec은 LLM을 surrogate user $\hat{u}$로 설정하고, 사용자의 과거 상호작용 $H$를 바탕으로 사용자 profile을 초기화한다.

이후 각 round에서 surrogate user $\hat{u}$는 다음을 수행한다.

1. 현재 추천 목록과 사용자 history를 비교한다.
2. 부족하거나 불일치하는 attribute $a_t$를 찾는다.
3. 해당 attribute에 맞는 tool을 호출한다.
4. 새로운 후보 item 집합 $I^{a_t}$를 얻는다.
5. 추천 목록이 충분하다고 판단하면 최종 추천 목록 $I_{\hat{u}}$를 출력한다.

### 4. ToolRec의 전체 구조

ToolRec은 크게 세 가지 구성요소로 이루어진다.

- User Decision Simulation
- Attribute-oriented Tools
- Memory Strategy


![ToolRec2](/assets/images/ToolRec2.png)

### 1. User Decision Simulation

User Decision Simulation은 LLM이 surrogate user 역할을 하며, 사용자의 입장에서 추천 과정을 판단하는 모듈이다.

LLM은 다음 세 단계를 반복한다.

- Thought
  - 현재 추천 목록이 사용자 선호와 잘 맞는지 생각한다.
  - 어떤 attribute를 기준으로 추가 탐색하거나 정렬할지 결정한다.

- Action
  - Retrieval, Rank, Finish 중 하나를 선택한다.

- Observation
  - tool 실행 결과를 확인한다.
  - 반환된 item 목록을 보고 다음 행동을 결정한다.

각 step $t$에서 LLM 기반 surrogate user $\hat{u}$는 이전 observation $o_{t-1}$을 바탕으로 thought $g_t$와 action $A_t$를 생성한다.

$\pi(g_t, A_t \mid c_t)$

여기서 context $c_t$는 이전까지의 observation, thought, action을 모두 포함한다.

$c_t = (o_0, g_1, A_1, o_1, \cdots, g_{t-1}, A_{t-1}, o_{t-1})$

<span style="background-color: #fff3cd">즉, ToolRec은 단순히 LLM에게 “추천해줘”라고 하는 방식이 아니라, LLM이 reasoning을 통해 어떤 tool을 사용할지 단계적으로 결정하게 만든다.</span>

![ToolRec25](/assets/images/ToolRec25.png)

### 2. Attribute-oriented Tools

Attribute-oriented Tools는 특정 attribute를 기준으로 item pool을 탐색하거나 후보 item을 정렬하는 도구이다.

ToolRec에서는 두 종류의 tool을 사용한다.

#### 1) Retrieval Tools

Retrieval Tool은 특정 attribute를 기준으로 관련 item을 검색한다.

예를 들어 영화 추천에서는 다음과 같은 attribute를 사용할 수 있다.

- genre
- release year
- actor

책 추천에서는 다음과 같은 attribute를 사용할 수 있다.

- price
- sales rank

Yelp 데이터에서는 다음과 같은 attribute를 사용할 수 있다.

- category
- city
- stars

Tool action은 다음과 같이 표현된다.

$T_{type}[a_t, K]$

여기서 $T_{type}$은 Retrieval 또는 Rank이고, $a_t$는 선택된 attribute, $K$는 반환할 item 개수이다.

예를 들어 다음 action은 genre 기준으로 item $5$개를 검색한다는 뜻이다.

$Retrieval[genre, 5]$

#### 2) Rank Tools

Rank Tool은 현재 후보 item들을 특정 attribute 기준으로 다시 정렬한다.

예를 들어 LLM이 “현재 추천 영화들은 배우 측면에서 사용자의 과거 선호와 잘 맞지 않는다”고 판단하면, actor 기준으로 후보 item을 다시 정렬할 수 있다.

예시는 다음과 같다.

$Rank[actor, 4]$

이는 actor attribute를 기준으로 후보 item을 정렬하고, 상위 $4$개 item을 반환한다는 뜻이다.

<span style="color: blue">Retrieval Tool은 새로운 후보 item을 가져오는 역할이고, Rank Tool은 이미 있는 후보 item을 사용자 선호에 맞게 재정렬하는 역할이다.</span>

### 3. Retrieval Tool의 학습 구조

Retrieval Tool은 attribute별로 별도 모델을 모두 학습하는 방식이 아니라, 기존 sequential recommender의 backbone을 활용한다.

논문에서는 SASRec 기반 구조를 사용한다.


![ToolRec3](/assets/images/ToolRec3.png)

사용자의 과거 행동 sequence $H$에 대해 sequential model은 다음과 같이 user representation을 만든다.

$H = f_{seq}(H|\beta) = h^L_{|H|}$

여기서 $\beta$는 pre-training parameter이고, $h^L_{|H|}$는 마지막 layer에서 얻은 사용자 행동 representation이다.

이후 pre-trained backbone parameter $\beta$는 freeze한다.

Attribute-specific encoder는 사용자의 attribute sequence $a_u$를 입력으로 받아 attribute representation을 만든다.

$a_u = f_{attr}(a_u|\gamma) = a^L_{|a_u|}$

그 다음 frozen behavior representation $H$와 attribute representation $a_u$를 결합한다.

$u = Dense(a_u \oplus H, \theta)$

여기서 $\gamma$와 $\theta$는 fine-tuning 단계에서 학습되는 parameter이다.

학습에는 BPR loss를 사용한다.

$L_{BPR} = - \sum_{(H,v)\in O^+, (H,w)\in O^-} \log \sigma(\phi(u,v) - \phi(u,w))$

여기서 $O^+$는 positive sample, $O^-$는 negative sample이다.  
$\phi(\cdot)$는 inner product layer이고, $\sigma(\cdot)$는 sigmoid function이다.

<span style="background-color: #fff3cd">이 구조의 핵심은 기존 sequential recommendation 능력은 유지하면서, 특정 attribute에 민감한 retrieval tool을 효율적으로 만드는 것이다.</span>

### 4. Memory Strategy

Memory Strategy는 tool이 반환한 item을 검증하고 저장하는 역할을 한다.

LLM은 item name이나 ID를 잘못 생성할 수 있기 때문에, 추천 과정에서 반환된 item이 실제 item pool에 존재하는지 확인해야 한다.

Memory Strategy는 다음을 수행한다.

1. 전체 item pool directory로 초기화된다.
2. tool이 반환한 candidate item이 실제 item pool에 존재하는지 확인한다.
3. 존재하지 않는 item이 있으면 tool을 다시 실행하게 한다.
4. 검증된 item은 어떤 tool과 attribute로 얻어진 것인지 함께 저장한다.
5. 이후 round에서 LLM이 이전 결과를 참고할 수 있게 한다.

<span style="background-color: #fff3cd">Memory Strategy는 LLM hallucination을 줄이고, 여러 tool 호출 결과를 체계적으로 관리하기 위한 장치이다.</span>

---

## 실험

### 1. Experimental Settings

ToolRec의 성능을 평가하기 위해 세 가지 real-world dataset을 사용했다.

- ML-1M
  - MovieLens-1M 기반 영화 추천 데이터셋
  - attribute: genre, release year

- Amazon-Book
  - Amazon Book category 데이터셋
  - attribute: price, sales rank

- Yelp2018
  - Yelp Challenge 기반 local business 데이터셋
  - attribute: category, city, stars

![ToolRec4](/assets/images/ToolRec4.png)

평가는 sequential recommendation setting에서 진행되었다.

데이터는 timestamp 기준으로 정렬하여 train, validation, test set으로 나누었다.  
평가 방식은 leave-one-out strategy를 사용했다.

주요 평가 지표는 다음과 같다.

- $Recall@10$
- $NDCG@10$

### 2. Baselines

ToolRec은 다음 방법들과 비교되었다.

#### 1) Conventional Sequential Recommenders

- SASRec
  - self-attention 기반 sequential recommender

- BERT4Rec
  - bidirectional self-attention 기반 sequential recommender

#### 2) LLMs as RSs

- P5
  - 추천 문제를 text-to-text generation task로 통합한 generative model

#### 3) LLM-enhanced RSs

- $SASRec_{BERT}$
  - SASRec에 BERT semantic representation을 추가한 모델

- $BERT4Rec_{BERT}$
  - BERT4Rec에 BERT semantic representation을 추가한 모델

#### 4) LLM-controlled RSs

- Chat-REC
  - LLM이 backend recommender 사용 여부를 결정하고 결과를 refine하는 방식

- LLMRank
  - LLM을 ranking model로 사용하는 방식

#### 5) Proposed Method

- ToolRec
  - SASRec backbone 기반 attribute-oriented retrieval tool 사용

- $ToolRec_B$
  - BERT4Rec backbone 기반 attribute-oriented retrieval tool 사용

### 3. Performance Comparison

#### Motivation

ToolRec은 기존 추천시스템 및 기존 LLM 기반 추천 방법보다 좋은 성능을 보이는가?

#### Setting

세 데이터셋에서 ToolRec과 baseline들을 비교했다.

- ML-1M
- Amazon-Book
- Yelp2018

평가 지표는 $Recall@10$과 $NDCG@10$이다.

![ToolRec5](/assets/images/ToolRec5.png)

#### Results

- ToolRec은 ML-1M과 Amazon-Book에서 대부분의 baseline보다 높은 성능을 보였다.
- 특히 ML-1M에서는 $NDCG@10$ 기준으로 가장 좋은 성능을 보였다.
- Amazon-Book에서도 ToolRec이 가장 좋은 성능을 보였다.
- ToolRec은 SASRec과 BERT4Rec 같은 backbone 모델보다 성능이 향상되었다.

<span style="background-color: #fff3cd">이는 LLM이 attribute 단위로 item pool을 탐색하고, tool을 활용해 추천 과정을 조율하는 방식이 효과적임을 보여준다.</span>

하지만 Yelp2018에서는 ToolRec의 성능이 좋지 않았다.

그 이유는 LLM이 영화나 책처럼 웹상에 정보가 많은 domain에는 강하지만, local business처럼 정보가 제한적인 domain에는 약하기 때문이다.

<span style="background-color: #fff3cd">즉, ToolRec은 LLM의 open-world knowledge가 풍부하게 작동할 수 있는 domain에서 특히 효과적이다.</span>

### 4. Effectiveness of User Decision Simulation

#### Motivation

ToolRec에서 user decision simulation, CoT, tool learning이 실제로 성능 향상에 기여하는가?

#### Setting

ToolRec의 변형 모델을 만들어 비교했다.

- w/ single
  - CoT와 tool learning을 제거하고, LLM이 SASRec 후보 item만 rank하도록 함

- w/ multi
  - SASRec 후보 item과 attribute-oriented retrieval tool의 후보 item을 함께 사용하여 LLM이 rank하도록 함

- w/ Plan
  - CoT를 사용하지 않고, LLM이 tool-calling 계획을 한 번에 생성한 뒤 그대로 실행하도록 함

- ToolRec
  - CoT 기반으로 매 round마다 Thought → Action → Observation을 반복하는 원래 방식

![ToolRec6](/assets/images/ToolRec6.png)

#### Results

- w/ single은 성능이 낮았다.
- 이는 LLM의 zero-shot ranking만으로는 추천 성능을 충분히 개선하기 어렵다는 것을 의미한다.
- w/ multi는 w/ single보다 좋은 성능을 보였다.
- 이는 attribute-oriented retrieval tool이 추가 정보를 제공하기 때문이다.
- w/ Plan보다 ToolRec이 더 좋은 성능을 보였다.

<span style="background-color: #fff3cd">즉, 한 번에 계획을 세우고 실행하는 방식보다, 매 round마다 관찰 결과를 보고 다음 행동을 결정하는 iterative decision simulation이 더 효과적이다.</span>

### 5. Analysis of Round Termination

#### Motivation

ToolRec은 몇 번의 round 안에서 추천 과정을 종료하는가?

#### Setting

ToolRec이 추천 과정을 종료하는 round 수를 분석했다.

- His Round: 전체 사용자에 대한 종료 round 분포
- Hit Round: 추천 목록에 target item이 포함된 경우의 종료 round 분포
- Hit/His Ratio: 해당 round에서의 성공 비율

![ToolRec7](/assets/images/ToolRec7.png)

#### Results

- 대부분의 추천 과정은 $3$~$4$ round 안에서 종료되었다.
- 이는 LLM 기반 surrogate user가 몇 번의 반복만으로 사용자의 선호가 충분히 반영되었는지 판단할 수 있음을 의미한다.
- 일부 사용자는 더 많은 round가 필요했다.
- 이는 사용자의 관심사가 다양하거나 세밀한 경우, 더 많은 attribute 탐색이 필요하기 때문이다.

### 6. Efficiency and Scalability of Retrieval Tools

#### Motivation

Attribute-oriented retrieval tool은 효율적이고 확장 가능한가?

#### Setting

다음 모델들의 trainable parameter 수와 FLOPs를 비교했다.

- SASRec
- $SASRec_{BERT}$
- full + $a_1$
- frozen + $a_1$

여기서 full + $a_1$은 attribute-specific model 전체를 fine-tuning하는 방식이고, frozen + $a_1$은 backbone을 freeze한 뒤 attribute-specific encoder만 학습하는 방식이다.

![ToolRec8](/assets/images/ToolRec8.png)

#### Results

- $SASRec_{BERT}$는 BERT embedding을 사용하기 때문에 모델 크기가 크다.
- full + $a_1$은 사실상 attribute마다 새로운 모델을 학습하는 것과 비슷하다.
- attribute 수가 많아질수록 full fine-tuning은 비효율적이다.
- frozen + $a_1$은 FLOPs는 비슷하지만 trainable parameter 수가 훨씬 적다.

<span style="background-color: #fff3cd">따라서 ToolRec의 frozen backbone + attribute-specific encoder 구조는 attribute가 늘어나도 비교적 효율적으로 확장될 수 있다.</span>

---

## 추가 실험

### 1. Surprises and Limitations

#### Motivation

ToolRec은 LLM을 활용하기 때문에 기존 추천시스템 평가 방식으로는 실패처럼 보이지만, 실제로는 의미 있는 추천일 수 있는 경우가 발생한다.

#### Setting

ToolRec은 Memory Strategy를 사용하여 추천된 item이 dataset directory에 존재하는지 확인한다.  
만약 LLM이 실제 세계에는 존재하지만 dataset에는 없는 item을 추천하면, 기존 평가 기준에서는 failure로 처리된다.

![ToolRec9](/assets/images/ToolRec9.png)

#### Result

논문에서는 LLM이 dataset 안의 item을 tool로 찾지 않고, 직접 “인기 있고 평가가 좋은 영화”를 추천한 사례를 보여준다.

이 영화들은 실제 세계에는 존재하지만, ML-1M dataset 안에 없을 수 있다.  
따라서 자동 평가에서는 실패로 처리된다.

<span style="background-color: #fff3cd">하지만 실제 사용자 관점에서는 이것이 반드시 나쁜 추천이라고 보기 어렵다.</span>

즉, LLM 기반 추천에서는 기존 offline evaluation만으로는 추천 품질을 완전히 평가하기 어렵다는 문제가 있다.

### 2. Influence of LLM Selection

#### Motivation

ToolRec의 성능은 어떤 LLM을 사용하느냐에 따라 달라지는가?

#### Setting

논문에서는 base LLM을 바꾸어 비교했다.

- ChatGPT
- Vicuna
- PaLM

같은 prompt template을 사용하여 추천 성능을 비교했다.

![ToolRec10](/assets/images/ToolRec10.png)

#### Results

- Vicuna와 PaLM은 task instruction을 제대로 따르지 못하는 경우가 있었다.
- tool usage를 오해하거나, 사용자 선호를 잘못 해석하는 문제가 발생했다.
- ChatGPT가 더 안정적으로 reasoning하고 tool을 사용할 수 있었다.
- ML-1M과 Amazon-Book에서는 ChatGPT가 더 좋은 성능을 보였다.
- Yelp2018에서는 모든 LLM이 SASRec보다 낮은 성능을 보였다.

<span style="background-color: #fff3cd">즉, ToolRec의 성능은 LLM의 reasoning 능력과 domain knowledge에 크게 의존한다.</span>

### 3. Domain Knowledge의 영향

ToolRec은 LLM의 open-world knowledge를 활용한다.

따라서 다음과 같은 domain에서는 효과적이다.

- 영화
- 책
- 대중적으로 많이 알려진 item

반면 다음과 같은 domain에서는 성능이 제한될 수 있다.

- 지역 기반 business
- niche item
- 웹상 정보가 부족한 item
- item 이름이나 ID만 있고 의미 정보가 부족한 데이터셋

<span style="color: blue">ToolRec은 의미 정보가 풍부한 domain에서는 강하지만, LLM이 잘 모르는 domain에서는 오히려 잘못된 tool decision을 할 위험이 있다.</span>

---

## 결론

### 1. 공헌

ToolRec의 주요 공헌은 다음과 같다.

1. <span style="color: blue">LLM을 추천 결과 생성기가 아니라, 추천 과정을 조율하는 surrogate user이자 controller로 활용했다.</span>

2. 추천 과정을 attribute 단위의 item pool 탐색 과정으로 정의했다.

3. LLM이 Thought → Action → Observation 과정을 반복하며, user preference와 현재 추천 목록의 불일치를 찾아낸다.

4. Attribute-oriented tool을 제안했다.

- Retrieval Tool
  - 특정 attribute를 기준으로 item을 검색한다.

- Rank Tool
  - 특정 attribute를 기준으로 후보 item을 재정렬한다.

5. Memory Strategy를 통해 item hallucination 문제를 완화했다.

6. ToolRec이 ML-1M과 Amazon-Book 같은 semantic knowledge가 풍부한 dataset에서 기존 추천시스템보다 좋은 성능을 보일 수 있음을 확인했다.

### 2. 한계

ToolRec의 한계는 다음과 같다.

1. <span style="background-color: #fff3cd">LLM의 domain knowledge에 성능이 크게 의존한다.</span>

영화나 책처럼 정보가 풍부한 domain에서는 효과적이지만, Yelp2018처럼 local business 중심 데이터에서는 성능이 떨어졌다.

2. <span style="background-color: #fff3cd">LLM의 reasoning 능력에 따라 tool 사용 품질이 달라진다.</span>

Vicuna나 PaLM은 같은 prompt를 사용해도 tool usage를 잘못 이해하거나 instruction을 제대로 따르지 못하는 경우가 있었다.

3. <span style="background-color: #fff3cd">Offline evaluation이 LLM 기반 추천의 실제 유용성을 완전히 반영하지 못할 수 있다.</span>

LLM이 실제 세계에는 존재하지만 dataset에는 없는 item을 추천하면, 자동 평가는 failure로 처리한다.  
하지만 사용자 입장에서는 좋은 추천일 수도 있다.

4. Attribute-oriented tool을 설계하려면 item attribute 정보가 필요하다.

즉, 데이터셋에 attribute 정보가 부족하거나 item description이 빈약하면 ToolRec의 장점이 충분히 발휘되기 어렵다.

### 3. 최종 정리

<span style="color: blue">ToolRec은 LLM과 기존 추천시스템을 결합하여, LLM이 사용자의 입장에서 추천 과정을 조율하도록 만든 framework이다.</span>

기존 방식이 한 번에 item을 추천하는 데 집중했다면, ToolRec은 추천을 여러 round의 탐색 과정으로 본다.  
LLM은 현재 추천 목록을 보고 부족한 attribute를 판단하고, Retrieval Tool 또는 Rank Tool을 호출하여 후보 item을 개선한다.

<span style="background-color: #fff3cd">따라서 ToolRec은 LLM의 reasoning 능력과 기존 추천시스템의 item retrieval 능력을 결합한 방식이라고 볼 수 있다.</span>

하지만 ToolRec은 LLM의 domain knowledge, reasoning 능력, 그리고 dataset의 semantic richness에 영향을 크게 받는다.  
따라서 실제 적용 시에는 domain-specific knowledge 보강, RAG, LLM fine-tuning, search engine이나 database tool 추가 등이 필요할 수 있다.

<br>

    개인 공부 기록용 블로그입니다.
    오류나 틀린 부분이 있을 경우 언제든지 글이나 메일로 지적해주시면 감사하겠습니다! ☺

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}