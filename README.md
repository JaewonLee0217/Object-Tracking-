## Object Tracking 프로젝트 기술 분석

### 📋 프로젝트 개요
이 프로젝트는 **심화전공실습 3조**에서 개발한 **자동차 주행 중 차선 및 차량 추적 시스템**입니다. OpenCV와 GSL(GNU Scientific Library) 라이브러리를 활용하여 실시간 비디오에서 차선과 차량을 동시에 감지하고 추적하는 기능을 구현했습니다.

### 🛠️ 사용된 주요 라이브러리 및 모듈

#### 1. **OpenCV (Computer Vision Library)**
- **VideoCapture**: 비디오 파일 읽기 (`data/video.mp4`)
- **VideoWriter**: 결과 비디오 저장
- **Mat**: 이미지 데이터 구조체
- **CascadeClassifier**: Haar Cascade를 이용한 차량 검출
- **HoughLinesP**: 확률적 허프 변환을 통한 직선 검출
- **cvtColor**: 색상 공간 변환 (BGR↔HSV, BGR↔GRAY)
- **inRange**: 색상 범위 기반 마스킹
- **bitwise_and**: 비트 연산을 통한 이미지 마스킹
- **addWeighted**: 이미지 블렌딩
- **GaussianBlur**: 가우시안 필터링
- **Sobel**: 엣지 검출

#### 2. **GSL (GNU Scientific Library)**
- **gsl_fit_linear**: 선형 회귀 분석을 통한 차선 추정
- **gsl_fit_linear** 함수를 사용하여 검출된 차선들의 기울기와 절편을 계산

### 🔧 구현된 핵심 기능들

#### 1. **차량 검출 (Vehicle Detection)**
```cpp
// Haar Cascade를 이용한 차량 검출
CascadeClassifier car_cascade;
car_cascade.load("haarcascade_cars.xml");
car_cascade.detectMultiScale(frame, cars, 1.3, 2);
```
- **Haar Cascade 분류기**를 사용하여 실시간 차량 검출
- 검출된 차량에 색상 기반 사각형 표시
- 차량 중심점 계산 및 색상 분석

#### 2. **차선 검출 (Lane Detection)**
```cpp
// 색상 기반 차선 추출
color_detect(frame, img_filtered);
// Canny 엣지 검출
Apply_Cannyf(img_filtered, img_edges);
// 관심 영역 설정
SetRegion(img_edges, height, width);
// 허프 변환으로 직선 검출
HoughLinesP(uImage_edges, lines, rho, theta, hough_threshold, minLineLength, maxLineGap);
```

**차선 검출 파이프라인:**
1. **색상 필터링**: 흰색과 노란색 차선만 추출
2. **Canny 엣지 검출**: 
   - 가우시안 필터링
   - Sobel 연산자 (수직/수평)
   - Non-Maximum Suppression
   - Double Thresholding
   - Hysteresis
3. **관심 영역 설정**: 사다리꼴 모양의 ROI 설정
4. **허프 변환**: 직선 검출
5. **선형 회귀**: GSL을 이용한 차선 추정

#### 3. **색상 공간 변환**
```cpp
// RGB to HSV 변환 (수동 구현)
void RGB2HSV(Mat img_in, Mat& hsv_img)
```
- RGB에서 HSV로 색상 공간 변환을 수동으로 구현
- 노란색 차선 검출을 위한 HSV 색상 범위 설정

#### 4. **관심 영역 (ROI) 설정**
```cpp
// 사다리꼴 모양의 관심 영역 설정
Mat SetRegion(Mat img_edges, int height, int width)
```
- 주행 차선이 위치할 가능성이 높은 영역만 처리
- 계산 효율성 향상 및 노이즈 제거

#### 5. **선형 회귀를 통한 차선 추정**
```cpp
// GSL 라이브러리를 이용한 선형 회귀
gsl_fit_linear(right_lines_x, 1, right_lines_y, 1, right_index,
               &c0, &c1, &cov00, &cov01, &cov11, &sumsq);
```
- 검출된 여러 직선들을 하나의 차선으로 통합
- 좌측/우측 차선을 각각 추정
- 기울기와 절편을 계산하여 연속적인 차선 표시

### �� 기술적 특징

#### 1. **실시간 처리**
- 비디오 스트림을 프레임별로 실시간 처리
- 효율적인 알고리즘으로 실시간 성능 확보

#### 2. **다중 객체 추적**
- 차량과 차선을 동시에 검출 및 추적
- 각각 다른 알고리즘 적용 (Haar Cascade vs Canny + Hough)

#### 3. **강건성 (Robustness)**
- 색상 기반 필터링으로 조명 변화에 강함
- 관심 영역 설정으로 불필요한 정보 제거
- 선형 회귀를 통한 노이즈 제거

#### 4. **컴퓨터 비전 기법 활용**
- **엣지 검출**: Canny 알고리즘
- **기하학적 변환**: 허프 변환
- **패턴 인식**: Haar Cascade
- **수치 분석**: GSL을 이용한 선형 회귀

### 📊 프로젝트 구조
```
Main/
├── main.cpp          # 메인 실행 파일
├── EdgeExtract.*     # Canny 엣지 검출 구현
├── Converter.*       # 색상 공간 변환
├── Region.*          # 관심 영역 설정
├── Drawlines.*       # 차선 그리기 및 선형 회귀
├── HaarCascades.*    # 차량 검출
└── data/            # 테스트 비디오 파일들
```

이 프로젝트는 **자율주행 기술의 핵심 요소**인 차선 인식과 차량 검출을 통합한 실용적인 시스템으로, OpenCV와 GSL 라이브러리의 다양한 기능을 효과적으로 활용하여 구현되었습니다.

기여점 1: 실시간 다중 객체 추적 시스템의 통합 구현
이 프로젝트는 자율주행 기술의 핵심 요소인 차선 인식과 차량 검출을 단일 시스템으로 통합하여 구현한 점이 가장 큰 기여점입니다. 기존의 대부분 연구들이 차선 인식과 차량 검출을 별도로 다루었던 것과 달리, 이 프로젝트는 OpenCV의 VideoCapture를 통해 실시간 비디오 스트림을 받아들여 동시에 두 가지 작업을 수행합니다. 차량 검출에는 Haar Cascade 분류기를 활용하고, 차선 검출에는 Canny 엣지 검출과 허프 변환을 조합한 파이프라인을 구축하여 각각의 특성에 최적화된 알고리즘을 적용했습니다. 특히 두 시스템의 결과를 addWeighted 함수를 통해 시각적으로 통합하여 운전자가 한눈에 인식할 수 있는 직관적인 인터페이스를 제공한 점이 실용적 가치를 높였습니다.
기여점 2: 수동 구현을 통한 컴퓨터 비전 알고리즘의 심화 이해
프로젝트의 두 번째 기여점은 OpenCV의 내장 함수에만 의존하지 않고 핵심 알고리즘들을 수동으로 구현하여 컴퓨터 비전의 원리를 깊이 있게 이해했다는 점입니다. EdgeExtract.cpp에서는 Canny 엣지 검출 알고리즘의 전체 파이프라인을 단계별로 구현했습니다. 가우시안 필터링, Sobel 연산자(수직/수평), Non-Maximum Suppression, Double Thresholding, Hysteresis 등의 각 단계를 직접 코딩하여 엣지 검출의 수학적 원리를 체험했습니다. 또한 Converter.cpp에서는 RGB에서 HSV로의 색상 공간 변환을 수동으로 구현하여 색상 이론의 수학적 기반을 이해했습니다. 이러한 수동 구현을 통해 단순히 라이브러리 사용법을 익히는 것을 넘어서 컴퓨터 비전 알고리즘의 내부 동작 원리와 각 단계의 중요성을 체계적으로 학습할 수 있었습니다.
기여점 3: GSL 라이브러리를 활용한 수치 분석 기법의 적용
세 번째 기여점은 GNU Scientific Library(GSL)를 활용하여 검출된 차선 데이터를 수치 분석적으로 처리한 점입니다. Drawlines.cpp에서 구현된 선형 회귀 분석은 단순히 검출된 직선들을 시각적으로 표시하는 것을 넘어서 수학적으로 정확한 차선을 추정합니다. gsl_fit_linear 함수를 사용하여 검출된 여러 직선들의 좌표 데이터로부터 최적의 직선 방정식을 계산하고, 이를 통해 좌측과 우측 차선을 각각 독립적으로 모델링합니다. 이 과정에서 노이즈가 포함된 검출 결과들을 통계적으로 처리하여 안정적이고 연속적인 차선을 생성할 수 있었습니다. 또한 사다리꼴 모양의 관심 영역(ROI) 설정을 통해 계산 효율성을 높이고, 색상 기반 필터링을 통해 흰색과 노란색 차선만을 선별적으로 검출하는 등 실용성을 고려한 최적화 기법들을 적용하여 실제 주행 환경에서의 강건성을 확보했습니다.

