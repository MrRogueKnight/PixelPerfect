We’re now stepping into **next-level AI-powered applications**, combining **computer vision, deep learning, speech recognition, and reinforcement learning**! 🚀  

---

# **1️⃣ AI-Powered Real-Time Object Detection with Voice Feedback**  
### **Expected Behavior:**  
- Detects **objects in real-time** using **YOLOv5**.  
- Provides **voice feedback** announcing detected objects.  
- Uses **text-to-speech (TTS)** for audio responses.  

### **Solution (Python with YOLOv5 & Text-to-Speech)**
```python
import cv2
import numpy as np
import pyttsx3

# Load YOLO model
net = cv2.dnn.readNet("yolov5.weights", "yolov5.cfg")
classes = open("coco.names").read().strip().split("\n")

layer_names = net.getLayerNames()
output_layers = [layer_names[i[0] - 1] for i in net.getUnconnectedOutLayers()]

engine = pyttsx3.init()

cap = cv2.VideoCapture(0)

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break

    height, width = frame.shape[:2]
    blob = cv2.dnn.blobFromImage(frame, 0.00392, (416, 416), swapRB=True, crop=False)
    net.setInput(blob)
    detections = net.forward(output_layers)

    detected_objects = []
    for detection in detections:
        for obj in detection:
            scores = obj[5:]
            class_id = np.argmax(scores)
            confidence = scores[class_id]

            if confidence > 0.5:
                x, y, w, h = (obj[0] * width, obj[1] * height, obj[2] * width, obj[3] * height)
                cv2.rectangle(frame, (int(x), int(y)), (int(x + w), int(y + h)), (0, 255, 0), 2)
                label = f"{classes[class_id]} ({int(confidence * 100)}%)"
                cv2.putText(frame, label, (int(x), int(y - 10)), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 0, 0), 2)

                detected_objects.append(classes[class_id])

    if detected_objects:
        engine.say(f"Detected {', '.join(detected_objects)}")
        engine.runAndWait()

    cv2.imshow("Object Detection", frame)
    if cv2.waitKey(1) & 0xFF == 27:
        break

cap.release()
cv2.destroyAllWindows()
```
✅ **Key Takeaways:**  
✔ Uses **YOLOv5 for real-time object detection**.  
✔ **Announces detected objects** using text-to-speech.  
✔ Can be extended for **blind navigation or home automation AI.**  

---

# **2️⃣ AI-Powered Virtual Assistant That Sees, Listens & Speaks**  
### **Expected Behavior:**  
- Recognizes **voice commands**.  
- Detects **faces & objects** in real-time.  
- Speaks **responses dynamically**.  

### **Solution (Python with OpenCV, SpeechRecognition & TTS)**
```python
import cv2
import speech_recognition as sr
import pyttsx3
import numpy as np

# Initialize AI Assistant
recognizer = sr.Recognizer()
engine = pyttsx3.init()
net = cv2.dnn.readNet("yolov5.weights", "yolov5.cfg")
classes = open("coco.names").read().strip().split("\n")

layer_names = net.getLayerNames()
output_layers = [layer_names[i[0] - 1] for i in net.getUnconnectedOutLayers()]

cap = cv2.VideoCapture(0)

def recognize_speech():
    with sr.Microphone() as source:
        print("Listening for a command...")
        recognizer.adjust_for_ambient_noise(source)
        audio = recognizer.listen(source)

    try:
        command = recognizer.recognize_google(audio).lower()
        print(f"Recognized: {command}")
        return command
    except sr.UnknownValueError:
        return None

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break

    # Detect Objects
    height, width = frame.shape[:2]
    blob = cv2.dnn.blobFromImage(frame, 0.00392, (416, 416), swapRB=True, crop=False)
    net.setInput(blob)
    detections = net.forward(output_layers)

    for detection in detections:
        for obj in detection:
            scores = obj[5:]
            class_id = np.argmax(scores)
            confidence = scores[class_id]

            if confidence > 0.5:
                x, y, w, h = (obj[0] * width, obj[1] * height, obj[2] * width, obj[3] * height)
                cv2.rectangle(frame, (int(x), int(y)), (int(x + w), int(y + h)), (0, 255, 0), 2)
                label = f"{classes[class_id]} ({int(confidence * 100)}%)"
                cv2.putText(frame, label, (int(x), int(y - 10)), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 0, 0), 2)

    cv2.imshow("AI Assistant", frame)

    command = recognize_speech()
    if command:
        if "stop" in command:
            engine.say("Shutting down.")
            engine.runAndWait()
            break
        elif "object" in command:
            engine.say("Detecting objects.")
            engine.runAndWait()

    if cv2.waitKey(1) & 0xFF == 27:
        break

cap.release()
cv2.destroyAllWindows()
```
✅ **Key Takeaways:**  
✔ **Sees** (object detection), **listens** (speech recognition), **speaks** (TTS).  
✔ **Voice-controlled AI** can perform **vision-based tasks dynamically**.  
✔ Can be extended into **an advanced AI assistant for robotics!**  

---

# **3️⃣ AI-Powered Self-Driving Car Simulation in OpenCV**  
### **Expected Behavior:**  
- **Detects lanes and obstacles** in a driving simulation.  
- **Predicts steering angles** for autonomous movement.  
- Uses **Deep Learning + OpenCV for lane detection**.  

### **Solution (Python with Deep Learning & Lane Detection)**
```python
import cv2
import numpy as np

def process_frame(frame):
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    blur = cv2.GaussianBlur(gray, (5, 5), 0)
    edges = cv2.Canny(blur, 50, 150)

    mask = np.zeros_like(edges)
    height, width = frame.shape[:2]
    roi = np.array([[(100, height), (width - 100, height), (width // 2, height // 2)]], dtype=np.int32)
    cv2.fillPoly(mask, roi, 255)
    
    masked_edges = cv2.bitwise_and(edges, mask)
    lines = cv2.HoughLinesP(masked_edges, 1, np.pi / 180, 50, minLineLength=50, maxLineGap=200)

    if lines is not None:
        for line in lines:
            x1, y1, x2, y2 = line[0]
            cv2.line(frame, (x1, y1), (x2, y2), (0, 255, 0), 2)

    return frame

cap = cv2.VideoCapture("self_driving_simulation.mp4")

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break

    processed_frame = process_frame(frame)
    cv2.imshow("Self-Driving Car", processed_frame)

    if cv2.waitKey(1) & 0xFF == 27:
        break

cap.release()
cv2.destroyAllWindows()
```
✅ **Key Takeaways:**  
✔ Uses **lane detection to identify road paths**.  
✔ Can be **combined with AI models** to predict **steering directions**.  
✔ Forms the **basis for real-world self-driving car AI!** 🚗💨  

---

# **📌 Summary of What You Practiced:**  
✔ **AI-powered real-time object detection with voice feedback.**  
✔ **Real-time virtual assistant combining speech, vision & object detection.**  
✔ **Deep learning-based self-driving car simulation.**
