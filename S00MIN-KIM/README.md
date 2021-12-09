# 버전 설명

자세한 코드 설명은 https://mysterious-world.tistory.com/2 에 있다. 

211122 ver는 **KoBERT 다중 감정분류 모델**이며,
211202 ver는 위 모델에 **KoGPT2 chatbot을 추가**한 버전이다. 

# 모델 선정 이유 및 간략한 원리 탐구
## BERT
### 특성
- 양방향을 지향하여 문맥 특성을 활용하여 좋은 성능을 냄.
- 대용량 말뭉치로 사전 학습을 진행하여 언어에 대한 이해도가 높음.
- 자신의 사용 목적에 따라 파인튜닝이 가능. output layer만 추가로 달아주면 원하는 결과를 출력해냄.
- **다중분류** 가능
- KoBERT는 이를 한국어로 학습시킨 모델
### 원리
1. 대량의 말뭉치를 Encoder를 통해 임베딩한 후 사전학습한다. 
  Token Embedding(Word Piece Embedding) + Segment Embedding(Sentence Embedding) + Position Embedding(Self Attention)

2. 사전 학습모델을 전이하여 파인튜닝하고 Task를 수행한다.
  MLM(Masked Language Model)와 NSP(Next Sentence Prediction)의 두 가지 방식을 이용한다.

## GPT2
### 특성
- 머신러닝 알고리즘을 활용해 입력된 샘플 텍스트를 구문론적, 문법적, 정보 등의 일관성을 갖춘 텍스트로 생성하는 자연어 처리 모델
- **문장생성**에 최적화
- KoGPT2는 이를 한국어로 학습시킨 모델
### 원리 
Encoder는 단순한 self-attention 레이어를 사용하는 반면, Decoder는 Masked Self Attention을 사용한다.
자기 회귀(Auto-regressive)적인 언어 모델이라 이미 생성된 토큰이 다음 토큰의 생성 확률에 영향을 끼치고, 이를 통해 다음 단어 예측이 가능하다. 
챗봇은 이러한 특성을 통해 다음 단어를 예측하는 Task를 반복하여 문장을 생성한다.

# 학습 데이터셋

### KoBERT 다중감정분류
1.~~[한국어 감정 정보가 포함된 단발성 대화 데이터셋](https://aihub.or.kr/opendata/keti-data/recognition-laguage/KETI-02-009)~~
2. [감정 분류를 위한 대화 음성 데이터셋](https://aihub.or.kr/opendata/keti-data/recognition-laguage/KETI-02-002)
![image](https://user-images.githubusercontent.com/68471619/145357042-0109cb8f-507c-4875-96df-9ee3e6eb1026.png)

### KoGPT2 챗봇
1. [챗봇 트레이닝용 문답 페어](https://github.com/songys/Chatbot_data)
2. [웰니스 대화 스크립트 데이터셋](https://aihub.or.kr/opendata/keti-data/recognition-laguage/KETI-02-006)
  ※ (<챗봇의 응답> | <사용자의 발화>, <감정 라벨>) 형식이 되도록 라벨링 작업이 필요하다.
![image](https://user-images.githubusercontent.com/68471619/145357192-bf3639a2-a33d-4db0-93c7-3efc9780db4f.png)

# 결과
 우울이 느껴지는 문장 위주로 테스트
![image](https://user-images.githubusercontent.com/68471619/145357864-43abe9db-a35f-4300-bf2d-6473e68db0b1.png)
![image](https://user-images.githubusercontent.com/68471619/145357884-30bc212c-a648-4570-8520-fb0550fd332a.png)


## reference
[KoBERT](https://github.com/haven-jeon/KoGPT2-chatbot)

[KoGPT2-chatbot](https://github.com/haven-jeon/KoGPT2-chatbot)

