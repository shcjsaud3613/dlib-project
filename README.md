# 환경설정
## dlib & CMake 설치
dlib는 프로그래밍 언어 C ++로 작성된 범용 크로스 플랫폼 소프트웨어 라이브러리다. 계약 및 구성 요소 기반 소프트웨어 엔지니어링에 의한 설계 아이디어에 크게 영향을 받는다. 따라서 무엇보다도 독립 소프트웨어 구성 요소의 집합이다.

CMake는 멀티플랫폼으로 사용할 수 있는 Make의 대용품을 만들기 위한 오픈소스 프로젝트로 키트웨어와 인사이트 콘솔티엄에서 만들었다. 스스로 기존의 Make의 과정을 수행하지는 않고 지정한 운영 체제에 맞는 Make 파일의 생성만을 수행하기 때문에 Meta Make라고도 불린다.

설치방법은 아래 링크를 참고한다.
https://sulastri.tistory.com/3

필자는 이렇게 했는데도 import가 안돼서 그냥 프로젝트 안에서 따로 패키지를 받았다.

## opencv & numpy 설치
OpenCV은 실시간 컴퓨터 비전을 목적으로 한 프로그래밍 라이브러리다. 원래는 인텔이 개발하였다. 실시간 이미지 프로세싱에 중점을 둔 라이브러리다. 인텔 CPU에서 사용되는 경우 속도의 향상을 볼 수 있는 IPP를 지원한다.

NumPy는 행렬이나 일반적으로 대규모 다차원 배열을 쉽게 처리 할 수 있도록 지원하는 파이썬의 라이브러리다. NumPy는 데이터 구조 외에도 수치 계산을 위해 효율적으로 구현된 기능을 제공한다.

opencv 설치방법은 아래 링크를 참고한다.
https://webnautes.tistory.com/913

필자는 opencv를 설치하니 numpy도 같이 설치됐다. 만약 numpy가 설치되지 않았다면, 아래 링크를 참고한다.
https://hyungc.tistory.com/entry/Numpy-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0-in-PyCharm


# 기능 분석
사람의 얼굴을 분석하여 68개의 점이 검출된다.

- 0~16: 턱
- 17~21: 왼쪽 눈썹
- 22~26: 오른쪽 눈썹
- 27~30: 콧등
- 31~35: 코
- 36~41: 왼쪽 눈
- 42~47: 오른쪽 눈
- 48~67: 입

# 프로젝트 개발
## VideoCapture를 통해 카메라 연결
```python
import cv2
import numpy as np
import dlib

cap = cv2.VideoCapture(0)

while True:
    _, frame = cap.read()

    cv2.imshow("Frame", frame)

    key = cv2.waitKey(1)
    if key == 27:
        break
```

위 코드를 입력하고 실행하면 아래와 같이 카메라가 촬영한 화면이 나온다.

![1](https://user-images.githubusercontent.com/74952376/109069539-b2668500-7734-11eb-8998-cdf0b890ab5e.JPG)

## get_frontal_face_detector를 통해 얼굴위치 추적
```python
import cv2
import numpy as np
import dlib

cap = cv2.VideoCapture(0)

detector = dlib.get_frontal_face_detector()

while True:
    _, frame = cap.read()
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

    faces = detector(gray)
    for face in faces:
        print(face)

    cv2.imshow("Frame", frame)

    key = cv2.waitKey(1)
    if key == 27:
        break
```

코드를 위와 같이 수정하고 실행하면 사람 얼굴 위치에 따라 콘솔 출력값이 바뀌는 것을 확인할 수 있다.

![2](https://user-images.githubusercontent.com/74952376/109069541-b397b200-7734-11eb-8faa-575b177a525f.JPG)

## rectangle을 통해 사각형 표시
위 콘솔에서 나온 결과는 얼굴 위치를 사각형 좌표값으로 표시한 값이다.

실제로 사각형을 띄워보도록 하겠다. 위에서 콘솔값을 출력한 부분을 수정하도록 하겠다.

```python
while True:
    _, frame = cap.read()
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

    faces = detector(gray)
    for face in faces:
        x1 = face.left()
        y1 = face.top()
        x2 = face.right()
        y2 = face.bottom()
        cv2.rectangle(frame, (x1, y1), (x2, y2), (0, 255, 0), 3)

    cv2.imshow("Frame", frame)

    key = cv2.waitKey(1)
    if key == 27:
        break
```

위 부분을 수정하고 실행하면 얼굴부분에 초록색 사각형이 표시되는 것을 확인할 수 있다.

![3](https://user-images.githubusercontent.com/74952376/109072057-db3c4980-7737-11eb-94e6-9061968826b2.JPG)

## 68개 점으로 landmark 찍기
이제 landmark를 통해 얼굴에서 68개 점을 추출해보도록 하겠다.

landmark를 찾기 위해서는 "shape_predictor_68_face_landmarks.dat" 파일이 필요하다. 이 파일은 아래 링크에서 받을 수 있다.
http://dlib.net/files/shape_predictor_68_face_landmarks.dat.bz2

압축을 푼 후 dat 파일을 프로젝트 폴더에 넣어준다.

![4](https://user-images.githubusercontent.com/74952376/109072049-da0b1c80-7737-11eb-8f32-0425823e5a7b.JPG)

마지막으로 전체 코드를 수정하고 실행한다.

```python
import cv2
import numpy as np
import dlib

cap = cv2.VideoCapture(0)

detector = dlib.get_frontal_face_detector()
predictor = dlib.shape_predictor("shape_predictor_68_face_landmarks.dat")

while True:
    _, frame = cap.read()
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

    faces = detector(gray)
    for face in faces:
        x1 = face.left()
        y1 = face.top()
        x2 = face.right()
        y2 = face.bottom()
        #cv2.rectangle(frame, (x1, y1), (x2, y2), (0, 255, 0), 3)

        landmarks = predictor(gray, face)

        for n in range(0, 68):
            x = landmarks.part(n).x
            y = landmarks.part(n).y
            cv2.circle(frame, (x, y), 4, (255, 0, 0), -1)


    cv2.imshow("Frame", frame)

    key = cv2.waitKey(1)
    if key == 27:
        break
```

아래와 같은 화면이 나오면 성공한 것이다.

![5](https://user-images.githubusercontent.com/74952376/109072055-db3c4980-7737-11eb-98cb-bf373e404f73.JPG)
