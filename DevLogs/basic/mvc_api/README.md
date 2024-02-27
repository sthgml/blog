# 1. Train api 통신 Context - 책임 분리

- [x]  ($TrainResultTab) 컴포넌트 분리
  - [x]  loading animation ( 3초 타이머 포함 )
- [x]  ($DataConvertTab)
  - [x]  데이터 구조 탭 및 줄바꿈 처리
  - [ ]  ~~post and convert 버튼 → 저장됨 모달 → modelName readonly로 변경~~
- [x]  ($ViewerPanel)
  - [x]  api 버튼들 side-panel-normal로 옮기기
  - [x]  api 버튼 누르면 - side-panel-expand 펼쳐지게

💡 TrainContext / TrainContextProvider 추가 → MVC 패턴 적용

![mvc패턴을 적용한 trainer pannel 화면](/images/mvc_trainer.png)

1. MVC 패턴
    1. Model: api 통신과 같은 low level 동작 관리, data 관리
    2. View: 레이아웃과 화면을 처리
    3. Controller: 유저와 직접적인 상호작용, 모델과 뷰에 명령 전달
2. 효과
    1. 버튼을 한 컴포넌트에서 관리 (SRP)
    2. UX개선: 
        1. 유저가 순차적인 작업을 한군데에서 수행 가능 
        2. 순차적으로 버튼 비활성화하여 유저가 워크플로우 진행도를 파악하기에 용이함
3. 적용화면
    1. 노드 연결 직후
        1. ![노드 연결 직후](/images/mvc_trainer_01.png)
    2. convert
        1. ![convert](/images/mvc_trainer_02.png)
    3. train 시작 이후
        1. ![train 시작 이후](/images/mvc_trainer_03.png)

|  | 역할 | Data management | .. 다른 노드들.. |
| --- | --- | --- | --- |
| ControlPanel | 노드 생성 | createNode() |  |
| Editor | 노드 표시, 노드 연결 등 작업  노드 클래스 (전달) / 컨트롤 (가공) / 컴포넌트 (-) / ++ 패널(유저 상호작용) | - selected ⇒ data.setValue(….) |  |
| SidePanel | 선택된 노드에 따라 다른 내용 표시 | 옵션 값 적용 + StroageMain (탐색기) |  |
