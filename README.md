# Streamlit Camera

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

<img width="699" alt="image" src="https://github.com/user-attachments/assets/63ca9cf1-64f1-47a3-aefb-f33f23643b5a" />



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

[Live webcam feed into the web app](https://discuss.streamlit.io/t/live-webcam-feed-into-the-web-app/397)
