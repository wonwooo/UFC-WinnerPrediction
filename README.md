


# Deep Learning For UFC Winner Prediction
<p align="center"><img width="700" height="400" src="https://github.com/wonwooo/UFC-WinnerPrediction/blob/master/JDSvsCain.jpg"/></p>

### 최종 승자 예측 정확도 : 75%

Kaggle의 [UFC-Fight historical data from 1993 to 2019](<https://github.com/graykode/toeicbert>)의 데이터를 이용해 승자 예측을 위한 ANN Classifier를 구현한 프로젝트 입니다.
1999년 이전의 경기 데이터는 Rule의 변경으로 사용하지 않았고,
38개의 Numerical 변수와 3개의 Categorical 변수를 사용했습니다. 

## KeyPoints for Model's Performance Imporvement

### 1.  New Features : WinPoint, LosePoint
UFC 전문가들이 선수의 전력을 분석할 때 가장 중요하게 보는것은 그동안 싸워 온 상대들의 전력입니다. 따라서 BLUE/ RED 코너의 선수에게 각각 2가지 Numerical feature를 데이터로부터 가공하여 추가하였습니다.
1.  얼마나 강한 상대를 이겨왔는가(WinPoint)
2.  얼마나 약한 상대에게 패해왔는가(LosePoint)

해당 선수의 전적을 탐색하면서, 승리한 경우 상대의 전적에서 승수를 가져와 WinPoint에 합산하여, 강한 상대(전적에 승이 많은 상대)를 이겨온 선수가 WinPoint가 높도록 했습니다.

패배한 경우에는 패배를 안겨준 상대의 전적에서 패배를 가져와 LosePoint에 합산하여 상대적으로 약한 상대(전적에 패가 많은 상대)에게 패할 수록 LosePoint가 높아지도록 하였습니다. 따라서 BLUE/RED 코너의 선수에게 2개씩, 총 4개의 feature가 Input variable로 추가되었고, 해당 변수의 추가는 본 모델의 Accuracy 향상에 가장 큰 영향을 끼쳤습니다. 

### 2. Embedding Layer
본 모델에 사용된 Categorical(범주형) 변수는 3가지 입니다.
1. RED코너 선수의 Stance(오른손잡이[Orthodox], 왼손잡이[Southpaw], 양손잡이[Switch])
2.  BLUE코너의 Stance(오른손잡이[Orthodox], 왼손잡이[Southpaw], 양손잡이[Switch])
3.  게임의 라운드 수[3라운드/5라운드]

본 모델에서는 범주형 변수를 ANN의 input으로 사용하기 위해 Stance는 3차원, 라운드 수는 2차원의 One-hot vector로 변환한 다음 Embedding(Linear) Layer를 거쳐 3차원의 Numerical value로 변환하여 Input으로 사용하였습니다. Embedding layer는 다른 Linear Layer와 마찬가지로 Backprop을 통한 학습을 진행하였습니다.

## Structure of ANN Classifier for UFC

앞서 설명한 모델을 도식화 하면 아래 그림과 같습니다.
2개의 Hidden Layer(BatchNorm)와 Dropout를 사용하였고 마지막 Linear Layer를 통해
승자를 예측 하도록 하였습니다. 


<p align="center"><img width="800" height="800" src="https://github.com/wonwooo/UFC-WinnerPrediction/blob/master/ANN_structure.PNG"/></p>
