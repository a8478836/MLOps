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
