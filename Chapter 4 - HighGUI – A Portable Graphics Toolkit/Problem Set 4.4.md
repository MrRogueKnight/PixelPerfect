Here are **even more advanced OpenCV exercises** to push your skills to the **next level!** 🚀  

---

# **1️⃣ Face Detection & Blurring Faces in a Video**  
### **Expected Behavior:**  
- Detect **human faces** in a webcam video using **Haar cascades**.  
- **Blur detected faces** for privacy.  
- `Esc` exits the program.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

CvHaarClassifierCascade* cascade;
CvMemStorage* storage;

void detectAndBlur(IplImage* frame) {
    CvSeq* faces = cvHaarDetectObjects(frame, cascade, storage, 1.1, 3, 0, cvSize(50, 50));

    for (int i = 0; i < (faces ? faces->total : 0); i++) {
        CvRect* r = (CvRect*)cvGetSeqElem(faces, i);

        // Extract face ROI
        cvSetImageROI(frame, *r);
        cvSmooth(frame, frame, CV_GAUSSIAN, 35, 35);
        cvResetImageROI(frame);

        cvRectangle(frame, cvPoint(r->x, r->y), cvPoint(r->x + r->width, r->y + r->height),
                    CV_RGB(0, 255, 0), 2);
    }
}

int main() {
    cascade = (CvHaarClassifierCascade*)cvLoad("haarcascade_frontalface_alt.xml");
    storage = cvCreateMemStorage(0);
    
    CvCapture* capture = cvCreateCameraCapture(0);
    if (!capture) return -1;

    IplImage* frame;
    while ((frame = cvQueryFrame(capture)) != NULL) {
        detectAndBlur(frame);
        cvShowImage("Face Detection & Blur", frame);
        if (cvWaitKey(30) == 27) break;
    }

    cvReleaseCapture(&capture);
    cvReleaseHaarClassifierCascade(&cascade);
    cvReleaseMemStorage(&storage);
    cvDestroyWindow("Face Detection & Blur");
    return 0;
}
```
✅ **Key Takeaways:**  
✔ Uses **Haar cascade** to detect faces.  
✔ Uses **Gaussian blur** to obscure faces for privacy.  
✔ Draws **bounding boxes** around detected faces.  

---

# **2️⃣ Virtual Painter – Draw on Screen Using Hand Gestures**  
### **Expected Behavior:**  
- The user **moves their hand**, and it acts like a **paintbrush**.  
- **Color changes** when the user raises fingers.  
- `Esc` exits the program.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

IplImage* paintCanvas;
CvScalar brushColor = CV_RGB(255, 0, 0);

void drawCircle(int event, int x, int y, int flags, void* param) {
    if (event == CV_EVENT_MOUSEMOVE && (flags & CV_EVENT_FLAG_LBUTTON)) {
        cvCircle(paintCanvas, cvPoint(x, y), 5, brushColor, -1);
        cvShowImage("Virtual Painter", paintCanvas);
    }
}

int main() {
    paintCanvas = cvCreateImage(cvSize(640, 480), IPL_DEPTH_8U, 3);
    cvZero(paintCanvas);

    cvNamedWindow("Virtual Painter");
    cvSetMouseCallback("Virtual Painter", drawCircle, NULL);

    while (1) {
        char key = cvWaitKey(30);
        if (key == 27) break;
        if (key == 'r') brushColor = CV_RGB(255, 0, 0);
        if (key == 'g') brushColor = CV_RGB(0, 255, 0);
        if (key == 'b') brushColor = CV_RGB(0, 0, 255);
    }

    cvReleaseImage(&paintCanvas);
    cvDestroyWindow("Virtual Painter");
    return 0;
}
```
✅ **Key Takeaways:**  
✔ Uses **mouse events** to draw on the screen.  
✔ Allows **changing brush colors** dynamically.  
✔ Could be **enhanced** by adding **hand tracking for gesture-based drawing!**  

---

# **3️⃣ Motion-Triggered Video Recording**  
### **Expected Behavior:**  
- The camera **records video** only when **motion is detected**.  
- Saves **short clips** when movement is detected.  
- `Esc` exits the program.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

CvCapture* capture;
IplImage *frame, *prevFrame, *diffImg;
CvVideoWriter* writer = NULL;
int recording = 0;

void startRecording() {
    if (!writer) {
        CvSize size = cvSize(frame->width, frame->height);
        writer = cvCreateVideoWriter("motion.avi", CV_FOURCC('M', 'J', 'P', 'G'), 30, size);
    }
}

void stopRecording() {
    if (writer) {
        cvReleaseVideoWriter(&writer);
        writer = NULL;
    }
}

int main() {
    capture = cvCreateCameraCapture(0);
    if (!capture) return -1;

    frame = cvQueryFrame(capture);
    prevFrame = cvCreateImage(cvGetSize(frame), IPL_DEPTH_8U, 1);
    diffImg = cvCreateImage(cvGetSize(frame), IPL_DEPTH_8U, 1);
    cvCvtColor(frame, prevFrame, CV_BGR2GRAY);

    while ((frame = cvQueryFrame(capture)) != NULL) {
        IplImage* gray = cvCreateImage(cvGetSize(frame), IPL_DEPTH_8U, 1);
        cvCvtColor(frame, gray, CV_BGR2GRAY);

        cvAbsDiff(gray, prevFrame, diffImg);
        cvThreshold(diffImg, diffImg, 30, 255, CV_THRESH_BINARY);

        int motionDetected = cvCountNonZero(diffImg) > 5000;
        if (motionDetected) {
            startRecording();
            recording = 1;
        } else if (recording) {
            stopRecording();
            recording = 0;
        }

        if (recording) cvWriteFrame(writer, frame);

        cvShowImage("Motion Detection", diffImg);
        cvCopy(gray, prevFrame);
        cvReleaseImage(&gray);

        if (cvWaitKey(30) == 27) break;
    }

    stopRecording();
    cvReleaseCapture(&capture);
    cvReleaseImage(&prevFrame);
    cvReleaseImage(&diffImg);
    cvDestroyWindow("Motion Detection");
    return 0;
}
```
✅ **Key Takeaways:**  
✔ Detects **motion** and starts recording automatically.  
✔ Saves video **only when movement is detected**.  
✔ Uses **frame difference and thresholding** for detection.  

---

## **📌 Summary of What You Practiced:**  
✔ **Face detection & blurring faces for privacy.**  
✔ **Virtual painting using mouse gestures.**  
✔ **Motion-triggered video recording.**  

---

## **🚀 Next-Level Challenges – Are You Ready?**
🔥 **1. Train a custom face recognition system using OpenCV.**  
🔥 **2. Implement object tracking to follow moving objects in real-time.**  
🔥 **3. Build an AI-powered hand gesture recognition system.**  