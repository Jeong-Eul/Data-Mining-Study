### Sequence to Sequence Learning with Neural Networks


##### Abstract
기존 DNN은 어려운 학습 문제에서 좋은 성능을 보여주었지만 Sequences to sequences 문제에서는 사용하기 어려웠음  
본 논문은 시퀀스 구조에 대한 최소한의 가정을 하면서 일반적인 end-to-end 시퀀스 학습 접근 방법을 제시함  

저자는 입력 시퀀스를 고정 차원의 vector로 mapping한 다음 해당 벡터에서 대상 시퀀스를 디코딩하는 데에 multilayered Long Short-Term Memory (LSTM)를 사용하는 일반적인 end-to-end 시퀀스 학습 접근 방법을 제안함  
이 방법을 영어에서 프랑스어로의 번역 작업에 적용한 결과, LSTM이 전체 테스트 세트에서 BLEU 점수 34.8을 달성함  
LSTM은 긴 문장에도 어려움이 없었으며, 기존의 SMT 시스템과 비교하여 높은 번역 품질을 보임  
LSTM은 단어 순서에 민감하며 능동태와 수동태에 상관없이 상대적으로 불변적인 표현을 학습함  

##### Introduction

DNN은 input과 target을 고정 차원 벡터로 적절하게 인코딩 할 수 있는 문제에만 적용될 수 있음  
음성인식, 기계번역과 같은 많은 문제들은 사전에 길이를 알 수 없는 시퀀스로 표현하는 것이 더욱 적절함  
이를 위해 도메인에 상관없이 시퀀스를 시퀀스로 mapping하는 방법을 배울 수 있는 모델이 필요함  

이러한 문제들은 길이가 알려지지 않은 시퀀스로 표현하는 것이 더 적절함-> LSTM 아키텍처를 사용하여 이 문제를 푸는 방법을 제시  

입력 시퀀스를 읽어서 고정 차원의 vector representation을 추출한 다음 다른 LSTM을 사용하여 target 시퀀스를 생성(LSTM은 장기 의존성이 있어 이러한 응용이 가능)  

시퀀스 문제를 neural network로 해결하고자 한 선행 연구의 시도  
-> Kalchbrenner and Blunsom [18]: Graves는 다른 부분에 집중할 수 있는 새로운 differentiable attention mechanism을, Blunsom은 이를 응용하여 machine translation에 성공함

##### The model
입력 시퀀스와 출력 시퀀스 간의 정렬이 미리 알려진 경우, 순환 신경망(RNN)은 쉽게 시퀀스를 시퀀스로 매핑할 수 있음  
하지만 입력과 출력 시퀀스가 길이가 서로 다르며 복잡하고 단조적이지 않은 경우에는 RNN을 어떻게 적용해야 할지 명확하지 않음  

RNN을 사용하여 입력 시퀀스와 출력 시퀀스의 길이가 다르고 복잡하며 비단조적인 관계가 있는 문제에 대해 일반적인 시퀀스 학습 전략은, 하나의 RNN을 사용하여 입력 시퀀스를 고정 크기 벡터로 매핑하고, 다른 RNN을 사용하여 이 벡터를 대상 시퀀스로 매핑하는 것임  

하지만 이 방법은 RNN에서 결과적으로 발생하는 장기 종속성으로 인해 학습하기 어려울 수 있음  
-> LSTM은 장기적인 시간 의존성을 가진 문제를 학습하는 것으로 알려져 있으므로, LSTM이 이러한 문제에 성공할 수 있다는 것  
![image](https://user-images.githubusercontent.com/122766824/223938355-1b41d566-dac0-407a-a820-914cb410a333.png)

LSTM은 입력 시퀀스를 고정 크기의 벡터로 매핑하고, 그 다음 다른 LSTM을 사용하여 벡터를 대상 시퀀스로 매핑하는 가장 간단한 전략을 사용함 또한 입력 문장의 단어 순서를 뒤집음으로써 모델의 성능을 향상시킴  

##### Experiment
작성 중입니다.
