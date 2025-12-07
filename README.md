<!--Heading-->
## Multi-Wafer Map Classification Enhancement via Adaptive Dynamic Class Weighting
**4학년 2학기** personal Project


## Summary
**동적 가중치 적용을 통한 멀티 웨이퍼 맵 분류 성능 향상**

반도체 제조 공정에서는 미세한 결함이 수율에 큰 영향을 미치며, 이를 분석하기 위한 웨이퍼맵 데이터는 클래스 불균형 문제가 두드러진다. 본 연구는 이러한 문제를 해결하기 위해 경량 CNN 모델에 동적 가중치 조정 기법을 결합한 분류 방식을 제안한다. 모델 복잡도를 크게 높이지 않으면서도, Epoch마다 Recall 기반 가중치를 재계산해 적용함으로써 소수 클래스의 분류 성능을 기존 방법론 대비 효과적으로 개선하였다.


## Method
<img width="1280" height="720" alt="11 18 meeting" src="https://github.com/user-attachments/assets/7684411b-3203-42c9-af43-bca59a917f40" />

**1. 다중 웨이퍼맵 데이터 기반 CNN 모델 학습**

MixedWM38 데이터셋을 활용하여 파인튜닝한 0.3M 파라미터 규모의 경량화된 CNN 기반 분류 모델을 학습한다.  

(MixedWM38: 반도체 제조 공정 중 발생하는 다양한 결함 패턴을 시각적으로 표현한 웨이퍼맵 데이터로, 9개의 Single Defeat, 13개의 Two-Mixed Defeat, 12개의 Three-Mixed Defeat, 4개의 Four-Mixed Defeat로 총 38개의 클래스로 구성되어 있다.)


**2. Confusion Matrix 기반 동적 가중치 산출**

Epoch이 끝날 때마다 Confusion Matrix 기반으로 Recall, Accuracy, F1 Score를 계산하고, 각 지표의 역수(1 / metric)를 활용해 클래스별 오분류 가중치를 산출한다. 이렇게 도출된 동적 가중치를 다음 Epoch의 Loss에 적용하여 불균형 클래스가 충분히 학습될 수 있도록 한다.


**3. 동적 가중치 적용 및 Early Stopping 기반 성능 평가**

업데이트된 클래스 가중치를 적용한 뒤 모델을 다시 학습하며, Validation Loss를 기준으로 Early Stopping을 수행한다. 성능이 개선되면 다음 Epoch로 반복하며, 성능 향상이 일정 기간 지속되지 않을 경우 학습을 종료한다.

## Result
#### Metric별 동적 가중치 Model에서의 성능
<!--Table-->
| Metric | Test Accuracy | Best Val      |
|------------------------|---------------|--------------------------|
| Recall                    | **97.71%**         | 97.80%          |
| F1 score                    | 97.70%         | 97.83%        |
| Accuracy                   | 97.45%         | 97.47%        |


Metric별 동적 가중치 모델의 효과를 확인하기 위해 Recall, F1 Score, Accuracy 세 가지 지표를 기준으로 각각 동적 가중치를 산출한 세 가지 모델을 학습하였다. 모든 모델은 동일한 환경에서 동일한 데이터셋을 사용해 학습·검증을 진행했으며, Epoch마다 산출된 지표의 역수를 기반으로 클래스별 가중치를 재조정하는 방식으로 성능을 비교하였다.

실험 결과, 세 가지 Metric 중 **Recall** 기반 모델이 **Test Accuracy 97.71%** 로 가장 우수한 성능을 보였다.

#### 유사 Model간의 성능 비교

| Models        | Test Accuracy | Best Val |
|---------------|----------------|-----------------|
| Baseline          |  97.02%  |97.22%|         
| VIT               | **99.12%**| 99.23%|
|클래스별 가중치 조절 Model |97.28%|97.57%|
|동적 가중치 조절 Model |97.71%|97.80%|

본 방법론의 실효성을 확인하기 위해 유사한 모델들과의 비교실험을 진행하였다. 다음은 유사 모델에 대한 요약 설명이다.

**Baseline Model**은 어떠한 가중치 조정 기법도 적용하지 않은 경량 CNN 구조로, 본 연구에서 사용하는 기본 성능 기준점으로 활용하였다.
**VIT**은 웨이퍼맵 분류에서 우수한 성능을 보이는 것으로 알려져 있으나, 약 6.47M 파라미터 규모의 대형 모델에 속해 경량 모델 대비 연산 비용이 크다.
**클래스별 가중치 조절 Model**은 데이터 불균형 문제를 해결하기 위해 사전에 클래스 가중치를 고정 적용하는 전통적인 방식으로, 관련 연구에서 가장 널리 사용되는 접근법이다.
**동적 가중치 조절 Model**은 본 연구에서 검증하고자 하는 모델이며, 1번 실험 결과를 적용하여 Metrix를 Recall로 적용하였다.

모델 간 성능을 비교한 결과, VIT이 Test Accuracy 99.12%로 가장 우수한 성능을 기록하였으며, 그다음으로 동적 가중치 조절 모델이 **97.71%** 의 정확도로 두 번째로 높은 성능을 달성하였다.

## Conclusion
본 연구에서는 동적 가중치 조절 기법을 적용하여 클래스 불균형 문제를 개선하고, 이를 통해 다중 웨이퍼맵 분류 성능이 향상됨을 실증적으로 확인하였다. 또한 **경량화된 CNN 모델을 기반으로 비교적 적은 파라미터 수로도 안정적인 성능을 달성**함으로써, 실사용 가능성 측면에서도 의미 있는 결과를 제시하였다.

다만 본 연구에서 사용한 MixedWM38 데이터셋은 클래스 간 불균형 정도가 심하지 않아, 제안 기법의 성능 향상이 크게 나타나지 못한 한계가 있다. 향후에는 클래스 불균형이 두드러지는 다양한 데이터셋에 본 방법론을 적용해 적응 가능성을 검증함으로써, 제안 접근법의 일반성과 확장성을 보다 심도있게 확장해나갈 것이다


## Reference
-----
**Dataset** : https://www.kaggle.com/datasets/co1d7era/mixedtype-wafer-defect-datasets
**bert-base-multilingual-uncased** : https://huggingface.co/nlptown/bert-base-multilingual-uncased-sentiment
**DeepCoNN** : https://github.com/jaejunchoe/DeepCoNN


