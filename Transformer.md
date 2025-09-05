<img width="797" height="107" alt="image" src="https://github.com/user-attachments/assets/45a73d8e-8b2c-470c-98b9-0ebc00da697d" /># Transformer 구조

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

alpha와 beta 값은 학습 가능한 파라미터로 attention output과 FFN의 출력을 안정화한다.  

## Residual Connection(skip connection)
Transformer에서 모든 블록들은 연결하는 방법  
현재 연산에 이전의 정보(벡터)들을 저장하여 전달한다.  
<img width="797" height="107" alt="image" src="https://github.com/user-attachments/assets/87139db3-167e-456d-92c9-aa30d7046c3c" />  

단순히 값을 더해주지만 gradient 계산에 큰 기여. gradient가 직접 전달되어 vanishing gradient 막을 수 있음


## Position-wise feed forward network(FFN)
Transformer의 FFN에서는 두 번의 선형 변환과 1번의 비선형 활성화(ReLU 또느느 GELU)가 사용된다. 그 이유는 모델이 단어들의 선형적인 특징 뿐만 아니라 비선형적인 특징들도 학습하여 representation을 높이기 위함. 자연어, 이미지, 음성 등은 대부분 비선형적인 특징을 갖고 있음  

(1) 수식

<img width="403" height="60" alt="image" src="https://github.com/user-attachments/assets/621edd8a-3d56-43c0-9101-79eb2871e8a3" />  
첫 번째 선형 변환으로 더 풍부한 표현을 학습하고, 비선형(ReLU나 GELU) 활성화를 통해 모델의 표현력(복잡한 패턴 학습)을 향상시키고, 다시 선형 변환함으로써 attention 출력과 동일한 차원으로 변환함  

토큰 별록 독립적으로 적용하기 때문에 토큰의 표현력을 강화할 수 있다.  







