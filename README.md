# dotLumen – Assistive Mobility System for the Visually Impaired

This project was developed as part of a software challenge at **dotLumen**, a company creating wearable mobility systems that empower visually impaired individuals.  
The goal was to prototype a system that uses **RGB and depth video** from Intel RealSense D455 sensors to detect relevant objects in the environment and provide **audio feedback** describing what’s ahead.

---

## 🎯 Project Overview

The challenge consisted of analysing video footage (RGB and Depth, 848×480 @30 FPS) captured by a dotLumen Glasses prototype while walking through Cluj-Napoca.  
The task was to:
1. Propose **five meaningful feature ideas** based on this data.  
2. **Implement one** feature as a proof-of-concept using at least one frame.

My implemented idea combines:
- **Crosswalk detection**
- **Object recognition** (cars, people, vehicles)
- **Audio feedback** announcing what’s detected
- Basic **distance estimation** using the depth stream

---

## 💡 Feature Ideas

1. Direction awareness — determine if obstacles are on the left, centre, or right side  
2. Audio feedback for nearby obstacles  
3. Distance estimation from the user to detected objects  
4. Dangerous object detection (vehicles, crosswalks, other people)  
5. Text-to-speech reading for signs or bills  

The implemented PoC focused on **#2, #3, and #4** combined into a single pipeline.

---

## 🧠 Implementation Summary

| Step | Description | Tools |
|------|--------------|-------|
| **Frame Extraction** | Extracted the 1:43 timestamp where a crosswalk, vehicles, and people are visible | OpenCV |
| **Crosswalk Detection** | Converted RGB to grayscale → threshold → Canny edges → Hough line transform to detect parallel white stripes | OpenCV |
| **Object Detection** | Used **YOLOv8 (small)** model for identifying cars, people, and buses | Ultralytics YOLOv8 |
| **Distance Estimation** | Used depth frame scaling to approximate object distance | NumPy, OpenCV |
| **Audio Output** | Generated spoken feedback such as *“Crosswalk ahead. 4 cars ahead. 2 persons ahead.”* | Google Text-to-Speech (gTTS) |

---

## 🧩 Sample Pipeline

```python
# Simplified workflow
frame = cv2.imread("frame_1_43.png")
gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
edges = cv2.Canny(gray, 100, 200)
lines = cv2.HoughLinesP(edges, 1, np.pi/180, 50, minLineLength=100, maxLineGap=10)

# YOLOv8 detection
from ultralytics import YOLO
model = YOLO("yolov8s.pt")
results = model(frame)

# Audio feedback
from gtts import gTTS
tts = gTTS("Crosswalk ahead. 4 cars ahead. 2 persons ahead.")
tts.save("feedback.mp3")
```
## 📊 Results

- **Crosswalk detection:** Successfully detected 5 parallel white lines using edge and line transforms.
- **Object detection:** YOLOv8 (small) correctly identified 3 cars, 1 bus, and 3 people in the selected frame.
- **Audio feedback:** gTTS generated clear spoken output describing detected objects.
- **Depth estimation:** Implemented basic distance scaling using the depth frame (limited accuracy).
- **Overall outcome:** Functional proof of concept demonstrating real-time environmental awareness for assistive mobility systems.

**Example Output:**
> “Crosswalk ahead. 4 cars ahead. 2 persons ahead. One truck ahead.”

## 🧰 Tools & Libraries

**Development Environment**
- Python 3 (Google Colab)
- Jupyter / Colab Notebooks for reproducibility

**Core Libraries**
- `OpenCV` – for video processing, grayscale conversion, edge and line detection  
- `NumPy`, `pandas` – for data handling and matrix operations  
- `Ultralytics YOLOv8` – for deep learning object detection  
- `gTTS` – for text-to-speech feedback generation  
- `Matplotlib` – for visualising detection results  

**Hardware Context**
- Intel RealSense D455 (RGB + Depth video @ 848×480, 30 FPS)
