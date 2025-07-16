---
title: "[Co-Week Academy] Day1"
categories: [activities, co-week-academy]
tags:
  - AI
layout: single
toc : true
toc_sticky: true
comments: true
---

제4회 CO-WEEK Academy 1일차 강의 내용을 정리한 학습 기록입니다.
{: .notice--info}


## 0. CO-WEEK 아카데미란?
**CO-WEEK ACADEMY**는 **융합형 인재 양성**과 **미래 기술 분야 이해 확대**를 목표로 하여 전국 18개 첨단분야 혁신융합대학이 매년 1회 개최하는 팝업 캠퍼스 형태의 교육 프로그램입니다. 

> **행사명** | 제 4회 CO-WEEK ACADEMY 
> **기간** | 2025년 6월 30일(월) ~ 7월 4일(금), 총 5일간
> **장소** | 강원도 평창 알펜시아 리조트
> **주최** | 교육부, 한국연구재단, 첨단분야 혁신융합대학 사업단협의회

저는 <span style="color:blue;">**미래자동차 컨소시엄**</span>  소속으로 참가하였습니다.

## 1. 인공지능 기반 영상 신호 처리 강의 내용
### 🐾 AI / ML / DL 차이

| 구분 | 특징 | 예시 |
|------|------|------|
| **AI** | 인간 지능을 모사하는 기술 분야 | 자율주행, 추천 시스템 등 |
| **ML** | AI의 하위 개념으로, 데이터를 기반으로 학습 | K-means, 회귀 분석 |
| **DL** | ML의 하위 개념으로, 심층 신경망 (DNN)을 이용한 학습 | CNN, RNN, Transformer |

**머신러닝 학습 방식**
- 지도 학습 (Supervised Learning)
    - 정답(label)이 있는 데이터 기반 학습
    - 입력 -> 정답 쌍을 가지고 모델이 패턴을 익힘
    - ex. 선형 회귀, 로지스틱 회귀, SVM, CNN, KNN
- 비지도 학습 (Unsupervised Learning)
    - 정답(label)이 없는 데이터로부터 패턴을 찾음
    - clustering이나 차원 축소 등에 활용
    - ex. K-means, PCA, DBSCAN
- 강화 학습 (Reinforcement Learning)
    - agent가 환경과 상호작용하면서 **보상을 최대화 하는 방향**으로 학습
    - 정답 X, 시행착오 O
    - ex.  DQN

### 🐾 Deep Neural Network (DNN)
- **Perceptron**: DNN의 기본 단위 
    - 아래는 단일 퍼셉트론의 구조를 나타낸 그림입니다.
    - 입력값들과 가중치의 선형 결합을 비선형 활성화 함수를 통해 출력으로 전달합니다. 

    ![퍼셉트론 구조](data:image/png;base64,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)
- **CNN (Convolutional Neural Network)**: 이미지 분류에 특화된 신경망 (Yann LeCun, 1998)
- **Transformer**: Attention 메커니즘 기반 모델 (자연어 처리에서 시작, 현재는 비전에도 적용)


### 🐾 디지털 영상 처리 파이프라인
1. **Image Acquisition**: 광원을 통해 객체에서 반사된 빛을 센서로 수집  
2. **Image Processing**: 연속 아날로그 신호를 디지털로 샘플링 및 양자화 → 이미지 압축  
3. **Rendering**: 압축된 비트스트림을 다시 해석해 영상 복원  
4. **Storage / Delivery**: 저장 혹은 전송

※ 샘플링: 연속 신호(아날로그)를 픽셀 단위로 표현하는 것
※ 양자화: 픽셀의 밝기 등 값을 일정 단계로 구분하는 것


### 🐾 딥러닝 기반 영상 처리 기술
- **Image Classification**: 단일 객체의 분류
- **Object Detection**: 이미지 내 여러 객체 탐지 및 위치 파악 (예: YOLO)
- **Image Segmentation**: 픽셀 단위 객체 분할

**응용 분야 예시**
- 교통 신호 인식
- 보행자·차량 탐지
- 의료 영상 분석

- **Style Transfer**: 예술 스타일을 다른 이미지에 적용
- **3D Pose Estimation**: 3D 위치 및 자세 추정
- **Super Resolution**: 저해상도 이미지를 고해상도로 변환


### 🐾 최근 AI 트랜드
- **Multimodal AI**: 텍스트, 이미지, 음성 등 다양한 데이터를 동시에 처리하는 모델  
- **Efficient AI (On-device AI)**: 엣지 디바이스에서 AI 모델을 직접 구동  
- **Generative AI**: 존재하지 않았던 콘텐츠(이미지, 텍스트, 오디오 등)를 생성 (예: ChatGPT, Claude, LLaMA 등)

_Master your skills. Embrace AI. Think beyond boundaries._


## 2. 활동 사진
![코위크 셀카](/assets/images/coweek_photo1.jpg)
요로코롬 마스코트 인형이랑 사진도 찍었었당
![알펜시아 저녁노을](/assets/images/coweek_photo2.jpg)
친구랑 돌아다니면서 찍은 하늘.. 
외국 느낌나고 몬가몬가 감성있었다.. 예뽀..


<br>

    개인 공부 기록용 블로그입니다.
    오류나 틀린 부분이 있을 경우 언제든지 글이나 메일로 지적해주시면 감사하겠습니다! ☺

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}