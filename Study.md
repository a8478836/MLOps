# What is LLM?

## 목적
기존 LSTM, RNN은 병렬화가 불가하고 Long-term dependency, Gradient vanishing/Exploing 문제 발생  
> Long-term dependency: 멀리 떨어진 단어 간 관계를 알기 어려움  
> Gradient vanishing/exploding: vanishing, exploding는 역전파 시 기울기가 사라지거나 무한대로 커지는 현상  

Transformer 아키텍처는 attention 이라는 메커니즘을 도입하면서 기존의 모델들을이 가진 문제를 해결할 수 있음  
왜냐하면 transformer는 병렬처리가 가능하도록 개발되었으며, RNN/LSTM은 순차적으로 토큰들이 연결되는 반면에, transformer는 모든 토큰들이 서로를 한번에 연결하기 때문

## 원리
### Attention
Attention의 핵심 원리는 중요한 단어에 더 '집중' 하는 것  
예를 들어,

> 나는 어제 스타벅스에서 커피를 마셨다

'스타벅스' 라는 단어를 이해하려면 '어제' 라는 단어보다 '커피'라는 단어와의 관계가 중요.  
단어와 다른 단어들 간의 연관 점수를 계산해서 중요한 단어에 가중치를 더 크게 주는 것이 Attention의 원리  
Attention의 종류는 bahdanau attention, dot-product attention, scaled dot-product attention 등이 있고, 구조적 관점에서 self-attention, cross-attention, multi-head attention등이 있음  
이중에서 self-attention에 대해 중점적으로 정리

### Scaled Dot-Product attention
Transformer에서 가장 기본이 되는 attention이며 각 Query가 key를 얼마나 주목할지 계산하고, value를 가중합해서 context 벡터를 생성  
> Query: 질문어, 다른 단어와 얼마나 관련이 있어야하는지 -> 내가 궁금한 내용  
> Key: 특징, 어떤 특징을 가지고 있는지에 대한 값 -> 도서관 책 제목  
> Value: 실제 정보, 실제 의미 -> 책 내용  
즉, Q가 각 K와 얼마나 비슷한지 확인 -> V  

<Q K V 구하기>
단어 개수 : n
임베딩 차원 : k
Q: Query 벡터 행렬 [n, d_k]  
K: Key 벡터 행렬 [n, d_k]  
V: Value 벡터 행렬 [n, d_v]  

Q와 K를 내적한 값을 root(d_k)로 나누어 스케일하고 softmax로 얼마나 주목하는지 확률로 변환한 값이 attention weight이며 각 단어가 가진 의미 정보 벡터인 V 값을 softmax(Q*k_upper(T) / root(d_k))*V 하면 output 구할 수 있음 

<img width="381" height="74" alt="image" src="https://github.com/user-attachments/assets/a180ac5b-cb50-47ed-951f-1a5789b565be" />  
* QK<sup>T</sup>에서 K의 전치 행렬을 곱하는 이유는 Q와 K의 유사도를 위해서 내적을 해야하는데, 내적을 할때 shape를 맞추기 위함임
  

### Self-Attention
같은 시퀀스 내 단어끼리 서로를 바라보며 context를 학습하는데, 입력 문장만으로 각 단어의 문맥 정보를 파악  
<ins>즉 self-attention의 내부 계산 방식이 scaled dot-product attention 방법임</ins>  
한 문장에서 위의 정보를 구하기 위해서 Q(Query), K(Key), V(Value) 벡터를 구해야 함  

<img width="569" height="38" alt="image" src="https://github.com/user-attachments/assets/a5b6679b-379e-44da-bc36-313bb3d6f3ba" />  

예를들어
> 'I love coffee'

라는 한 문장이 있을 때
단어 개수 : n -> 3
임베딩 차원(연관성을 담은 벡터 공간): d -> 4 (가정)  
Q: [3, d_k] → 3단어, d_k 차원  
K: [3, d_k] → 3단어, d_k 차원  
V: [3, d_v] → 3단어, d_v 차원  

Q와 K를 내적한 값을 root(d_k)로 나누어 스케일하고 softmax로 얼마나 주목하는지 확률로 변환한 값에 weight를 곱하면 V이고, 이 V 값이 최종 context 벡터 
예를 들어 한개의 물체를 한개의 카메라만 사용하여 촬영 -> 한 면에만 집중!!!  

### Multi-head attention
여러 개의 self-attention을 병렬로 수행함으로써 다양한 관점에서 context를 학습. 각 head가 Q, K, V를 서로 다른 가중치로 변환  
- 1 head: 단일 관점에서 단어 관계 학습 = self-attention
- n head: 서로 다른 문맥/패턴을 동시에 학습 = multi-head attention
- 512개의 차원을 8 head면 1 head당 64개의 차원을 가짐  

self-attention하고 차이는 multi-head attention은 한 물체를 n개의 카메라로 다방면에서 촬영하는 것 -> 입체적으로 집중!!

<img width="450" height="73" alt="image" src="https://github.com/user-attachments/assets/d6ce5419-1fd6-47f1-8fbe-d288772cb2ca" />  

결국 n 개의 head에서 나온 값들을 concatenate -> [n, h-d_v] 한 후 w_o라는 가중치 행렬을 곱해 최종 출력을 생성  
결국 역전파에서 attention output에 포함된 w_q, w_k, w_v, w_o가 Gradient descent로 업데이트 
여기서 w_q, w_k, w_v는 랜덤하게 초기화

### Cross-attention
Cross-attention을 Scaled dot-product attention을 서로 다른 시퀀스(예를 들어 문장)에 적용한 것  
Q -> Decoder의 hidden state  
K, V -> Encoder의 output에서 나옴  
Score = 똑같은 수식  

예를 들어 영어를 한국어로 번역하는 transformer가 있을 때 영어 문장을 벡터화 하면 K, V가 나오고 Decoder의 한국어로 번역된 단어 시퀀스를 Q라고 하면 Decoder가 한국어 단어를 예측할 때 영어 문장의 단어들을 K, V로 참고하여 최적의 단어를 선택하게 한다.  




