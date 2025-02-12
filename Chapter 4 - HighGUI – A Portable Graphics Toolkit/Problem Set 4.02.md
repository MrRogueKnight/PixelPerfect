Here are the **solutions** for the advanced challenges. These will take your OpenCV skills to the next level! 🚀  

---

## **1️⃣ Modify the video player to play videos in slow-motion or fast-forward.**  
### **Expected Behavior:**  
- The user **presses keys** to control playback speed:  
  - `1` → **Normal speed** (30 FPS).  
  - `2` → **Slow-motion** (10 FPS).  
  - `3` → **Fast-forward** (60 FPS).  
- `Esc` **exits** the program.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    CvCapture* capture = cvCreateFileCapture("video.avi");
    if (!capture) {
        printf("Error: Could not load video\n");
        return -1;
    }

    IplImage* frame;
    int delay = 30; // Default FPS (30ms per frame)

    while (1) {
        frame = cvQueryFrame(capture);
        if (!frame) break;

        cvShowImage("Video Player", frame);
        char key = cvWaitKey(delay);

        if (key == 27) break;   // Exit on 'Esc'
        if (key == '1') delay = 30;  // Normal speed
        if (key == '2') delay = 100; // Slow-motion
        if (key == '3') delay = 10;  // Fast-forward
    }

    cvReleaseCapture(&capture);
    cvDestroyWindow("Video Player");
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvWaitKey(delay)` **controls playback speed dynamically**.  
✔ `if (key == '2') delay = 100;` **slows down video**.  
✔ `if (key == '3') delay = 10;` **fast-forwards video**.  

---

## **2️⃣ Allow the user to adjust video brightness using a trackbar.**  
### **Expected Behavior:**  
- The user **adjusts brightness in real-time** using a trackbar.  
- The brightness value ranges from **0 (darkest) to 100 (brightest).**  
- `Esc` exits the program.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

CvCapture* capture;
IplImage* frame, *modified;
int brightness = 50;  // Default brightness level

void on_trackbar(int value) {
    cvConvertScale(frame, modified, 1.0, value - 50); // Adjust brightness
    cvShowImage("Video Brightness", modified);
}

int main() {
    capture = cvCreateCameraCapture(0);
    if (!capture) {
        printf("Error: Could not access webcam\n");
        return -1;
    }

    cvNamedWindow("Video Brightness", CV_WINDOW_AUTOSIZE);
    cvCreateTrackbar("Brightness", "Video Brightness", &brightness, 100, on_trackbar);

    while ((frame = cvQueryFrame(capture)) != NULL) {
        modified = cvCloneImage(frame);
        on_trackbar(brightness); // Apply initial brightness
        if (cvWaitKey(30) == 27) break; // Exit on 'Esc'
    }

    cvReleaseCapture(&capture);
    cvReleaseImage(&modified);
    cvDestroyWindow("Video Brightness");
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvCreateTrackbar()` creates **a slider for brightness adjustment**.  
✔ `cvConvertScale(frame, modified, 1.0, value - 50);` **adjusts brightness dynamically**.  
✔ `cvShowImage()` **updates the display in real-time**.  

---

## **3️⃣ Add text overlays to a live webcam feed using `cvPutText()`.**  
### **Expected Behavior:**  
- **Displays real-time video from the webcam**.  
- **Adds a timestamp overlay** (current time).  
- **Shows a "Press Esc to Exit" message** at the bottom.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"
#include <ctime>

int main() {
    CvCapture* capture = cvCreateCameraCapture(0);
    if (!capture) {
        printf("Error: Could not access webcam\n");
        return -1;
    }

    IplImage* frame;
    CvFont font;
    cvInitFont(&font, CV_FONT_HERSHEY_SIMPLEX, 1.0, 1.0, 0, 2, 8);

    while ((frame = cvQueryFrame(capture)) != NULL) {
        // Get the current time
        time_t t;
        time(&t);
        struct tm* timeinfo = localtime(&t);
        char timeString[50];
        strftime(timeString, sizeof(timeString), "Time: %H:%M:%S", timeinfo);

        // Draw text on frame
        cvPutText(frame, timeString, cvPoint(30, 50), &font, CV_RGB(255, 0, 0));
        cvPutText(frame, "Press Esc to Exit", cvPoint(30, frame->height - 30), &font, CV_RGB(0, 255, 0));

        cvShowImage("Webcam with Overlay", frame);
        if (cvWaitKey(30) == 27) break;  // Exit on 'Esc'
    }

    cvReleaseCapture(&capture);
    cvDestroyWindow("Webcam with Overlay");
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvPutText()` **draws text overlays on video**.  
✔ `strftime()` **formats the current time**.  
✔ `cvShowImage()` **updates the display in real-time**.  

---

## **📌 Summary of What You Practiced:**  
✔ **Control video playback speed** (normal, slow-motion, fast-forward).  
✔ **Adjust video brightness dynamically using a trackbar.**  
✔ **Add real-time text overlays (timestamp, instructions) on video.**  

---

## **🚀 More Challenges – Take It Further!**
🔹 **1. Implement a video filter that lets the user switch between grayscale, sepia, and edge detection.**  
🔹 **2. Add a motion detection overlay to highlight moving objects.**  
🔹 **3. Create a GUI with buttons (using `cvSetMouseCallback`) to switch between different camera effects.**  