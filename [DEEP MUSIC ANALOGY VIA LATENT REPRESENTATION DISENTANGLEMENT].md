# [Paper] DEEP MUSIC ANALOGY VIA LATENT REPRESENTATION DISENTANGLEMENT

[paper](https://archives.ismir.net/ismir2019/paper/000072.pdf)

ISMIR 2019

## **0. Overview**

- explicitly-constrained conditional variational autoencoder (EC2-VAE)
    - chord에 condition된 음악의 pitch와 rhythm representation을 disentangle
    - 네트워크의 중간 output에 명시적인 semantic constraint를 준 conditional VAE

## **1. Methodology**

- Nottingham dataset, unit은 1/4 beat 단위

### **1) Data Representation**

- 8-beat 멜로디 각각은 32 time step의 one-hot vector(130 dim)
    - 128 dim은 MIDI pitch의 onset (0-127)
    - 129 dim은 holding state, 130 dim은 rest
- rhythm 패턴 역시 32 time step의 one-hot vector(3 dim: onset, holding state, rest)
- chord는 input으로 주어지며 12 dim을 가지는 32 multi-hot vector (chromagram)

### **2) Model Architecture**

- 인코더에 biGRU 사용해서 멜로디를 latent representation z로 매핑
- 디코더에 uniGRU 사용해서 z로부터 멜로디 reconstruct
- **key innovation**
    - 디코더의 일부분에 subtask 부여
        - 명시적으로 latent rhythm representation Zr의 중간 output이 멜로디의 rhythm feature를 나타내도록 loss를 설계함으로써 z로부터 Zr을 disentangle
        - z의 나머지 부분은 rhythm 외의 모든 부분이며 latent pitch representation Zp로 해석됨
    - 또한, 인코더와 디코더에 chord를 추가 input(condition)으로 줌
        - 이는 z가 chord에 관련된 정보는 저장하지 않아도 되게끔 함
        - 대신 chord 진행의 latent 분포는 학습 불가

![%5BPaper%5D%20DEEP%20MUSIC%20ANALOGY%20VIA%20LATENT%20REPRESENTATI%2002c84c7d206443b892a9286f8623c20e/Untitled.png](%5BPaper%5D%20DEEP%20MUSIC%20ANALOGY%20VIA%20LATENT%20REPRESENTATI%2002c84c7d206443b892a9286f8623c20e/Untitled.png)

- chord는 각 time step에서 input에 concat
- disentanglement를 위해 z가 각각 128 dimension을 가지도록 Zp와 Zr로 나눠짐
- global 디코더의 인풋으로 Zp와 chord condition과 rhythm이 모두 활용됨
- rhythm과 멜로디 reconstruction에 모두 CE
- 단순히 rhythm 패턴에 pitch가 따라가지 않도록 디코더가 pitch와 rhythm 사이 관계 학습

## **2. Experiments**

### **1) Objective Measurements**

- 원래 멜로디의 pitch의 변화가 latent rhythm representation에 영향을 주지 않아야 함, 반대도 마찬가지

**1-1) Visualizing delta-z after transposition**

- rhythm과 chord는 그대로 두고 pitch만 i semitone 변화시켰을 때

![%5BPaper%5D%20DEEP%20MUSIC%20ANALOGY%20VIA%20LATENT%20REPRESENTATI%2002c84c7d206443b892a9286f8623c20e/Untitled%201.png](%5BPaper%5D%20DEEP%20MUSIC%20ANALOGY%20VIA%20LATENT%20REPRESENTATI%2002c84c7d206443b892a9286f8623c20e/Untitled%201.png)

- Zr의 변화보다 Zp의 변화가 훨씬 큰 모습
- 인간의 pitch perception을 반영하는 특징
    - chord가 주어졌을 때, Zp의 변화는 transpose된 멜로디를 기억하는 어려움으로 해석 가능
    - 어려움이 불협화음인 tritone에서 크고, 조화로운 major third, perfect fourth & fifth에서는 상대적으로 작은 양상, perfect octave에서 매우 작음

**1-2) F-score of Augmentation-based Query**

![%5BPaper%5D%20DEEP%20MUSIC%20ANALOGY%20VIA%20LATENT%20REPRESENTATI%2002c84c7d206443b892a9286f8623c20e/Untitled%202.png](%5BPaper%5D%20DEEP%20MUSIC%20ANALOGY%20VIA%20LATENT%20REPRESENTATI%2002c84c7d206443b892a9286f8623c20e/Untitled%202.png)

### 2**) Examples of Generation via Analogy**

- source 멜로디에서 Zp만 변경했을 경우, 생성된 멜로디가 source의 rhythm은 유지하면서 pitch feature를 잘 캡쳐
- Zr만 변경할 경우 완벽하게 새로운 rhythm 패턴을 상속받고 source의 pitch에 기반하여 작지만 새로운 변화를 생성
- 다른 chord 진행으로 source 멜로디를 생성할 경우, 새 멜로디가 단순히 모든 note를 transpose 하는게 아니라 합리적인 변화를 만들어 냄
- Zr과 Zp 학습된 latent representation 두 축을 이용한 subspace 상에서의 traversal 도 가능

### 3**) Subjective Evaluation**

- rhythm factor 변경
- baseline은 원본보다 더 빠른 rhythm을 갖게끔 단순히 note들을 잘라서 만든 rule-based 방식

![%5BPaper%5D%20DEEP%20MUSIC%20ANALOGY%20VIA%20LATENT%20REPRESENTATI%2002c84c7d206443b892a9286f8623c20e/Untitled%203.png](%5BPaper%5D%20DEEP%20MUSIC%20ANALOGY%20VIA%20LATENT%20REPRESENTATI%2002c84c7d206443b892a9286f8623c20e/Untitled%203.png)

## **Note**