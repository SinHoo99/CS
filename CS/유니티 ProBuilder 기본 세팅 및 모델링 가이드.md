# 🎮 Unity 공부 기록 (2025-03-18)

## 유니티 ProBuilder 기본 세팅 및 모델링 가이드

### 0. 기본 세팅

1) ProBuilder 설치하기

- 유니티 패키지 매니저에서 ProBuilder 검색 후 설치.

- 설치 완료 후 SampleTap에서 URP 서포트 임포트.

![image](https://github.com/user-attachments/assets/6c7d5b30-56df-4d62-98a7-6e0e74c10446)

### 1. 기본 도형 만들기

1) ProBuilder 창 열기

Tools → ProBuilder → ProBuilder Window 클릭.

![image](https://github.com/user-attachments/assets/932ed89c-9d63-44af-8a17-93e1c7f087db)


2) 도형 생성하기

New Shape 버튼 클릭.

![image](https://github.com/user-attachments/assets/edf9943a-8a01-4475-9424-94250aa451b5)

Create Shape 탭에서 원하는 도형 선택 (예: Cube).

![image](https://github.com/user-attachments/assets/417683f8-2572-41d2-b599-d507d64f1216)

생성된 도형이 씬에 나타남.

![img](https://github.com/user-attachments/assets/19aa5eca-fff8-4d1d-9282-07a7285a070c)


3) 텍스처 이슈 해결

ProBuilder 사용 시 기본 텍스처(모눈종이)가 적용되지 않는다면:

현재 사용하는 Unity 버전에 맞는 서포트 패키지를 임포트하지 않았을 가능성이 높음.

만약 설치 후에도 적용되지 않는다면:

Preferences → ProBuilder → Material 탭에서 머터리얼을 변경.

![image](https://github.com/user-attachments/assets/76fcfedc-5c25-4b1e-a387-3e98c441f537)


### 2. 둥근 지붕 만들기

1) 면 선택 및 조정

Cube 생성 후 네 번째 아이콘 (Face Selection) 클릭 → 면 선택 가능.

![img (1)](https://github.com/user-attachments/assets/1a5e5c07-6577-439e-a024-9d2e03ee9ed8)

Shift 키를 누르면 면의 크기 조절 가능.

![img (2)](https://github.com/user-attachments/assets/373962ac-56a3-48c0-85a6-9c149567dad8)

큐브 오브젝트 두 개를 이어붙이지 않고도 정교한 모델링 가능.

![img (3)](https://github.com/user-attachments/assets/8156a25e-a266-40c5-a4d8-675d784d3966)

2) 크기 측정 및 조절

![image](https://github.com/user-attachments/assets/b66a9467-39e0-4613-aaf8-e529bd78fd58)

![image](https://github.com/user-attachments/assets/37dfa24f-9dec-46e5-98ae-8d482be7b935)

Tools → ProBuilder → Dimension Overlay → Show 활성화 → 오브젝트 크기 측정 가능.

3) 선 추가 및 조작



세 번째 아이콘 (Edge Selection) 선택 → 선택한 선이 노란색으로 표시됨.  
![image](https://github.com/user-attachments/assets/2d38b17e-0a0e-4b9a-bc9c-989b709c0a92)  
![image](https://github.com/user-attachments/assets/173c3cba-e452-4a78-9115-4dd24e94ca50)

![image](https://github.com/user-attachments/assets/963db1a9-35fc-4db2-b280-28ee20b885c5)

ProBuilder 창에서 Connect Edges 클릭 → 윗면에 선 추가.

![image](https://github.com/user-attachments/assets/1345fc86-7c76-40c7-a019-a6a91bad01c9)

4) Bevel(베벨) 적용하기

Bevel 기능을 이용해 가장자리(Edge)를 둥글게 깎기.

각진 형태를 자연스럽게 조정 가능.

너무 각진 부분이 있다면 Smoothing 값을 증가시켜 부드럽게 만들기.

![img (4)](https://github.com/user-attachments/assets/57c46e97-c980-44d8-b6d2-3bfc6388f915)
![img (5)](https://github.com/user-attachments/assets/0a99b197-f73d-4d47-b46b-56cc6dac247f)

### 3. 텍스처 씌우기

1) 머터리얼 생성 및 적용

새로운 Material 생성 후 원하는 텍스처 추가.

![image](https://github.com/user-attachments/assets/d5a37b25-a24b-4459-96ad-c0f5ae90d368)


모델에 적용하면 텍스처가 의도한 대로 씌워지지 않을 수도 있음.

2) UV 매핑 조정하기

![image](https://github.com/user-attachments/assets/db026e37-6e1a-4e5d-895a-d068d85c3ea1)

UV Editor 클릭 → 창이 나타남.

![image](https://github.com/user-attachments/assets/8058b904-da5f-440e-bfe0-d2b0bec39989)

각 면을 선택하면 에디터 창에서 표시됨 → 원하는 면끼리 그룹화 가능.

필요 없는 부분(색이 따로 필요 없는 부분) 따로 처리하여 깔끔한 UV 매핑 적용.

![img (6)](https://github.com/user-attachments/assets/e74dcc07-3793-4e8c-8cbd-7ff36d93dc17)

4. 완성본

![image](https://github.com/user-attachments/assets/2a55a427-3075-4ccc-a977-9e42771e2b3c)

최종적으로 모델링과 텍스처링을 완료한 후 결과물을 확인하면 됩니다.

💡 팁

Bevel 사용 시 최적화를 고려하여 너무 높은 Segments 값을 사용하지 않도록 주의하세요.

UV 매핑을 정리할 때 텍스처 왜곡이 없는지 미리 체크하세요.

Dimension Overlay 기능을 활용하여 오브젝트의 실제 크기를 쉽게 측정하세요.

