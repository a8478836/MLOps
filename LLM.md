# LLM(Large language model)

## LLM의 핵심 원리
### Pretraining
LLM의 pretraining 방식은 2가지가 있다.
1. Masked Language Modeling (MLM): BERT 계열에서 사용되며 문장의 일부 단어를 mask로 가려서 맞추도록 학습(양방향 문맥 학습)
2. Casual Language Modeling (CLM): GPT 계열에서 사용되며 앞의 단어들을 보고 다음 단어를 예측하도록 학습(단방향 문맥 학습)

### Fine tuning
Pretraining에서 문맥을 이해하고 특정 Task에 맞게 조정하는 단계
1. Task-specific Fine-tuning
  - BERT에 Classification layer를 붙여서 감성 분석, 문장 분류
  - GPT에 요약 데이터셋으로 fine tuning
3. Instruction Fine-tuning
  - GPT-3 -> GPT-3.5 / GPT-4 발전의 핵심 기술
  - 사람이 쓴 '명령-응답(instruction-response)' 데이터셋으로 학습
4. RLHF(Reinforcement Learning with Human Feedback)
  - 사람의 피드백을 보상함수로 강화 학습
  - 모델이 '사람의 선호하는 답'을 생성하도록 조정
  - ChatGPT 같은 대화형 모델의 핵심

## 대표 LLM 아키텍쳐
### BERT(Encoder-Only)
BERT는 Transformer 구조에서 Encoder만 사용하여 양방향 문맥(MLM)을 학습하도록 설계 됨  
예를 들어 'I Love you' 라는 문장이 있을 때 이 문장을 토큰화하면 [I] [Love] [You]  
입력 : I [Mask] You
출력 : Love  
를 하나의 학습 데이터 쌍을 이루게 함  

위의 방식은 문맥 이해에 특화되어 있기에 생성 보다는 분석, 즉 '이해형 모델'

### GPT(Decoder-Only)
GPT는 Transformer에서 Decoder만 사용하여 단반향 문맥(CLM)을 학습하도록 설계 됨  
예를 들어 'I Love you' 라는 문장이 있을 때 이 문장을 토큰화하면 [I] [Love] [You]  
입력 : I Love
출력 : You
를 하나의 학습 데이터 쌍을 이루게 함

위의 방식은 자연스러운 텍스트 생성에 특화되어 있기에 문장 생성에 강한 '생성형 모델'

### T5(Encoder-Decoder, Seq2Seq)
T5는 Transfomer에서 Encoder와 Decoder 양쪽 다 사용하여 모든 문제를 text-to-text로 통일
예를 들어 'I Love You'라는 문장이 있을 때, 이 문장을 토큰화하면  [I] [Love] [You]  
입력 : I Love you
출력 : 나는 널 사랑해
를 하나의 학습 데이터 쌍을 이루게 함

위의 방식은 Encoder와 Decoder의 강점을 모두 가지고 있기 때문에 모든 NLP 태스크에서 사용 가능, 즉 '범용 모델'
