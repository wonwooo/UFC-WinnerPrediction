



# Deep Learning For UFC Winner Prediction
<p align="center"><img width="700" height="250" src="https://github.com/wonwooo/UFC-WinnerPrediction/blob/master/JDSvsCain.jpg"/></p>

## Model's prediction accuracy : 75%
- 500 경기에 대한 테스트 결과 승자 예측 정확도 75% 달성

## Dataset
- Kaggle의 [UFC-Fight historical data from 1993 to 2019](<https://github.com/graykode/toeicbert>)의 데이터를 이용해 승자 예측을 위한 ANN Classifier를 구현한 프로젝트
- 1999년 이전의 경기 데이터는 Rule의 변경으로 사용하지 않았고, 약 5000개의 경기 데이터를 사용
- Training+Val : Test = 9 : 1
- 경기 내용이 담긴 변수들은 경기 전 승자 예측에 사용할 수 없으므로 제거
- 두 선수의 능력치가 담긴 38개의 Numerical 변수와 3개의 Categorical 변수를 사용

## Features

- 승자 예측에 사용된 Feature는 아래와 같고, [UFC-Fight historical data from 1993 to 2019](<https://github.com/graykode/toeicbert>) 의 EDA를 참조하고 직접 성능을 실험해가며 경험적으로 선정함
```
current_win_streak : 최근 연승 기록
avg_KD : 경기 당 평균 Knockdown 횟수
avg_SIG_STR_pct : 경기 당유효타격의 비율
avg_SUB_ATT : 경기 당 Submission의 평균 시도횟수
avg_TD_landed : 경기 당 Takedown의 평균 성공횟수
avg_TOTAL_STR_att : 경기 당 평균 타격 시도
avg_TOTAL_STR_landed : 경기 당 평균 타격 성공
longest_win_streak : 최장 연승기록
losses : 패배
total_rounds_fought : 커리어를 통틀어 싸워 온 ROUND수의 합산
win_by_KO/TKO : KO/TKO 승리 횟수
win_by_Submission : Submission 승리 횟수
win_by_Decision : 판정승 횟수
wins : 승리
Height_cms : 신장
Reach_cms : 리치(팔길이)
age : 나이
WinPoint : 승점(이겨온 상대들의 강한 정도)
LosePoint : 패점(패배한 상대들의 약한 정도)
Stance : 파이팅 스탠스(오른손/ 왼손/ 양손)
no_of_rounds : 해당 경기의 라운드 수(3/5)
```

## KeyPoints for Model's Performance Imporvement

### 1.  New Features : WinPoint, LosePoint
- UFC 전문가들이 선수의 전력을 분석할 때 가장 중요하게 보는것은 그동안 싸워 온 상대들의 전력
- 데이터에 포함되지 않은 이 전력을 학습할 수 있도록 BLUE/ RED 코너의 선수에게 각각 2가지 Numerical feature를 데이터로부터 가공하여 추가
```
1.  얼마나 강한 상대를 이겨왔는가 : WinPoint
2.  얼마나 약한 상대에게 패해왔는가 : LosePoint
```
- 해당 선수의 전적을 탐색하면서, 승리한 경우 상대의 전적에서 승수를 가져와 WinPoint에 합산하여, 강한 상대(전적에 승이 많은 상대)를 이겨온 선수가 WinPoint가 높도록 함

- 패배한 경우에는 패배를 안겨준 상대의 전적에서 패배를 가져와 LosePoint에 합산하여 상대적으로 약한 상대(전적에 패가 많은 상대)에게 패할 수록 LosePoint가 높아지도록 함
-  따라서 BLUE/RED 코너의 선수에게 2개씩, 총 4개의 feature가 Input variable로 추가되었고, 해당 변수의 추가는 본 모델의 Accuracy 향상에 가장 큰 영향을 가져옴

### 2. Embedding Layer
본 모델에 사용된 Categorical(범주형) 변수는 3가지
```
1. RED코너의 Stance(오른손잡이/왼손잡이/양손잡이[Orthodox/Southpaw/Switch])
2. BLUE코너의 Stance(오른손잡이/왼손잡이/양손잡이[Orthodox/Southpaw/Switch])
3. 게임의 라운드 수(3라운드/5라운드)
```
- 본 모델에서는 범주형 변수를 ANN의 input으로 사용하기 위해 Stance는 3차원, 라운드 수는 2차원의 One-hot vector로 변환한 다음 Embedding(Linear) Layer를 거쳐 Numerical value로 변환하여 Input으로 사용
-  Embedding layer는 다른 Linear Layer와 마찬가지로 Backprop을 통해 학습

## Structure of ANN Classifier for UFC

- 전체 모델을 도식화 한 그림
- 3개의 Categorical 변수는 Embedding Layer을 거친 뒤 38개의 Numerical 변수와 합산
- 41차원의 Input Layer 
- 60차원의 Hidden Layer(BatchNorm)
- 30차원의 Hidden Layer(BatchNorm) 
- 2차원의 Output Layer(Winner Prediction)
- DropOut은 학습 시에만 적용 


<p align="center"><img width="800" height="800" src="https://github.com/wonwooo/UFC-WinnerPrediction/blob/master/ANN_structure.PNG"/></p>
