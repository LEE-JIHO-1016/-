##딥러닝을 활용한 교통표지판 classification 및 detection

###1. 과제 개요
   
   가. 과제 설계 배경 및 필요성
ADAS(Advanced Driving Assistance System)를 둘러싼 기술은 계속해서 증가하고 있습니다. 지능형 주행 및 교통 안전을 지속적으로 개선하기 위해, 물체 감지는 다가올 자율 주행 자동차에서 중요한 역할을 합니다. Tesla Inc.는 세계 최고의 자율주행차 제조업체이며 세계에서 가장 가치 있는 자동차 회사가 되었습니다. 미국의 구글Google, 중국의 바이두Baidu, 알리바바Alibaba 등 ICT업체들은 직접 자율주행 시험차를 만들어 주행 정보를 수집하거나, 인공지능 스타트업을 설립하거나 인수해 딥러닝 기술 개발에 박차를 가하고 있습니다. GM, 포드Ford 등 자동차업체들도 스타트업 기업에 투자하거나 인수함으로써 기술 습득과 협력에 속도를 내고 있습니다. 이것은 자동차 산업에서 자율주행 자동차에 대한 엄청난 수요를 보여줍니다. 자율주행 기술을 완성하기 위해서는 표지판 인식 기능이 반드시 필요합니다. 자동차가 어떠한 주행 환경에도 유연하게 반응할 수 있어야 하기 때문입니다. 
이는 교통표지판이 정확하고 신속하게 탐지되어야 한다는 것을 의미합니다. 이러한 시스템은 안전에 대한 위험을 줄이고 비용을 절감하는 이점이 있기 때문에 이러한 시스템을 개발하고 개선하는 것이 이 프로젝트의 주요 동기입니다. 또한 딥 러닝 알고리즘을 사용하여 이러한 시스템을 설계하는 것이 주요 목표입니다. 

   나. 과제 주요내용
  자동차 교통표지판 인식 시스템은 인식 단계와 분류 단계의 구조를 가지고 있습니다. 인식 단계에서는 교통 표지판이 설치되어 있을 만한 지역을 찾고, 분류 단계에서는 교통표지판의 의미를 파악하고 분류합니다. 저는 이 교통표지판을 딥러닝 알고리즘을 이용하여 독일 교통 표지판인 German Traffic Sign Detection Benchmark (GTDRB)을 yolo format으로 변형시킨 데이터셋을 이용해 detection을 진행하고 class수를 늘리기 위해 German Traffic Sign Recognition Benchmark(GTSRB)라고 불리는 데이터셋을 이용하여 detection과 더불어 classification을 진행하여 모델을 학습시키고 이를 이미지 상으로 구현하였습니다.

   다. 최종결과물의 목표
교통표지판을 정확하고 빠르게 인식하여 안전을 보장하는 강력한 교통표지 인식 시스템을 설계하는 것이 목표입니다. 모델들을 학습시켜서 그 중 정확도와 속도가 가장 빠른 모델을 설계하는 것이 최종 목표입니다. 또한 교통 표지판을 추적하여 교통표지판을 정확히 인식하는 것이 목표입니다.

###2. 과제 수행방법

YOLOv4 설정

교통표지판을 detection하기 위해 YOLOv4모델을 사용하였습니다.
Yolov4를 colab에서 사용하기 위해 우선 darknet을 클로닝 해준후, 미리 학습된 yolov4 weights 파일을 다운로드 받았습니다. 그 다음, custom데이터를 훈련시키기 위해서는 가장 먼저 cfg 파일을 수정해주어야 합니다. 넓이와 높이가 608 , 608로 되어있는데 이렇게 진행하면 gpu 메모리를 상당히 차지하므로 416,416로 수정해줍니다. batch를 얼마나 쪼개서 학습을 할건지에 대한 설정 값인 subdivisions = 16 으로 수정하고 max_batches는 데이터의 클래스 갯수 * 2000 을 적어줘야 하므로 4* 2000=8000으로 적어줍니다. steps는 max_batches의 80%의 수치와 90%의 수치를 작성해주면 됩니다. 마지막으로 filters = (classes + 5) x 3으로 수정해야하므로 27로 수정해줍니다. Yolo 라고 적혀져있는 부분마다 수정해줍니다.names,data,train.txt도 데이터에 맞게 수정해줍니다. 마지막으로 train을 시켜줍니다.

YOLOv4+CNN(4개 classes -> 43개 classes)

YOLO format으로 구성된 GTDRB데이터는 클래스가 4가지로, 적은 클래스를 보충하기 위해 YOLOv4 모델을 사용하여 이미지에서 객체를 감지하고, 이후 cnn 모델을 사용하여 43개의 class들로 분류하고 감지된 객체의 레이블을 예측하기로 하였습니다. CNN의 구조는 LeNet-5를 사용하였습니다.
Yolo format의 dataset에 CNN 모델을 사용하여 교통 표지판을 43개의 class들로 분류한 과정은 다음과 같습니다. 먼저 이미지 폴더에서 이미지를 가져왔습니다. 그런 다음, YOLO 모델을 사용하여 이미지에서 객체를 감지하였습니다. 객체가 감지되면, 그 객체를 포함하는 바운딩 박스를 그리고, 그 영역을 잘라내어 일정한 크기로 조정합니다. 이후, CNN 모델을 사용하여 잘린 이미지를 분류하고, 해당 객체의 레이블을 가져와서 바운딩 박스 위에 작성하였습니다. 마지막으로, 분류된 이미지를 출력 폴더에 저장하였습니다. 
하지만 YOLO dataset에 43개 class의 Labeling이 되어있지 않으므로 정확도를 나타내지 못합니다. 따라서 수치적으로 성능을 판별하기 위해 data labeling을 진행하였습니다. 한 장에 여러 표지판이 있는 경우가 많아서 502장을 multi-labeling을 진행하였습니다.

