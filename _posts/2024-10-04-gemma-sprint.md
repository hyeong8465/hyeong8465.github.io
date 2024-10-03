---
title: "[Projects] Gemma Sprint - Fake News Detector"
categories: [Projects]
tags: ["#GemmaSprint"]
pin: false
layout: post
toc: true
---

# Introduction
Google Machine Learning Bootcamp의 마지막 수료 조건인 Gemma sprint에 대한 회고를 작성하고자 한다.  
초기엔 LLM과 RAG를 활용하여 개인 맞춤혐 AI agent를 만들고자 하였다.  
그러나 다른 일정과 프로젝트 때문에 미루고 미루다가 마지막 주에 벼락치기를 하느라 주제를 파인 튜닝으로 바꾸었다.  
적당한 데이터를 찾다가 Kaggle의 가짜 뉴스 데이터를 분류하는 데이터를 찾았고 해당 데이터로 Gemma2를 파인 튜닝하였다.

### <a href="https://colab.research.google.com/drive/1mX0ZkiEPzw6NQvsH4ahWDzSzbEw_k3sO#scrollTo=SvBsoJlOioMZ" target="_blank" rel="noopener noreferrer"> Code </a>

# Data Load
파인 튜닝할 데이터는 Kaggle에서 <a href="https://www.kaggle.com/datasets/saurabhshahane/fake-news-classification/data" target="_blank" rel="noopener noreferrer"> Fake News Classification </a> 데이터를 사용하였다.  
title(제목), text(본문), 가짜 뉴스임을 알려주는 label(0/1)로 구성되어 있다.

# Procedure
1. 허깅 페이스에서 Gemma2 모델 로드
2. 데이터 전처리(결측치 제거)
3. 사전 학습 모델의 성능 평가
4. 파인 튜닝 모델의 성능 평가

# Conclusion
- Pre-fine-tuning accuracy: 0.5030
- Post-fine-tuning accuracy: 0.4975
- Improvement: -0.0055

## Problems
1. 사전 학습 모델의 성능이 썩 좋지 않다.  
데이터의 label의 분포가 고르기 때문에 하나만 찍어도 적어도 50%의 성능은 보장한다.값비싼 코스트를 들여서 사전 학습 시키고 인퍼런스까지 진행했지만, 찍는 것과 비슷한 성능인 것이다.  
2. 파인 튜닝 모델의 성능은 더 좋지 않다.  
데이터를 추가로 학습시켜 파인 튜닝을 적용했지만 성능은 오히려 감소했다.

## Why?
왜 이런 결과가 나타났는지 생각해봤다.
1. Only Decoder 구조  
LLM이면 자연어 이해, 생성에 모두 만능일 것이라고 막연히 생각하여 큰 고민없이 주제를 정하였는데, 애초에 모델과 맞지 않는 Task였던 것 같다.  
Gemma의 경우 디코더만 있기 때문에 언어 생성에 강점이 있지만 전체 문맥을 파악하여 가짜 뉴스인지 아닌지 판단하는 것에 사용하는 것은 잘못된 선택이다.
2. Parameters, Data  
코랩의 A100을 활용하였음에도 OOM 문제로 모델의 사이즈를 점차 줄였고, `model.half()`로 정밀도를 줄였다.
또한 급하게 벼락치기를 하느라, 연산 시간을 줄이기 위하여 약 7만 건의 데이터에서 1만 건만 사용하였고, epoch도 3번만 적용하였다.

결론적으로, 만능 같은 LLM이라도 해당 모델이 맞는 Task를 조사해야 하고, 시간적 여유를 두고 다양한 테스트를 진행하는 것이 중요하다.
아, 리소스 문제는 어쩔 수 없지만,,



