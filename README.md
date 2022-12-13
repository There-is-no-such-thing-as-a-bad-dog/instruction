# 2022년 기업애로해결 프로젝(희망이음 경진대회) 반려동물 이상행동 분석 서비스

[<img src="https://img.shields.io/badge/github-181717?style=for-the-badge&logo=github&logoColor=white">Client](https://github.com/There-is-no-such-thing-as-a-bad-dog/DOOOGG)


[<img src="https://img.shields.io/badge/github-181717?style=for-the-badge&logo=github&logoColor=white">Server](https://github.com/There-is-no-such-thing-as-a-bad-dog/yolov5_flask_jetson-nano)

### 만든이

[최재혁](https://github.com/jjaegii) - 학습 및 추론, 데이터 시각화<div/>
[안수진](https://github.com/ssuzyn) - 데이터 전처리<div/>
[신혜민](https://github.com/heymin2) - 어플리케이션 제작<div/>

## 결과물 사진
![image](https://user-images.githubusercontent.com/77189999/207287530-6dc6867e-1191-4e48-98bc-4503942fc70e.png)
**사진1** 메인화면(왼쪽) 데이터베이스 시각화(오른쪽) <div/>
![image](https://user-images.githubusercontent.com/77189999/207287577-a94a0f2a-987b-4473-8eec-dcfc3af855cf.png)
**사진 2** 이상행동(왼쪽) 앱푸시 알림(오른쪽)


## 1. 프로젝트의 목적, 개발동기 및 필요성
 1인 가구의 증가, 고령화 등 세계 인구 구조의 변화로 반려동물 양육 인구가 증가하고, 반려동물을 가족처럼 생각하는 문화가 확산되었다. 코로나19로 인해 위축된 경기에도 불구하고 글로벌 펫케어 시장은 지속적으로 성장하고 확대되었다는 것을 뉴스를 통해서 볼 수 있다. 관련된 펫 테크 선행기술에서는 관찰하는 반려동물의 이상행동이나 움직임을 탐지하면 해당 영상을 녹화로 남겨두어 사용자에게 제공하거나, 원격으로 대화 및 소통하는 서비스가 기본적인 주요 기능이며 반려동물의 모든 활동을 확인할 수 있는 활동 로그를 제공하는 기능은 많이 개발되지 않은 것을 확인할 수 있었다.
 따라서 기업에서 반려동물과 함께하는 1인 가구에게 필요한 서비스를 구현할 수 있는 기술을 요구하였고, 홈 CCTV 영상에 기반을 둔 인공지능을 활용하여 반려동물의 행동을 분석하고 분석된 자료를 바탕으로 학습을 통한 이상행동 모델을 도출하고자 한다. 도출된 모델은 반려동물의 이상행동을 감지하고 감지된 이상행동을 탐지했을 시 반려인에게 알림 서비스를 제공하고, 이상행동 감지 횟수를 그래프로 시각화하여 반려동물의 행동 유형을 분석하는 서비스 또한 제공한다는 차별성을 두고 있다.
 
## 2. 프로젝트의 해결방안 및 수행과정
|**학습용 GPU**|**추론 및 결과 송출용 서버**|**클라이언트**|
|---|---|---|
|RTX 3090 Ti|Jetson Nano|Galaxy A90|
**표 1** 하드웨어


|**학습 및 추론 프레임워크**|**웹서버**|**알람 메시지 서비스**|**데이터베이스**|
|---|---|---|---|
|PyTorch, YOLOv5|Flask|FCM|MySQL|
**표 2** 소프트웨어


![image](https://user-images.githubusercontent.com/77189999/207277824-723d2a4b-9d75-4fdb-9276-615ac4f79dc3.png)
**그림 3** 시스템 동작도

우리는 해당 프로젝트의 목적을 달성하기 위해 추론 모델 학습, 학습된 모델로 실시간 추론 및 추론 결과 송출, 수신된 추론 결과 시각화를 주요 기능으로 설정하였다.
 그로 인해 사용한 하드웨어적 요소로는 추론 모델 학습에 이용한 RTX 3090 Ti, 카메라 기반 추론 및 추론 결과 송출용 서버로 엣지컴퓨팅용 보드인 Jetson Nano, 송출되는 추론 화면과 알람 기능을 수신 받을 클라이언트로 Galaxy A90을 사용하였고, 소프트웨어적 요소로는 인공지능 프레임워크인 PyTorch, YOLOv5(You Only Look Once), 실시간 추론 화면 및 추론 결과 송출용 웹서버 Flask, 행동 데이터를 저장할 데이터베이스인 MySQL, 이상행동 감지 시 알람 기능을 보내는 FCM(Firebase Cloud Messaging)을 사용하였다.
 전체적인 시스템의 흐름은 그림 1에서 볼 수 있듯이 서버 기능을 담당하는 Jetson Nano에서 카메라를 통해 반려동물의 행동을 탐지하며 Flask를 통해 탐지 화면을 송출하고 해당 행동을 데이터베이스에 저장하고, 이상행동이 감지되었다면 FCM을 통해 클라이언트에게 알림 메시지를 전송하고 클라이언트는 앱 푸시 알림을 통해 반려동물의 이상행동 여부를 확인할 수 있고, 또한 데이터베이스에 저장된 행동 빈도 및 시간을 확인하여 반려동물이 주로 어떤 행동을 많이 하는지 확인할 수 있게 설계하였다.

## 3. 프로젝트의 세부내용
 YOLO는 You Only Look Once의 약자로 Object detection 분야에서 많이 알려진 모델이다.
처음으로 one-stage-detection 방법을 고안하여 실시간으로 Object Detection이 가능하게 만들었다. 이미지를 여러 장으로 분할하고 CNN 모델을 이용하여 이미지를 분석하는 R-CNN 모델을 사용하여 이미지 전체를 한 번만 보는 특징이 있어 이를 활용하여 Jetson Nano라는 Tegra X1 GPU가 장착된 엣지컴퓨터용 보드에서도 빠른 속도로 운용할 수 있다.
 우리는 해당 프로젝트의 주요 기능인 YOLOv5의 실시간 추론에 사용되는 인공지능 모델을 학습시키기 위해 2022년 10월 11일부터 2022년 11월 06일까지 약 한 달간 수집한 반려동물 행동 데이터 중 약 2000장의 행동 이미지 데이터를 수집하였고, 해당 이미지 데이터들을 전처리 과정인 이미지 어노테이션(annotation)을 통해 전처리를 완료하고 모델에 학습시켰다.
 해당 프로젝트의 결과물은 기존 시장에 나와있는 상용 홈캠들 보다 인공지능을 활용하여 반려동물이 어떤 행동을 하는지 보지 않아도 알 수 있다는 점에 반려인이 외부에서 홈캠을 주기적으로 보는 등의 신경 쓰는 행동을 줄일 수 있다는 장점이 있다.
 
 ## 4. 기대효과 및 활용 방안
  한국의 반려동물 양육가구 수가 빠르게 증가함에 따라, 펫 케어 시장 규모 역시 최근 5년간 연평균 8.4%의 높은 성장세를 기록한 것을 Euromonitor의 국내 펫케어 시장 규모 현황 및 전망 자료에서 볼 수 있다. 펫 케어 산업의 성장에 따라 제품과 서비스의 종류가 늘어나고 다양한 유형으로 시장 진출이 활발하게 진행되고 있으며 반려동물의 산책 경로 추적, 생활습관 기록, 수면시간 체크 서비스 등 관련 기능을 제공하는 기술 또한 많이 개발되고 있다. 따라서 우리가 개발한 반려동물 이상행동 분석 서비스 또한 실용성, 시장성, 경제성, 사업화 가능성 모든 측면에서 기대효과가 높다.
  
## 5. 기업, 대학, 지자체 등 유관기관과의 연계·협력
 기업 애로 해결을 위해 기업에 어떤 문제가 있는지 확인 후 기업에서 원하는 방향으로 해결하기 위해 두 번에 걸쳐 회의를 했고, 반려동물의 이상행동에 대해 데이터를 쌓은 후 그 데이터를 사용자가 확인할 수 있도록 앱으로 시각화하기로 결정했다. 학습용 서버 구축을 위해 기업에 있는 컴퓨터를 빌려 사용 후 기업애로 프로젝트 테스트를 위해 과제 지원금으로 Jetson Nano를 구매해 추론용 서버를 구축하였고, 데이터베이스 서버 구축을 위한 의견을 나누고 그에 따른 테이블 구성을 했고, 애로 해결을 위한 어떤 효율적인 기술을 사용해서 인공지능 모델을 학습시킬지 의견을 나눴다. 처음에는 방대한 데이터를 구해야 한다는 생각에 인터넷에 있는 반려견 행동 유형별 데이터를 사용하려고 했으나 데이터를 이식하는 게 쉽지 않았다. 그래서 기업에서 하던 프로젝트 중 수영장의 CCTV를 이용하여 사람의 행동을 분석하고 이상행동을 분류하는 수영장 인명 구조 프로젝트가 있는데 거기에 사용한 인공지능 방식을 배워서 YOLOv5를 사용하여 직접 반려견 행동 유형별 데이터를 모으기로 하였다. 
 
 ## 6. Reference
- 조용화, 이혁재, 김영훈, Jo Yong-Hwa, Lee Hyuek-Jae, and Kim Young-Hun. "YOLO 기반 개체 검출과 Node.js 서버를 이용한 반려견 행동 분류 시스템 구현." 융합신호처리학회 논문지 (JISPS) 21.1 (2020): 29-37.
- 신민찬 ( Minchan Shin ),and 문남미 ( Nammee Moon ). "반려동물 행동 분석 기반 이상행동 예측 시스템." 한국정보처리학회 학술대회논문집 28.1 (2021): 487-490.
