# Transformer 구조

## Positional Encoding

Transformer 구조는 RNN, LSTM의 한계를 극복하기 위해 개발된 구조이지만, 병렬적으로 처리하는 특성으로 인해 시간 또는 위치 정보를 알 지 못함  
따라서, Transformer 구조에서 이 역할을 대신하기 위해 위치 또는 시간(순서)정보를 vector에 추가하는 방법을 사용한다.  
Transformer 논문에서는 sine & cosine 함수 기반 위치 인코딩 방법을 사용함  

(1) sine & cosine 기반 positional encoding  

<img width="366" height="122" alt="image" src="https://github.com/user-attachments/assets/58942905-5ce0-4eaa-bcce-a4b746201abc" />  

- pos -> 토큰 위치 (0, 1, 2, …)  
- i -> 임베딩 차원의 인덱스  
- d_model -> 임베딩 차원 (예: 512)

(2) 특징
- 위치가 조금 바뀌어도 유사한 벡터 유지 가능
- 두 벡터 간의 위치 차이가 벡터 차이와 동일
- 학습 중 보지 못한 긴 문장에도 추론 시 사용 가능

단순히 입력 값을 임베딩한 후 positional encoding 값을 더하여 input으로 사용  


## Layer Normalization
Transformer 블록의 마지막에 layer norm.을 하여 토큰들을 정규화 한다. 그 이유는 학습 중에 벡터들의 입력 분포가 변하면 모델 학습이 어려워진다. 따라서 토큰 단위로 입력 값들을 안정화함으로써 모델의 성능도 안정시킬 수 있다. 따라서 입력 벡터들을 평균 0, 표준편차 1로 만든다.

(1) 수식

<img width="378" height="266" alt="image" src="https://github.com/user-attachments/assets/15863714-fc63-4ba1-accc-80a13a0ccc8a" />


