Awesome! Let’s tackle these **advanced challenges** one by one. These exercises will take your OpenCV skills to a **professional level!** 🚀  

---

## **1️⃣ Implement a Video Filter that Switches Between Grayscale, Sepia, and Edge Detection**  
### **Expected Behavior:**  
- The user **presses keys** to switch between different filters:  
  - `1` → **Normal Color Mode**  
  - `2` → **Grayscale**  
  - `3` → **Sepia Effect**  
  - `4` → **Edge Detection (Canny)**  
- `Esc` **exits** the program.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

void applySepia(IplImage* img) {
    for (int y = 0; y < img->height; y++) {
        uchar* row = (uchar*)(img->imageData + y * img->widthStep);
        for (int x = 0; x < img->width; x++) {
            uchar b = row[x * 3 + 0];
            uchar g = row[x * 3 + 1];
            uchar r = row[x * 3 + 2];

            row[x * 3 + 0] = cv::saturate_cast<uchar>(0.272 * r + 0.534 * g + 0.131 * b); // Blue
            row[x * 3 + 1] = cv::saturate_cast<uchar>(0.349 * r + 0.686 * g + 0.168 * b); // Green
            row[x * 3 + 2] = cv::saturate_cast<uchar>(0.393 * r + 0.769 * g + 0.189 * b); // Red
        }
    }
}

int main() {
    CvCapture* capture = cvCreateCameraCapture(0);
    if (!capture) {
        printf("Error: Could not access webcam\n");
        return -1;
    }

    IplImage *frame, *gray, *edges;
    int mode = 1;  // Default mode: Normal

    while ((frame = cvQueryFrame(capture)) != NULL) {
        if (mode == 2) {
            gray = cvCreateImage(cvGetSize(frame), IPL_DEPTH_8U, 1);
            cvCvtColor(frame, gray, CV_BGR2GRAY);
            cvShowImage("Filter Mode", gray);
            cvReleaseImage(&gray);
        } 
        else if (mode == 3) {
            applySepia(frame);
            cvShowImage("Filter Mode", frame);
        } 
        else if (mode == 4) {
            edges = cvCreateImage(cvGetSize(frame), IPL_DEPTH_8U, 1);
            cvCvtColor(frame, edges, CV_BGR2GRAY);
            cvCanny(edges, edges, 50, 150, 3);
            cvShowImage("Filter Mode", edges);
            cvReleaseImage(&edges);
        } 
        else {
            cvShowImage("Filter Mode", frame); // Normal mode
        }

        char key = cvWaitKey(30);
        if (key == 27) break;   // Exit on 'Esc'
        if (key == '1') mode = 1;
        if (key == '2') mode = 2;
        if (key == '3') mode = 3;
        if (key == '4') mode = 4;
    }

    cvReleaseCapture(&capture);
    cvDestroyWindow("Filter Mode");
    return 0;
}
```

✅ **Key Takeaways:**  
✔ **`cvCvtColor()`** converts the image to grayscale.  
✔ **Sepia effect uses a color transformation formula.**  
✔ **Canny edge detection** is applied using `cvCanny()`.  

---

## **2️⃣ Add Motion Detection Overlay to Highlight Moving Objects**  
### **Expected Behavior:**  
- The program detects **motion in a live webcam feed**.  
- Moving objects are **highlighted in white on a black background**.  
- `Esc` **exits** the program.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    CvCapture* capture = cvCreateCameraCapture(0);
    if (!capture) {
        printf("Error: Could not access webcam\n");
        return -1;
    }

    IplImage *frame, *gray, *prevGray, *diffImg;
    
    frame = cvQueryFrame(capture);
    gray = cvCreateImage(cvGetSize(frame), IPL_DEPTH_8U, 1);
    prevGray = cvCreateImage(cvGetSize(frame), IPL_DEPTH_8U, 1);
    diffImg = cvCreateImage(cvGetSize(frame), IPL_DEPTH_8U, 1);

    cvCvtColor(frame, prevGray, CV_BGR2GRAY);

    while ((frame = cvQueryFrame(capture)) != NULL) {
        cvCvtColor(frame, gray, CV_BGR2GRAY);
        cvAbsDiff(gray, prevGray, diffImg);
        cvThreshold(diffImg, diffImg, 30, 255, CV_THRESH_BINARY); // Highlight movement

        cvShowImage("Motion Detection", diffImg);
        cvCopy(gray, prevGray);  // Store current frame as previous frame

        if (cvWaitKey(30) == 27) break;  // Exit on 'Esc'
    }

    cvReleaseCapture(&capture);
    cvReleaseImage(&gray);
    cvReleaseImage(&prevGray);
    cvReleaseImage(&diffImg);
    cvDestroyWindow("Motion Detection");
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvAbsDiff()` **detects differences between consecutive frames**.  
✔ `cvThreshold()` **highlights moving areas in white**.  
✔ **Stores previous frame and compares it with the current frame.**  

---

## **3️⃣ Create a GUI with Buttons to Switch Between Camera Effects**  
### **Expected Behavior:**  
- Clicking a button switches the effect (`Grayscale`, `Sepia`, `Edge Detection`).  
- `Esc` exits the program.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

CvCapture* capture;
IplImage* frame;
int effectMode = 1;  // Default: Normal

void onMouse(int event, int x, int y, int flags, void* param) {
    if (event == CV_EVENT_LBUTTONDOWN) {
        if (x < 100) effectMode = 1; // Normal
        else if (x < 200) effectMode = 2; // Grayscale
        else if (x < 300) effectMode = 3; // Sepia
        else if (x < 400) effectMode = 4; // Edge Detection
    }
}

void applySepia(IplImage* img) {
    for (int y = 0; y < img->height; y++) {
        uchar* row = (uchar*)(img->imageData + y * img->widthStep);
        for (int x = 0; x < img->width; x++) {
            uchar b = row[x * 3 + 0];
            uchar g = row[x * 3 + 1];
            uchar r = row[x * 3 + 2];

            row[x * 3 + 0] = cv::saturate_cast<uchar>(0.272 * r + 0.534 * g + 0.131 * b);
            row[x * 3 + 1] = cv::saturate_cast<uchar>(0.349 * r + 0.686 * g + 0.168 * b);
            row[x * 3 + 2] = cv::saturate_cast<uchar>(0.393 * r + 0.769 * g + 0.189 * b);
        }
    }
}

int main() {
    capture = cvCreateCameraCapture(0);
    if (!capture) {
        printf("Error: Could not access webcam\n");
        return -1;
    }

    cvNamedWindow("Camera Effects");
    cvSetMouseCallback("Camera Effects", onMouse, NULL);

    while ((frame = cvQueryFrame(capture)) != NULL) {
        if (effectMode == 2) cvCvtColor(frame, frame, CV_BGR2GRAY);
        if (effectMode == 3) applySepia(frame);
        if (effectMode == 4) cvCanny(frame, frame, 50, 150, 3);

        cvShowImage("Camera Effects", frame);
        if (cvWaitKey(30) == 27) break;
    }

    cvReleaseCapture(&capture);
    cvDestroyWindow("Camera Effects");
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvSetMouseCallback()` **detects button clicks to change effects**.  
✔ **Different effects (grayscale, sepia, edges) apply dynamically.**  

---

## **📌 Summary of What You Practiced:**  
✔ **Live video filters (grayscale, sepia, edge detection).**  
✔ **Motion detection using frame differences.**  
✔ **Mouse interaction for GUI-based effect switching.**  