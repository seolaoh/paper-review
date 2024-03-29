# [TUNING RECURRENT NEURAL NETWORKS WITH REINFORCEMENT LEARNING](https://openreview.net/pdf?id=Syyv2e-Kx)

ICLR 2017

[code](https://github.com/natashamjaques/magenta/tree/rl-tuner/magenta/models/rl_tuner)  
[sample melodies](https://drive.google.com/drive/folders/0BycMAUU0mKhwN3BEMENCMXN2cFE)

## 0. Overview

* RNN으로 music sequence에서 다음 note를 생성하는 모델은 과도하게 token을 반복하거나 일관성 없는 sequence를 생성하는 경향이 있음
* RL Tuner는 pre-train된 RNN의 확률 분포는 그대로 유지하면서 음악 이론 기반의 제약을 두기 위해 강화학습을 이용하는 모델
* reward function은 task-related reward와 pre-train된 RNN에 의해 주어진 action의 확률을 결합한 형태

## 1. RL Tuner

* pre-train된 Note RNN에 음악 이론에 관한 개념들을 학습시키면서, 동시에 원래 데이터에서 학습한 정보는 유지하는 것이 목표
* Note RNN으로 학습된 LSTM을 사용하여 Q-network, Target Q-network, Reward RNN 초기화
* RL 모형화에서 state는 이전 note와 Q-network, Reward RNN의 LSTM cell의 internal state를 포함
* 음악 이론 기반의 rule을 정의하여 rule에 대한 reward signal rMT(a, s) 정의
* Reward RNN은 초기화된 후 고정되며, 이를 이용하여 log p(a|s)를 계산

<p align="center">
<img src="./figures/RL_Tuner.png", width=600> 
</p>

<p align="center">
<img src="./figures/equation_Tuner.png", width=700> 
</p>

### 1) RELATIONSHIP TO KL CONTROL
<br/>

### 2) MUSIC-THEORY BASED REWARD
* 모든 note가 같은 key에 속해야 한다
* 멜로디는 key의 tonic note로 시작하고 끝나야 한다
* rest가 있거나 한 note가 지속되지 않는 한, 하나의 tone은 한 행에 4번 이상 반복되지 않아야 한다
* 멜로디가 1,2,3 비트에서 자신과 0.15 이상의 상관관계가 있는 경우 모델에 페널티를 부여한다
* 멜로디는 augmented 7ths, 한 옥타브 이상의 큰 점프와 같은 어색한 구간이 없어야 한다
* 좋은 작곡은 작은 단계와 더 큰 harmonic interval의 혼합으로 움직여야 한다
* 가장 높은 note와 가장 낮은 note는 유일해야 한다
* 짧은 음악적 "아이디어"를 나타내는 note의 연속인 motif를 연주할 경우 모델은 reward를 받는다
* 모델이 하나의 멜로디 안에서 같은 motif를 반복하는 것이 좋다

## 2. EXPERIMENTS

* Note RNN의 학습을 위해 30,000개의 MIDI song으로부터 monophonic melody 추출
* 멜로디는 16개의 note로 나뉘어서, 각 time step은 한 마디의 1/16을 나타냄
* 0 = note off, 1 = no event, 3 옥타브의 notes로 인코딩 -> 길이가 38인 one-hot encoding
* Note RNN으로 정의된 policy가 cross entropy reward로 사용된 Q-learning과 prior policy로 사용된 generalized psi-learning, G-learning 비교

## 3. RESULTS

<p align="center">
<img src="./figures/music_theory.png", width=600> 
</p>

* 음악 이론 rule 관련 metric, 상단의 반은 0에 가까울수록, 하단의 반은 클수록 좋음
* RL의 적용으로 Note RNN의 "bad behavior"이 대부분 고쳐짐
* metric들의 개선 정도는 해당 behavior에 주어진 reward의 크기에 관련됨   

<p align="center">
<img src="./figures/average_reward.png", width=700> 
</p>

* training data에 대한 정보를 잘 유지했는지 보기 위해 music theory reward만을 사용하여 학습한 RL only 모델과 세 모델을 비교
* 세 모델이 베이스라인에 비해 높은 log p(a|s) 값을 유지하는 것을 확인, 이는 데이터 분포에 대한 정보를 유지했음을 의미
* Q와 psi 모델이 가장 듣기 좋은 멜로디를 생성

## Note
