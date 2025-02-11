### **Chapter 4: HighGUI – A Portable Graphics Toolkit**  

This chapter focuses on **HighGUI**, which is OpenCV’s module for handling **image display, video capture, and GUI elements** (such as windows, trackbars, and mouse interactions). Mastering HighGUI is essential for **visualizing OpenCV processing results** and **interacting with the user**.

---

## **1. What Is HighGUI?**  
HighGUI (High-Level Graphical User Interface) is OpenCV’s built-in **interface for handling images, videos, and simple GUI elements**.  

**Features of HighGUI:**  
✔ **Create windows** for displaying images.  
✔ **Read and write images** in various formats (JPG, PNG, BMP, etc.).  
✔ **Play and save videos** in AVI, MP4, and other formats.  
✔ **Capture video from a camera** (webcam, USB camera).  
✔ **Create GUI elements** like **buttons, trackbars, and mouse events**.  

---

## **2. Creating a Window in OpenCV**  

### **Function: `cvNamedWindow()`**
Creates a window to display images.

```cpp
cvNamedWindow("My Window", CV_WINDOW_AUTOSIZE);
```

| **Flag** | **Description** |
|----------|--------------|
| `CV_WINDOW_AUTOSIZE` | Window resizes automatically to fit the image (default). |
| `CV_WINDOW_NORMAL` | Allows the user to resize the window manually. |

### **Example: Create and Show an Empty Window**
```cpp
#include "highgui.h"

int main() {
    cvNamedWindow("My Window", CV_WINDOW_AUTOSIZE);
    cvWaitKey(0); // Wait until a key is pressed
    cvDestroyWindow("My Window");
    return 0;
}
```

---

## **3. Loading and Displaying an Image**  

### **Function: `cvLoadImage()`**
Loads an image from a file.

```cpp
IplImage* img = cvLoadImage("input.jpg", CV_LOAD_IMAGE_COLOR);
```

| **Flag**                 | **Description** |
|--------------------------|--------------|
| `CV_LOAD_IMAGE_COLOR`    | Loads a color image (default). |
| `CV_LOAD_IMAGE_GRAYSCALE` | Loads a grayscale image. |
| `CV_LOAD_IMAGE_UNCHANGED` | Loads the image as it is (including alpha channels). |

### **Example: Load and Display an Image**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    IplImage* img = cvLoadImage("input.jpg", CV_LOAD_IMAGE_COLOR);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    cvNamedWindow("Display Image", CV_WINDOW_AUTOSIZE);
    cvShowImage("Display Image", img);

    cvWaitKey(0);
    cvReleaseImage(&img);
    cvDestroyWindow("Display Image");
    return 0;
}
```

---

## **4. Playing a Video in OpenCV**  

### **Function: `cvCreateFileCapture()`**
Loads a video file for playback.

```cpp
CvCapture* capture = cvCreateFileCapture("video.avi");
```

### **Function: `cvQueryFrame()`**
Retrieves frames from the video.

```cpp
IplImage* frame = cvQueryFrame(capture);
```

### **Example: Play a Video File**
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
    while ((frame = cvQueryFrame(capture)) != NULL) {
        cvShowImage("Video Player", frame);
        if (cvWaitKey(30) >= 0) break; // 30ms delay (~30 FPS)
    }

    cvReleaseCapture(&capture);
    cvDestroyWindow("Video Player");
    return 0;
}
```

---

## **5. Capturing Video from a Camera**  

### **Function: `cvCreateCameraCapture()`**
Opens the default webcam.

```cpp
CvCapture* capture = cvCreateCameraCapture(0);
```
(Use `1`, `2`, etc. for multiple cameras.)

### **Example: Capture and Display Live Video**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    CvCapture* capture = cvCreateCameraCapture(0);
    if (!capture) {
        printf("Error: Could not access webcam\n");
        return -1;
    }

    IplImage* frame;
    while ((frame = cvQueryFrame(capture)) != NULL) {
        cvShowImage("Webcam", frame);
        if (cvWaitKey(30) >= 0) break;
    }

    cvReleaseCapture(&capture);
    cvDestroyWindow("Webcam");
    return 0;
}
```

---

## **6. Writing a Video to a File**  

### **Function: `cvCreateVideoWriter()`**
Creates a video file.

```cpp
CvVideoWriter* writer = cvCreateVideoWriter("output.avi",
            CV_FOURCC('M','J','P','G'), 30, cvSize(640, 480));
```

| **Parameter** | **Description** |
|--------------|--------------|
| `"output.avi"` | Output file name |
| `CV_FOURCC('M','J','P','G')` | Codec for AVI format |
| `30` | Frames per second |
| `cvSize(640,480)` | Video resolution |

### **Example: Capture and Save Video from Webcam**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    CvCapture* capture = cvCreateCameraCapture(0);
    if (!capture) {
        printf("Error: Could not access webcam\n");
        return -1;
    }

    IplImage* frame = cvQueryFrame(capture);
    CvSize size = cvSize(frame->width, frame->height);
    CvVideoWriter* writer = cvCreateVideoWriter("output.avi",
            CV_FOURCC('M','J','P','G'), 30, size);

    while ((frame = cvQueryFrame(capture)) != NULL) {
        cvWriteFrame(writer, frame); // Write frame to file
        cvShowImage("Recording", frame);
        if (cvWaitKey(30) >= 0) break;
    }

    cvReleaseCapture(&capture);
    cvReleaseVideoWriter(&writer);
    cvDestroyWindow("Recording");
    return 0;
}
```

---

## **7. Creating Trackbars (Sliders) in OpenCV**  
Trackbars allow users to control parameters interactively.

### **Function: `cvCreateTrackbar()`**
Creates a trackbar.

```cpp
cvCreateTrackbar("Brightness", "Window", &brightness, 100, NULL);
```

### **Example: Adjust Image Brightness with Trackbar**
```cpp
#include "cv.h"
#include "highgui.h"

IplImage* img, *modified;
int brightness = 50;

void on_trackbar(int value) {
    cvConvertScale(img, modified, value / 50.0, 0);
    cvShowImage("Image", modified);
}

int main() {
    img = cvLoadImage("input.jpg", CV_LOAD_IMAGE_GRAYSCALE);
    modified = cvCloneImage(img);

    cvNamedWindow("Image", CV_WINDOW_AUTOSIZE);
    cvCreateTrackbar("Brightness", "Image", &brightness, 100, on_trackbar);
    
    on_trackbar(brightness); // Initial call
    cvWaitKey(0);

    cvReleaseImage(&img);
    cvReleaseImage(&modified);
    cvDestroyAllWindows();
    return 0;
}
```

---

## **8. Exercises**
1. **Load and display an image, then allow the user to resize the window manually.**  
2. **Write a program that captures video from a webcam and saves it in grayscale.**  
3. **Modify the video player to allow pausing and resuming using a key press.**  
4. **Create a trackbar to control the contrast of an image interactively.**  

---

## **Conclusion**  
✔ Learned how to **load and display images**.  
✔ Played **videos from files and webcams**.  
✔ Captured **real-time video**.  
✔ Created **trackbars for user interaction**.  
✔ Saved **videos to files**.  