---
title: "[Co-Week Academy] Day1"
categories: [Activities, Co-Week_Academy]
tags:
  - 인공지능
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

저는 **미래자동차 컨소시엄** 소속으로 참가하였습니다.

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
    - 입력값들과 가중치의 선형 결합을 비선형 활성화 함수를 통해 출력으로 전달하는 퍼셉트론 구조를 나타냅니다. 
    ![퍼셉트론 구조](../assets/images/perceptron.png)
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
![코위크 셀카](../assets/images/coweek_photo1.jpg)
요로코롬 마스코트 인형이랑 사진도 찍었었당
![알펜시아 저녁노을](../assets/images/coweek_photo2.jpg)
친구랑 돌아다니면서 찍은 하늘인데 외국 느낌나고 예쁜듯??


<br>

    개인 공부 기록용 블로그입니다.
    오류나 틀린 부분이 있을 경우 언제든지 글이나 메일로 지적해주시면 감사하겠습니다! ☺

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}