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
