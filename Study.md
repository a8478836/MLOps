# What is LLM?

## 목적
기존 LSTM, RNN은 병렬화가 불가하고 Long-term dependency, Gradient vanishing/Exploing 문제 발생  
> Long-term dependency: 멀리 떨어진 단어 간 관계를 알기 어려움  
> Gradient vanishing/exploding: vanishing, exploding는 역전파 시 기울기가 사라지거나 무한대로 커지는 현상  

Transformer 아키텍처는 attention 이라는 메커니즘을 도입하면서 기존의 모델들을이 가진 문제를 해결할 수 있음  
왜냐하면 transformer는 병렬처리가 가능하도록 개발되었으며, RNN/LSTM은 순차적으로 토큰들이 연결되는 반면에, transformer는 모든 토큰들이 서로를 한번에 연결하기 때

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


### Q K V 구하기
위의 정보를 구하기 위해서 Q(Query), K(Key), V(Value) 벡터를 구해야 함  
예를들어
> 'I love coffee'

가 있을 때
단어 개수 : n -> 3
임베딩 차원(연관성을 담은 벡터 공간): d -> 4 (가정)
Q: [3, d_k] → 3단어, d_k 차원  
K: [3, d_k] → 3단어, d_k 차원  
V: [3, d_v] → 3단어, d_v 차원  




