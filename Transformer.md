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
Transformer의 FFN에서는 두 번의 선형 변환과 1번의 비선형 활성화(ReLU 또는 GELU)가 사용된다. 그 이유는 모델이 단어들의 선형적인 특징 뿐만 아니라 비선형적인 특징들도 학습하여 representation을 높이기 위함. 자연어, 이미지, 음성 등은 대부분 비선형적인 특징을 갖고 있음  

(1) 수식

<img width="403" height="60" alt="image" src="https://github.com/user-attachments/assets/621edd8a-3d56-43c0-9101-79eb2871e8a3" />  
첫 번째 선형 변환으로 더 풍부한 표현을 학습하고, 비선형(ReLU나 GELU) 활성화를 통해 모델의 표현력(복잡한 패턴 학습)을 향상시키고, 다시 선형 변환함으로써 attention 출력과 동일한 차원으로 변환함  

토큰 별록 독립적으로 적용하기 때문에 토큰의 표현력을 강화할 수 있다.  

## Transforemr
Transformer는 입력 전체에 대해 병렬적으로 관계에 대한 의존성을 계산하는 신경망 구조이며, 핵심은 self-attention을 사용하여 각 토큰이 다른 토큰과 얼마나 관련이 있는지 알 수 있다. 초창기에는 Encoder-decorder 구조인 기계 번역을 위해 개발되었지만, 다양한 분야에서 응용하여 확장되었다.  

위에서 공부했던 개념들을 바탕으로 보통 아래와 같이 layer를 설계한다.
- Encoder layer (한 층):
1. Multi-Head Self-Attention (입력에 대해)
2. Residual 연결 + LayerNorm
3. Position-wise Feed-Forward Network (FFN): 두 개의 선형층 사이에 비선형 (원 논문은 ReLU, 현대 모델은 GELU)
4. Residual + LayerNorm

- Decoder layer (한 층):
1. Masked Multi-Head Self-Attention (미래 토큰을 보지 않도록 causal mask)
2. Residual + LayerNorm
3. Encoder-Decoder Multi-Head Attention (쿼리는 디코더, 키/값은 인코더 출력)
4. Residual + LayerNorm
5. FFN + Residual + LayerNorm

### 학습
1. Optimizer: Adam or AdamW
2. Scheduler: learning rate warmup + inverse-sqrt decay
3. Regularization: Dropout, label smoothing
4. Tokenization: BPE, SentencePiece
5. etc: Mixed-precision(AMP), graident accumulation, gradient clipping

예시) '나는 학교에 간다' -> 512 차원, 8개의 head일 경우
나는 -> [0.1 0.2 ... 0.1235] 512 차원 (512,)  
학교에 -> [0.13 0.24 ... 0.3235] 512 차원 (512,)  
간다 -> [0.89 0.72 ... 0.1285] 512 차원 (512,)  

multi-head attention을 하면 Q, K, V를 생성하면  

<img width="504" height="147" alt="image" src="https://github.com/user-attachments/assets/eaf03a67-dee2-4d88-b152-03288e4c2a64" />

8개의 head가 있으므로 각각에서 3개의 토큰 * 64차원의 벡터를 계산  
<img width="468" height="186" alt="image" src="https://github.com/user-attachments/assets/78bfb1d6-36e8-4f35-bb11-ba54d3cfa6d2" />  

각 head에서 scaled dot-product attention 계산
<img width="510" height="195" alt="image" src="https://github.com/user-attachments/assets/eb5f979f-c739-4a87-b4ef-e9c9a297a0e1" />  

head 결과를 concatenate

<img width="485" height="122" alt="image" src="https://github.com/user-attachments/assets/1fbe6a36-de25-470f-bffb-4fc73d109bb5" />



