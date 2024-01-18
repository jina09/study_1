# AI B모드 불량 영상 검출 자동화 프로젝트
작성자: 최진아

서론
초음파 장비를 시장에 내보내기 전 이 장비의 성능을 검증하기 위해 다양한 테스트를 수행합니다.
초음파 검사의 질을 향상시키기 위해 검사자 및 판독자에 대한 지속적인 교육이 중요하고, 초음파 진단 장치도 계속적인 품질 관리를 통해 정확하고 우수한 영상을 제공하여 임상 진료에 도움이 되는 정보를 제공해야 합니다. 따라서 진단 가치가 있는 영상을 제공하기 위해 적절한 품질관리가 시행되어야 합니다. 
초음파 장비는 인체 내 질병의 유무를 검사하는 의료장비로서 정확한 의료 영상 정보를 제공해야 하므로 장비 성능에 대한 평가는 표준화된 방법과 기준으로 반드시 이루어져야 합니다.

목적
기존에 AI를 이용하여 불량 영상 검출 자동화를 시행했을 때, 가성 불량이 많이 나왔습니다. AI를 이용하여 불량 영상 검출 자동화를 시행할 때, 정확도와 가성 불량과 과적합이 나오지 않도록 하는 것이 목표입니다. 
※ 가성 불량: 불량이 아닌데 불량으로 판정
※ B모드(2D모드): Brightness, 장기의 단면상을 실시간으로 화면에 흑백 음영으로 표시하며 초음파에서 가장 많이 사용되는 모드

데이터 전처리
V8과 W10(CA1-7A) 장비의 Broken: 1,654개, Normal: 4,152개를 사용하였습니다.
CNN_2 모델은 Data Augmentation 적용(좌우 반전, 회전, 대비)하여 데이터셋 크기를 2배 증강시킴. Broken: 3,308개, Normal: 8,304개
데이터셋 비율은 Train : Val : Test = 7:1:2
데이터 정규화 (0~255 -> 0~1)

인공지능 학습
프레임워크: Pytorch
알고리즘: CNN, ResNet18
Batch Size: 16, 32(CNN_2) / Learning Rate: 0.001 / 이미지 크기: 256x256 / 이진 분류
Activation Function: ReLU, Loss Function: CrossEntropyLoss, SGD
Epoch: 10 (컴퓨터 사양 관계로)

학습 결과
Model     지표	Train_Loss	Val_Loss	Val_Accuracy
CNN	0.014	0.011	99.7%
CNN_2	0.052	0.044	99.8%
ResNet18	0.0019	0.0017	99.96%

ResNet18 모델의 정확도가 99.96%로 다른 모델에 비해 높은 성능을 가집니다.
CNN_2는 CNN 모델에 대해 Data Augmentation을 적용하여 데이터셋이 2배입니다. 두 모델은 Batch Size가 32, Grayscale 적용 유무 차이를 가지고 있는데, 데이터셋이 더 크면 정확도가 향상될 거라 생각했지만, CNN_2의 Loss값이 CNN 모델보다 떨어지는 것을 확인할 수 있습니다.






테스팅
가장 정확도가 높은 모델을 새로운 데이터셋을 이용하여 테스팅합니다. (과적합과 가성불량 테스팅)
Predict        Org	Broken	Normal
Broken	569	7
Normal	0	724
가성불량: 731 中 7, 진성불량: 569 中 0, 정확도: 99.46%
나쁘지 않은 성능을 보입니다.

결론
B모드에서의 불량 영상 자동 검출에 높은 성능을 보이는 모델은 ResNet18이며 이 모델을 가지고 다른 모드에서도 활용하는 것도 괜찮을 것 같습니다.
 

