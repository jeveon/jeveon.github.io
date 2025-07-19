---
layout: post
title: "[논문 리뷰] One-shot active learning for image segmentation via contrastive learning and diversity-based sampling"
categories: Paper
excerpt_image: /assets/images/Con-2025-07-19/Untitled-20250719170953092.webp
tags: [One-shot Active Learning, Image Segmentation, Contrastive Learning]
---

## 1. Introduction

최근 CNN이 컴퓨터 비전 분야에서 널리 활용, 하지만 CNN은 대량의 라벨링 된 샘플이 필요하다. 하지만 의료 영상이나 원격 탐사 영상처럼 고도의 지식이 필요한 경우 라벨링에 많은 시간과 자원이 소모된다. 따라서 이를 위해 Active learning을 통해서 unlabeled data pool에서 효율적으로 데이터를 선택해 성능을 유지하며, labeling 비용을 줄인다.

전통적인 AL 방식은 일반적으로 각 반복마다 일부의 샘플을 선택하고 labeling 한 후, 전체 labeling 된 sample을 이용해서 모델을 학습한다. 이러한 방식은 전문가와 상호작용이 더 많이 필요하여, 더 많은 자원이 요구된다.

이를 해결하기 위해서 One-Shot Active Learning이 제안되었다. OSAL은 raw image data만을 사용해서, unsupervised learning 이나 self-supervised learning으로 학습을 한다. 따라서 OSAL의 성능은 추출된 feature에 의존적이다.

대부분의 OSAL 방식은 auto-encoder를 사용하여 feature를 추출하지만 이 방식은 표현 능력에 한계가 있다. 또한 OSAL 방식은 representative query strategy를 사용하는데 이는 sampling redundancy(중복)이 발생할 수 있다.

따라서 이러한 문제를 해결하기 위해서 본 논문은 contrastive self-supervised learning을 통해서 feature extraction을 진행하고 deep clustering과 farthest point sampling을 기반으로 새로운 query strategy를 사용하는 framework를 제안한다.


## 2. Method

### 2.1 Problem statement

unlabeled sample $x_u$로 구성된 $n$개의 sample pool을 $X_U$라고 가정하자. 이때 AL은 효율적인 query strategy($Q$)를 선정해서 label효율성이 가장 높은 $m$개의 sample로 구성된 $X_C \subseteq X_U$ 를 선택한다. 이후 $X_C$는 전문가 $O$에 의해 labeling되어 $X_L$이 생성된다.

주석 비율 ($r = \frac{|X_L|}{|X_U|} = \frac{m}{n}$)이 $r = 100\%$ 일 때 모델이 최고 성능에 도달한다.

### 2.2 Contrastive Self-supervised Feature Learning

![a](/assets/images/Con-2025-07-19/dd.png)

본 논문에서 제안하는 framework는 feature space에서 sample 선택이 이루어지며 이때 사용되는 feature는 self-superives contrastive learning 방식을 통해서 추출한다. 비슷한 sample은 가깝게 다른 sample은 멀게 하도록 하는 것을 목적으로 하며 이를 위해서 4번식과 같은 loss function이 사용된다. 여기서 $x$와 $x^+$는 positive pair, $x$와 $x^-$는 negative pair, $f$는 encoder이다. $L_{NCE}$를 확장해 positive pair는 동일한 이미지에도 data augmentation을 적용해서 사용하고 negative pair는 서로 다른 이미지를 sampling해 구성되며, 이를 통해서 5번 식이 만들어진다.

여기에 더불어 contrastive 표현의 성능을 높이기 위해서 Momentum Contrastive learning을 추가로 적용한다. 이 방식은 key encoder를 도입해서 positive sample과 negative sample을 생성하고 6번 식의 loss를 최소화 한다.

또한 Positive sample에는 data augmentation이 적용이 없는데 label이 없는 환경에서는 augmentation이 제대로 적용되었는지 검증이 불가능하다 따라서 6번 식을 기반으로 새로운 validation standard를 설계하였다. 

positive pair와 negative pair의 구별을 어렵게 만들기 위해서 Augmentation 집합인 $t \sim \mathcal{J}$ 에서 7번 식과 같은 augmentation 방식을 찾으려고 하였다. 이를 통해서 학습된 feature에 augmentation에 더 invariant하도록 만든다.

![a](/assets/images/Con-2025-07-19/Untitled-20250719170953092.webp)
### 2.3 Diversity-based query strategy

feature space에서 pre-clustering을 K-mean 알고리즘을 사용해서 진행한다. sample간의 거리는 Euclidean distance로 측정을 한다. 또한 기존의 K-mean은 local optimum에 빠지기 쉽기 때문에 K-means ++ 초기화 기법을 사용해서 초기 중심점을 가능한 멀리 떨어지도록 선택한다. 마지막으로 K-mean 기반 방식은 클러스터 개수를 사전에 지정해야하는데 이를 Silhouette Coefficient를 활용해서 최적의 클러스트 개수를 정하였다.

이렇게 선택된 cluster에서 최적의 sample을 선택한다. 선택하는 방식은 각 cluster에서 Farthest Point Sampling 알고리즘을 사용해서 $δ$ 개의 sample을 선택한다.

![a](/assets/images/Con-2025-07-19/Untitled-20250719171017157.webp)


## 3. Experiment

Skin Lesion Segmentation, Remote Sensing Image Segmentation, Chest X-ray Segmentation을 데이터로 사용했으며,  비교 모델은 Passive Learning(random sampling), ITAL (Iterative Active Learning): Bayesian, Core-set, OSAL (One-Shot Active Learning): K-median, RA을 사용함.

데이터 그래프에 표시되는 상한선과 하한선은 모든 데이터가 라벨 되었을 때의 성능을 의미한다. 아래는 각 데이터 셋에 대한 실험 결과이다.

![a](/assets/images/Con-2025-07-19/Untitled-20250719171319886.webp)

![a](/assets/images/Con-2025-07-19/Untitled-20250719171337565.webp)

![a](/assets/images/Con-2025-07-19/Untitled-20250719171350712.webp)

![a](/assets/images/Con-2025-07-19/Untitled-20250719171412366.webp)

![a](/assets/images/Con-2025-07-19/Untitled-20250719171422230.webp)

마지막으로 diversity-based query strategy의 시각화 결과이다.

![a](/assets/images/Con-2025-07-19/Untitled-20250719171617679.webp)
