# Streamlit Camera

여기에서는 OpenCV와 Streamlit의 camera_input을 이용해 Streamlit에서 Camara를 제어하는것을 설명합니다. 

## OpenCV

[Live webcam feed into the web app](https://discuss.streamlit.io/t/live-webcam-feed-into-the-web-app/397)와 같이 OpenCV를 이용해 Streamlit으로 카메라 인터페이스를 만들 수 있습니다.

CV를 이용해 video capture를 해서 반복적으로 보여주는 방법으로 비디오처럼 웹캠을 보여줄 수 있습니다.

```python
import cv2
import streamlit as st

st.title("Webcam Live")

run = st.checkbox('Run')
FRAME_WINDOW = st.image([])
camera = cv2.VideoCapture(0)

while run:
    _, frame = camera.read()
    frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
    FRAME_WINDOW.image(frame)
else:
    st.write('Stopped')
```

실행화면입니다.

<img width="500" alt="image" src="https://github.com/user-attachments/assets/71b59ece-e0c3-4cc6-b23a-3793848ff617" />


cv2의 설치는 아래와 같이 진행합니다.

```text
pip install opencv-python
```

Local에서 실행하는 명령어는 아래와 같습니다.

```python
streamlit run application/app.py
```


## Streamlit

Streamlit의 [st.camera_input](https://docs.streamlit.io/develop/api-reference/widgets/st.camera_input#stcamera_input)을 이용하면 아래와 같이 구현이 가능합니다.

```python
import streamlit as st

enable = st.checkbox("Enable camera")
picture = st.camera_input("Take a picture", disabled=not enable)

if picture:
    st.image(picture)
```

구현된 화면 이미지입니다.

<img width="500" alt="image" src="https://github.com/user-attachments/assets/ee3d7688-f63c-41f0-9417-a4043e520593" />

이미지를 byte로 저장해서 활용하는 방법은 아래와 같습니다.

```python
import streamlit as st

img_file_buffer = st.camera_input("Take a picture")

if img_file_buffer is not None:
    bytes_data = img_file_buffer.getvalue()
    st.write(type(bytes_data))
```

Pillow and Numpy를 이용한 처리의 예는 아래와 같습니다.

```python
import streamlit as st
from PIL import Image
import numpy as np

img_file_buffer = st.camera_input("Take a picture")

if img_file_buffer is not None:
    img = Image.open(img_file_buffer)

    img_array = np.array(img)   # To convert PIL Image to numpy array:

    st.write(type(img_array)) # <class 'numpy.ndarray'>

    st.write(img_array.shape)  # (height, width, channels)
```

CV2를 이용한 활용의 예제입니다.

```python
import streamlit as st
import cv2
import numpy as np

img_file_buffer = st.camera_input("Take a picture")

if img_file_buffer is not None:
    bytes_data = img_file_buffer.getvalue()
    cv2_img = cv2.imdecode(np.frombuffer(bytes_data, np.uint8), cv2.IMREAD_COLOR)

    st.write(type(cv2_img))  # <class 'numpy.ndarray'>

    st.write(cv2_img.shape) # (height, width, channels)
```

## QR Detector 

[QR detector](https://github.com/blackary/streamlit-camera-input-live/blob/main/example_app%2Fstreamlit_app.py) 사용시 설치 명령어는 아래와 같습니다.

```text
pip install streamlit-camera-input-live
streamlit run application/qr_detector.py
```

이를 구현한 코드는 아래와 같습니다. QR까지 고려하다보니 속도가 늦어서 카메라에 묶어서 활용하기 어렵습니다.

```python
import cv2
import numpy as np
import streamlit as st

from camera_input_live import camera_input_live

image = camera_input_live()

if image is not None:
    st.image(image)
    bytes_data = image.getvalue()
    cv2_img = cv2.imdecode(np.frombuffer(bytes_data, np.uint8), cv2.IMREAD_COLOR)

    detector = cv2.QRCodeDetector()

    data, bbox, straight_qrcode = detector.detectAndDecode(cv2_img)

    if data:
        st.write("# Found QR code")
        st.write(data)
        with st.expander("Show details"):
            st.write("BBox:", bbox)
            st.write("Straight QR code:", straight_qrcode)
```

## Reference

[Live Webcam with Streamlit](https://z-uo.medium.com/live-webcam-with-streamlit-f32bf68945a4)
