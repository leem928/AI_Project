# 딥러닝 미세표정 분류 시스템 개발 🙂

<img src="https://github.com/user-attachments/assets/f6ccd7c3-b502-4381-a8ce-e2278363e656" alt="미세표정 감정 분석 예시" width="500"/><br><br>

## 1. 주제
미세표정은 사람이 감정을 숨기거나 무의식적으로 드러낼 때, 아주 짧은 시간 동안 얼굴에 나타나는 비자발적 표정을 말함

본 프로젝트는 미세표정 기반 딥러닝 감정 인식 모델을 개발, 업로드된 비디오를 분석하여 초 단위로 미세표정의 발생 시점 및 유형을 자동으로 검출·보고하는 웹사이트 개발을 목표<br><br>

## 2. 플로우 차트
두개의 CNN을 각각 학습 시킨 뒤 Softmax Score Esemble 해서 최종 성능이 나옴

<img src="https://github.com/user-attachments/assets/71627e2d-183e-42f3-8c28-c0c9280ccaaf" alt="미세표정 감정 분석 예시" width="500"/><br><br>

## 3. 데이터셋
**고속 영상 기반 미세표정 데이터셋**  


26명 피실험자, 247개 표정 시퀀스 (200fps 촬영)  
감정 클래스: 7개  

OnsetFrame: 감정 표현이 시작되는 프레임  
ApexFrame: 감정이 가장 강하게 표현되는 프레임  
OffsetFrame: 감정 표현이 종료되는 프레임  
Estimated Emotion: 추정되는 감정  

<img src="https://github.com/user-attachments/assets/3599d6e6-fd96-47bd-ada0-1bb918d1ada5" alt="미세표정 감정 분석 예시" width="500"/><br><br>

## 4. 전처리
**1) Others, Fear 제거**


Others 제거: 정의가 모호하고 학습에 혼란을 줄 수 있음  
Fear 제거: 샘플수가 매우 적고 클래스 불균형 심화  

happiness, surprise, repression, disgust, sadness
→ 총 5개의 감정 클래스 사용

<img src="https://github.com/user-attachments/assets/cbc20e89-99d9-444e-a3a4-45fb9b0b2ada" alt="미세표정 감정 분석 예시" width="500"/><br><br>


**2) ApexFrame 추출, Optical Flow 계산**


Optical Flow: 연속된 두 영상 프레임 사이에서 픽셀의 움직임을 벡터 형태로 추정하는 기술  

본 프로젝트에서는 OnsetFrame → ApexFrame 간의 Optical Flow를 사용  
필요한 이유: 미세한 움직임 포착, 정지 이미지 한계 극복, 움직임 기반 Feature 제공

<img src="https://github.com/user-attachments/assets/11906c97-084d-4be1-ab06-6b3fdb887c3e" alt="미세표정 감정 분석 예시" width="500"/><br><br>

**3) ApexFrame + CLAHE, Histogram Equalization**


Histogram Equalization : 전체 얼굴 대비 향상으로 흐릿한 표정 변화도 인식 가능  
CLAHE: 노이즈 억제로 미세한 근육 변화를 정확히 감지 가능  

히스토그램 평활화 성능: 61.29%  
CLAHE 성능: 64.52%  

히스토그램 평활화는 성능이 떨어짐  
CLAHE는 ApexFrame과 성능이 같지만 과적합이 발생함  

 **→ 둘다 미적용**


<img src="https://github.com/user-attachments/assets/50439549-4697-485d-b517-8c5f66590382" alt="미세표정 감정 분석 예시" width="500"/><br><br>

## 5. 모델
**ApexFrame 모델 선정**

<img src="https://github.com/user-attachments/assets/b1cd0a91-f452-42eb-81ea-827b4dfe9ff1" alt="미세표정 감정 분석 예시" width="500"/><br><br>

**Optical Flow 모델 선정**
  
<img src="https://github.com/user-attachments/assets/6ecac4ee-8706-4c90-93ed-429042b0b94a" alt="미세표정 감정 분석 예시" width="500"/><br><br>

**SimpleCNN 적용**
  
Apex Frame 성능 : 64.52  
Optical Flow 성능 : 83.87  


<p align="center">
  <img src="https://github.com/user-attachments/assets/3a25b515-ea5b-4448-9b53-603c623beb6b" width="300"/>
  <img src="https://github.com/user-attachments/assets/3824a480-1d39-42c5-a92d-650e06bb1a1d" width="300"/>
  <img src="https://github.com/user-attachments/assets/d4cf298d-d469-4e0c-a2c2-90e1dca779ca" width="300"/>
</p><br><br>

**Data Agmentation**
  
<img src="https://github.com/user-attachments/assets/e413a4d9-38a7-47b3-a101-737c5777fc1f" alt="미세표정 감정 분석 예시" width="500"/><br><br>

**CBAM 적용**
  
CBAM (Convolutional Block Attention Module)  
: 이미지에서 중요한 부분에 더 집중해서 성능을 높이는 모듈  

Apex baseline에 적용 → Accuracy: 45.16%  
Optical Flow에 적용 → Accuracy: 51.61%  


CBAM 적용 시 성능 급락 원인  

1. 데이터셋 크기 부족  
2. 학습 불안정 (복잡도 증가)  


<img src="https://github.com/user-attachments/assets/4a53c194-7ec4-48cc-9d26-f8e1cad06a53" alt="미세표정 감정 분석 예시" width="500"/>

**Softmax Score Ensemble**

Softmax Score Ensemble 결과  
성능: 95.42%  

성능이 확 높아진 원인  

1. 레이블 불균형  
2. 모델 간 예측 경향 유사  
3. 클래스 간 경계 모호성  

<img src="https://github.com/user-attachments/assets/b7e9cc79-1614-4a22-83f4-c99ead0de61e" alt="미세표정 감정 분석 예시" width="500"/>

**결론**

현재 데이터셋의 수가 적고 불균형 및 다양성 부족으로 인해 편향과 일반화 한계가 나타남  
추후 다양한 데이터셋을 추가해 성능 보완 필요<br><br>

## 6. Flask 제작
**첫페이지**  

1. 비디오로 분석  
 - 비디오를 업로드하여 대표 프레임을 추출하고 학습된 딥러닝 모델로 감정을 예측함   

2. 웹캠으로 분석  
 - 실시간 웹캠 영상을 입력받아 0.4초 단위로 감정을 예측하고 화면에 출력함  

<img src="https://github.com/user-attachments/assets/6e5dd9d1-f3a9-4981-a9d6-c6c28a470806" alt="미세표정 감정 분석 예시" width="500"/><br><br>

**1. 비디오로 분석 페이지**  

(1) 분석 대상 얼굴을 확대한 대표 프레임 추출  

(2) 표정이 담긴 얼굴 영상 재생  

(3) 미세표정 통계  
	- 총 감정 변화 횟수  
	- 미세표정 횟수  
	- 비율 % 제공  

(4) 감정 변화 로그 - 감정 레이블 / 이모지 / 지속 시간(초) 표시  

<img src="https://github.com/user-attachments/assets/6c063185-4e1e-4184-a784-e1dd1c1689a2" alt="미세표정 감정 분석 예시" width="500"/><br><br>

**2. 웹캠으로 분석 페이지**

<img src="https://github.com/user-attachments/assets/f8a21f85-b13f-40df-abc7-3c9b84b826c9" alt="미세표정 감정 분석 예시" width="500"/><br><br>

## 7. 활용 방안 및 기대효과

심리상담 및 임상 진단  
	- 내담자 또는 환자의 미세표정을 통해 감정의 진위 파악  
	- 치료 반응의 정량적 모니터링 가능  

2.  인터뷰 및 채용 분야  
	- 지원자의 진짜 감정(불안, 억제 등)을 미세표정으로 분석하여 신뢰성 보완  

3.  온라인 교육 플랫폼  
	- 수강생의 몰입도나 이해도 등을 미세표정 기반으로 분석하여 피드백 제공  


보안, 의료, 교육, 채용 등 다양한 분야에 유연하게 적용 가능  
감정 변화에 따른 피드백 제공으로 사용자 몰입도 및 만족도 향상  



