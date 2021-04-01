---
layout: page
title: Computer Vision (CV)
description: Computer Vision (CV)
keywords: Computer Vision (CV)
permalink: /ds/cv/
---

# Computer Vision (CV)

<br/>

### OpenCV

    $ pip install cmake
    $ pip install opencv-python
    $ pip install dlib

<br/>

    download face_recognition-1.3.0-py2.py3-none-any.whl:
    http://pypi.org/project/face-recognition/#files

<br/>

    $ pip install face_recognition-1.3.0-py2.py3-none-any.whl

<br/>

```
import cv2
import dlib
import face_recognition

print(cv2.__version__)
print(dlib.__version__)
print(face_recognition.__version__)
```

<br/>

```python
import cv2
import face_recognition

image_to_detect = cv2.imread('two-people.jpg');
# cv2.imshow("test", image_to_detect)

# model can be 'hog' or 'cnn'
all_face_locations = face_recognition.face_locations(image_to_detect, model="hog")

print('There are {} of faces in this image'.format(len(all_face_locations)) )

for index, current_face_location im enumerate(all_face_locations):
    top_pos, right_pos, botton_pos, left_pos = current_face_location
    print('Found face {} at top:{}, right:{}, botton:{}, left{}'.format(index+1, top_pos, right_pos, bottom_pos, left_pos))
    current_face_image = image_to_detect[top_pos:bottom_pos,left_pos:right_pos]
    cv2.imshow('Face No: ' +str(index+1), current_face_image)
```
