### Efficient Estimation of Word Representations in Vector Space


#### Abstract  
저자는 데이터 세트로부터 단어의 연속적인 표현 벡터를 계산하기 위한 2개의 모델을 제안했음   
이러한 단어의 표현 벡터의 quality는 유사도 측정으로 평가되며, 이전에 나왔던 뉴럴넷 기반의 기술들과 비교를 했음  
저자는 제안한 모델이 적은 계산 비용으로 유사도 task에서 높은 성능 향상을 이루었고, 16억개의 단어를 높은 quality로 학습하는데 하루도 안걸린다고 설명을 덧붙였음    
더불어 저자는 이러한 벡터들이 syntactic, semantic 단어 유사도 측정을 위한 test set에서 sota 수준의 성능을 보였다고 함  

#### Introduction  
기존의 문제점)   
NLP 시스템에서 단어를 원자단위로 취급(one-hot encoding처럼)
 - 이는 간단하고, 강건하며 거대한 양의 데이터로 훈련되는 간단한 모델이 적은 데이터로 훈련된 복잡한 시스템보다 성능이 우수하다는 관찰 결과 때문  
 - 한계: 자동 음석 인식에서 관련 도메인 데이터의 양은 제한적, 일반적으로 성능은 고품질의 글로 기록된 음성 데이터의 크기에 지배됨(기계번역에서 많은 언어를 위한 존재하는 말뭉치들은 10억개 이하임)  
 - 따라서 이러한 상황은 주요한 진전을 얻을 수 없고 선진 기술에 집중할 필요가 있음  

최근의 향상된 머신러닝 기술을 통해 많은 데이터셋으로 복잡한 모델을 훈련시키는 것이 가능해짐 -> 기존의 간단한 모델을 능가

이 논문의 주요 목적)  
10억개의 단어로부터 높은 수준의 단어 벡터를 학습하는 기술을 소개  

#### Model Architectures  
기존에 존재하는 모델사이의 성능을 비교하기 위해 training complexity를 정의함  

$$O = E \times  T \times Q$$  

E는 훈련 epoch 수, T는 훈련 세트의 단어 수, Q는 각 모델 아키텍처에 대해 자세히 정의되는 value 

##### Feedforward Neural Net Language Model(NNLM)  

$N$: one-hot encoding 된 단어의 개수  
$V$: 어휘의 수  
$P$: projection layer  
$D$: word representation demension  
$H$: hidden layer size  
$V$: Output layer with dimensionality V  

$$Q = N \times D + N \times D \times H + H \times V$$  

embedding: $N \times D$  
hidden layer: $N \times D \times H$  
output: $H \times V$     

이 경우에서 가장 큰 연산량은 hidden layer에서 output을 만들어내는 연산인 $H \times V$ 이지만 hierarchical softmax를 사용하면 $H\times log_2(V)$로 연산을 줄일 수 있음  
따라서 가장 부하가 큰 연산은 projection layer에서 hidden layer를 만들어 내는 $N \times D \times H$가 됨  


##### Recurrent Neural Net Language Model(RNNLM)    
이 모델은 shallow neural network를 사용하는 NNLM에 비해 RNN을 사용하는 점에서 복잡한 패턴을 효율적으로 표현할 수 있음  
RNN 모델은 projection layer(one hot encoding 같은)를 필요로 하지 않음(오직 input, hidden, output layer)    
이런 모델에서 특별한 점은 time-delayed 연결을 사용하여 은닉층을 자신에게 연결하는 recurrent matrix 임  
  - 단기 기억 메모리를 형성할 수 있게 함   
  - 이전의 정보가 현재의 hidden layer에 표현되게 할 수 있음

$$Q = H \times H + H \times V$$  

동일하게, 가장 부하가 큰 연산량은 $H \times H$  

##### Parallel Training of Neural Networks  
거대한 데이터셋에서 모델을 훈련하기 위해 DistBelief라는 대규모 분산프레임워크 위에 여러 모델을 구현했음(이 프레임 워크는 NNLM과 이 논문에서 제안된 새로운 모델을 포함)  
많은 CPU 코어를 사용하는 모델 복제본을 사용하여 더욱 복잡한 모델을 빠르게 훈련할 수 있음  


##### New Log-linear Models  
computational complexity를 최소로하는 분산된 단어의 표현을 학습하기 위한 2가지 새로운 모델 아키텍쳐를 제안

1. Continuous Bag of Words Model  
2. Continuous Skip-gram Model  


##### Continuous Bag of Words Model  
NNLM에서 (가장 연산량이 많았던) hidden layer가 제거되고, projection layer는 모든 단어들에 공유됨  
따라서 모든 단어들은 같은 위치에 투영됨(그들의 벡터들은 평균처리 된다) ->  BOW라고 부름  

과거의 데이터 N개를 고려할 뿐만 아니라 미래의 단어 N개도 고려함  
(weight가 모든 input 단어들에 대해 공유됨) -> CBOW(figure 1)  
![image](https://user-images.githubusercontent.com/122766824/223931139-b00de507-e19f-499a-9671-eead2ee87e17.png)

$$Q = N \times D + D \times log_2(V) $$

##### Continuous Skip-gram Model

현재 단어의 벡터를 문맥을 고려하여 단순이 예측하는 것이 아니라 같은 문장에서 다른 단어에 기반을 둔 단어의 분류를 최대화하는 것을 시도  
현재의 input 단어를 continuous projection layer를 통해 log-linear classifier에 입력  
현재 단어를 제외한 현재 단어의 전 후 단어를 모두 예측  
현재 단어의 전 단어와 후 단어를 몇 개를 예측할 것인지에 따라 computational complexity가 커짐  
현재 단어와 거리가 먼 단어들이 관련성이 적기 때문에 그 단어들에게 weight를 덜 주는 방식으로 학습  

-> CBOW에서는 각 word vector들을 평균내서 사용하기 때문에 등장 빈도가 낮은 word들은 제대로 된 학습을 기대하기 힘듦  
    하지만 Skip gram은 input word vector를 온전히 사용하기 때문에 등장 빈도가 낮은 word들에 대해 train 효과가 크다는 장점이 있다.  

$$Q = C \times (D + D \times log_2(V) ) $$  

C는 단어의 최대 거리, 즉 현재 단어로부터 전의 단어 최대 C개, 후의 단어 최대 C개를 고려하겠다는 것  
R은 <1;C> 즉 1부터 C까지 랜덤하게 선택되는 파라미터이다.  

##### Result

선행 연구에 따라 단어 사이의 다양한 유형을 관찰할 수 있는 성능측정법을 사용  
big이라는 단어와 biggest라는 단어 사이의 관계를 학습하여 다른 단어인 small과 비슷한 smallest를 찾도록 하는 것  
즉 X = vector(‘biggest’) - vector(‘big’) + vector(‘small’)  
vector space에서 X와 코사인 거리가 가까운 단어를 찾을 수 있음  
훈련이 잘 되었다면 X를 잘 찾을 수 있음  
이와 같은 semantic한 의미까지 제대로 담은 word vector를 활용할 경우 수많은 NLP task에서 뛰어난 성능 향상을 보이게 됨  



##### Task Description

단어 벡터의 품질을 측정하기 위해 저자는 종합적인 test set을 정의함  
: semantic question 5가지, syntactic question 9가지  

모든 question types에 대해 각각 정확도를 측정했음  
다만 정답과 완전히 동일한 word를 예측한 경우에만 정답으로 간주하므로 정확도 100%는 사실상 불가능  
![image](https://user-images.githubusercontent.com/122766824/223931501-734667fb-2b0e-4cb6-a94e-9a8a83a2447a.png)

##### Maximization of Accuracy

백만개의 어휘에 포함된 단어를 가지고 모델링 -> 최적화 과정에서 시간 제약문제를 겪음  
따라서 data가 많아질수록 단어 벡터의 차원을 증가시키는 것이 정확도를 향상시킬 수 있음  

하나의 요인을 고정하고 하나의 요인만 증가시킬 경우 성능이 떨어지는 것을 관찰했고 두 요인 모두 증가시켜야함을 알 수 있었음
![image](https://user-images.githubusercontent.com/122766824/223931591-41a7c60b-51d9-4c97-b823-422d2c309f80.png)

##### Comparison of Model Architectures

같은 데이터, 같은 word dimension(640)으로 훈련시키고, 다양한 모델을 비교했음  
![image](https://user-images.githubusercontent.com/122766824/223931644-bc423ae0-77a7-4a31-aec6-a7b1982cc25e.png)
RNNLM-> 주로 syntactic question에 대해 좋은 성능  
NNLM -> RNN 보다 상당히 좋은 성능을 보임 이건 놀랍지 않은데, 이는 RNNLM의 단어 벡터가 비선형 은닉층과 직접적으로 연결되어 있기 때문(?) -> 잘 이해되지 않음  
: RNNLM에서는 이전 단어들의 정보가 현재 예측에 영향을 미치는 과정을 반영하기 때문에 데이터가 많지 않을 경우 과적합(overfitting) 문제가 발생하기 쉬운 반면에  
NNLM은 비선형 은직층이 없기 때문에 훈련이 상대적으로 간단하고, 데이터가 많지 않더라도 일반화 성능이 좋음 따라서 NNLM이 RNNLM보다 성능이 나은 경우가 있을 듯    

CBOW -> NNLM보다 syntactic, semantic tasks에 대해 더 나은 성능을 보임  
Skip-gram -> CBOW 모델보다 경미하게 syntactic task에서 나빴음 하지만 semantic면에서 다른 모델들 보다 성능이 좋았음  


##### Microsoft Research Sentence Completion Challenge

1040개의 각각의 문장에서 하나의 단어가 missing인 상황에서 5개의 선지 중에 가장 적절한 단어를 맞추는 문제  
여기서 Skip-gram과 RNNLMs를 결합하여 가장 SOTA를 달성함  
![image](https://user-images.githubusercontent.com/122766824/223931829-510efee2-c046-4c27-a53d-f269afbf9dd5.png)


##### Example of the learned relationships

단어 사이의 상관관계를 분석해 다른 단어에 대해 유사한 관계를 갖는 단어를 예측하는 task에서 본 논문의 model은 60%의 정확도를 달성함  
저자는 더욱 많은 data와 더욱 큰 dimension으로 훈련하면 word vector가 더 나은 성능을 낼 것이라고 함  
정확도를 높이는 다른 방법은 1개 이상의 relationship을 제공하는 것-> 10개의 example을 사용하여 제안한 모델의 정확도를 10% 정도 향상됨을 관찰함   

##### Conclusion
이 논문에서는 다양한 모델에서 파생된 단어 벡터 representation의 quality를 syntactic,  semantic task에 대해 연구했음  
기존의 연구와 비교하여 매우 간단한 모델을 사용해 계산 비용을 줄이면서 높은 품질의 단어 벡터를 생성하는 것이 가능하다는 것을 발견  
