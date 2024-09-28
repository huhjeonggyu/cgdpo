## CGDPO 알고리즘 소개

CGDPO(Computational-Graph-based Direct Policy Optimization)는 금융 분야의 dynamic consumption & portfolio choice problem을 풀기 위해 개발된 강화학습 알고리즘입니다. 물론 금융 분야 이외의 stochastic optimal control problem에도 적용될 수 있습니다.

본 알고리즘은 다음 연구자들에 의해 개발되었습니다:
* 성균관대 수학과 허정규
* 아주대 금융공학과 구형건
* 성균관대 핀테크융합전공 임병화
* 아주대 금융공학과 김현균
* 전남대 데이터사이언스대학원 정승원

궁금한 점이 있으시면 저자 중 허정규 교수에게 메일(jghuh@skku.edu)로 문의 부탁드립니다. 이 프로젝트는 현재 진행중으로 연구에 이용하신다면 다음을 인용 부탁드립니다.

Huh, J., Koo, H. K., & Lim, B. H. (2024, July). Continuous-time portfolio optimization via model-based reinforcement learning [Paper presentation]. 2024 Asia/Pacific Conference, Financial Management Association International, Seoul, South Korea.

## CGDPO 알고리즘의 특징

CGDPO 알고리즘은 REINFORCE 알고리즘의 변형으로 다음과 같은 특징이 있습니다.

1. 모델 기반 강화학습
2. 정책 기반 강화학습
3. 역전파를 이용한 정책 신경망의 직접 학습
4. 연속 시간 문제 해결을 위한 초기 상태 랜덤 샘플링
5. 오차가 높은 부분을 타개하기 위한 어댑티브 샘플링
   
REINFORCE 알고리즘과 1,2번은 동일하지만 3,4,5번에서 차별화됩니다. CGDPO에 관한 자세한 설명이 필요한 분은 다음 발표 자료를 참고하세요:
[PPT(24.09)](https://drive.google.com/file/d/1Oy4faY7PMiEI09mdIJWxmKPeEJ2CZ5aD/view?usp=sharing)

## CGDPO와 PPO의 성능 비교

2024년 9월 기준으로 본 알고리즘은 dynamic choice & portfolio choice problem을 푸는 가장 강력한 도구입니다. 강화학습 알고리즘 중 널리 사용되는 PPO 알고리즘과 비교시 상대에러의 관한 RMSE에서 훨씬 우월합니다.

### 완전 시장
| | | 1min | 5min | 10min | 30min | 60min |
|--------|------|------|------|-------|-------|-------|
| Consumption | PPO | 1.61E+00 | 1.25E+02 | 4.49E+00 | 3.31E+00 | 1.52E+00 |
| | CGDPO | 2.41E+00 | 6.37E-01 | 3.02E-01 | 1.52E-01 | 1.22E-01 |
| Portfolio Weight | PPO | 1.34E-01 | 1.74E-01 | 1.77E-01 | 2.03E-01 | 2.22E-01 |
| | CGDPO | 3.04E-02 | 1.06E-02 | 6.54E-03 | 3.00E-02 | 1.47E-02 |

### 불완전 시장
| | | 1min | 5min | 10min | 30min | 60min |
|--------|------|-------|-------|--------|--------|--------|
| Myopic Demand | PPO | 4.94E+01 | 1.64E+02 | 3.61E+01 | 6.15E+01 | 1.32E+02
| | CGDPO | 2.19E-01 | 5.67E-02 | 2.10E-02 | 8.50E-03 | 1.21E-02
| Hedging Demand | PPO | 1.34E+01 | 1.58E+01 | 6.20E+00 | 6.25E+00 | 1.23E+01
| | CGDPO | 8.99E-01 | 8.42E-01 | 2.79E-01 | 2.54E-01 | 2.37E-01

위험 자산은 1개만 고려되었으며, 완전 시장은 Merton이 고려한 로그 수익률이 기하브라운운동을 따르는 시장을, 불완전 시장은 Kim & Omberg가 제시한 리스크 프리미엄이 OU process로 움직이는 시장을 의미합니다. 또한 완전 시장에서는 소비가 고려되나, 불완전 시장에서는 소비가 고려되지 않습니다. 이는 Merton과 Kim & Omberg의 원래의 설정을 따르는 것입니다. 완전 시장에서는 최적 소비량, 최적 포트폴리오 비율을 예측하는 신경망 2가지가 있으며, 불완전 시장에서는 최적 근시안적 수요(myopic demand), 최적 헷징 수요(hedging demand)를 예측하는 신경망 2가지가 있습니다. 근시안적 수요와 헷징 수요를 합치면 포트폴리오 비율이 됩니다. 잘 알려져 있는 것처럼, 불완전 시장에서의 dynamic choice problem은 마코위츠의 static choice problem에서 도출된 결과와 동일한 근시안적 수요에 헷징 수요를 더하는 것으로 설명됩니다. 자세한 설명이 필요한 분들은 complete 폴더와 incomplete 폴더를 들어가보시길 바랍니다.

PPO 알고리즘은 완전 시장에서도 수렴하지 않고 해답 주변을 순회하는 모습을 보여주며, 불완전 시장에서는 아예 해답과 동떨어진 답을 내놓습니다.

연구진의 견해로는 어떤 모델이든 적당한 답을 원한다면 CGDPO로 10분만 학습하더라도 충분합니다. 만약 정확한 답안을 원한다면 오래 학습할 수록 좋지만, 완전 시장에서는 1시간 이상, 불완전 시장에서는 4시간 이상의 학습을 권장합니다. 정확도를 극도로 높이면 불완전 시장에서는 '근시안적 수요'와 '헤징 수요'를 분리하여 구할 수 있습니다.

## CGDPO 알고리즘의 기여

이 알고리즘은 다음의 측면에서 기여합니다:
* 짧은 시간 안에 불완전시장을 포함한 임의 모델에서의 정책 신경망 학습을 담보
* 불완전 시장에서 '근시안적 수요'와 '헤징 수요'를 분리하여 구해낸 최초의 연구

## 비교 연구 1

* Dai, M., Dong, Y., Jia, Y., & Zhou, X. Y. (2023). Learning Merton's Strategies in an Incomplete Market: Recursive Entropy Regularization and Biased Gaussian Exploration. arXiv preprint arXiv:2312.11797.

위의 연구는 모델 프리 강화학습을 엔트로피 정규화 방법과 엮어 dynamic choice problem을 풀어내려고 하고 있습니다. 이 연구는 static model에 적용된 아래의 두 연구를 dynamic model로 알맞게 확장하는 연구로 봐야 합니다.
1. Wang, H., & Zhou, X. Y. (2020). Continuous‐time mean–variance portfolio selection: A reinforcement learning framework. Mathematical Finance, 30(4), 1273-1308
2. Dai, M., Dong, Y., & Jia, Y. (2023). Learning equilibrium mean‐variance strategy. Mathematical Finance, 33(4), 1166-1212.

혹은 연속 확률 모형을 위해 개발된 아래의 알고리즘을 dynamic choice problem에 응용하는 연구로 볼 수도 있습니다.
1. Jia, Y., & Zhou, X. Y. (2022). Policy evaluation and temporal-difference learning in continuous time and space: A martingale approach. Journal of Machine Learning Research, 23(154), 1-55.
2. Jia, Y., & Zhou, X. Y. (2022). Policy gradient and actor-critic learning in continuous time and space: Theory and algorithms. Journal of Machine Learning Research, 23(275), 1-50.

## 비교 연구 2

* Geng, S., Nassif, H., Kuang, Z., Reppen, A., & Sircar, R. (2023, July). Factor learning portfolio optimization informed by continuous-time finance models. In ICML Workshop on New Frontiers in Learning, Control, and Dynamical Systems.

위의 연구는 모델 기반 강화학습을 이용하여 정책 신경망의 아웃풋 form를 제한하고 모델 프리 강화학습을 이러한 form 중 특정 부분에만 허용하는 하이브리드 형식을 취합니다. 모델 프리 강화학습이 금융에 어울리지 않는다는 인식하고 있다는 점에서 저희 연구진과 생각이 같습니다. 다만 저희 연구진은 모델 기반 강화학습에 전적으로 의존하고 시장 모델로 생성 신경망을 사용하려는 반면, 해당 연구진은 모델을 여전히 사용하되 시장과 모델의 괴리를 모델 프리 강화학습으로 극복하려는 시도를 합니다.

## CGDPO 알고리즘의 결과 

### 완전 시장

<div style="display: flex; justify-content: space-between;">
    <img src="./complete/image/5min_consumption_complete_net.png" alt="5분 소비신경망" width="45%">
    <img src="./complete/image/5min_myopic_complete_net.png" alt="5분 투자신경망" width="45%">
</div>

왼쪽은 소비량을 예측하는 신경망 결과이고, 오른쪽은 투자량을 예측하는 신경망의 결과물입니다. 5분만에 매우 정확한 답을 얻음을 확인할 수 있습니다. 에러가 작다는 사실은 매칭되는 아래의 에러에 관한 그림을 봐도 확인됩니다.

<div style="display: flex; justify-content: space-between;">
    <img src="./complete/image/5min_consumption_complete_error.png" alt="5분 소비신경망 에러" width="45%">
    <img src="./complete/image/5min_myopic_complete_error.png" alt="5분 투자신경망 에러" width="45%">
</div>

### 불완전 시장

<div style="display: flex; justify-content: space-between;">
    <img src="./incomplete/image/1h_myopic_incomplete_net.png" alt="1시간 근시안신경망" width="45%">
    <img src="./incomplete/image/1h_hedging_incomplete_net.png" alt="1시간 헤징신경망" width="45%">
</div>

왼쪽은 근시안적 수요를 예측하는 신경망 결과이고, 오른쪽은 헤징 수요를 예측하는 신경망의 결과물입니다. 1시간이 지나니 두 개의 수요를 분리할만큼 매우 정확한 답이 도출되고 있음을 확인할 수 있습니다. 에러가 작다는 사실은 매칭되는 아래의 에러에 관한 그림을 봐도 확인됩니다.

<div style="display: flex; justify-content: space-between;">
    <img src="./incomplete/image/1h_myopic_incomplete_error.png" alt="1시간 근시안신경망 에러" width="45%">
    <img src="./incomplete/image/1h_hedging_incomplete_error.png" alt="1시간 헤징신경망 에러" width="45%">
</div>
