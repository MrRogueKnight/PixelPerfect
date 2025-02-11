### **Chapter 2: Introduction to OpenCV**  

This chapter focuses on setting up OpenCV, writing basic programs, handling images and videos, and understanding some fundamental transformations.

---

## **1. Getting Started with OpenCV**  
After installing OpenCV, the first step is to set up the programming environment and write simple programs.  

- **Required Libraries** (to be linked in your project):  
  - `highgui.lib` (GUI & file handling)  
  - `cxcore.lib` (Core OpenCV functions)  
  - `ml.lib` (Machine Learning)  
  - `cv.lib` (Computer Vision)  

- **Include Directories Required:**  
  ```
  C:/Program Files/OpenCV/include
  C:/Program Files/OpenCV/cxcore/include
  C:/Program Files/OpenCV/ml/include
  C:/Program Files/OpenCV/otherlibs/highgui
  ```

**💡 Memory Trick:** Remember **"HCM-CV"** → **H**ighGUI, **C**XCore, **M**L, **C**V  

---

## **2. First OpenCV Program – Display an Image**  
### **Goal:** Load an image from disk and display it.  

```cpp
#include "highgui.h"

int main(int argc, char** argv) {
    IplImage* img = cvLoadImage(argv[1]);  // Load image
    cvNamedWindow("Example1", CV_WINDOW_AUTOSIZE);  // Create window
    cvShowImage("Example1", img);  // Show image
    cvWaitKey(0);  // Wait for key press
    cvReleaseImage(&img);  // Release image memory
    cvDestroyWindow("Example1");  // Destroy window
    return 0;
}
```

### **Breakdown of the Code:**  
- **`cvLoadImage(argv[1])`** → Loads the image file.  
- **`cvNamedWindow()`** → Creates a resizable window.  
- **`cvShowImage()`** → Displays the image.  
- **`cvWaitKey(0)`** → Waits indefinitely until a key is pressed.  
- **`cvReleaseImage()`** → Frees image memory to prevent leaks.  
- **`cvDestroyWindow()`** → Closes the window properly.  

**💡 Memory Trick:** Think of **"LNWSR-D"** → **L**oad, **N**ame, **W**ait, **S**how, **R**elease, **D**estroy  

---

## **3. Handling Video Files in OpenCV**  
### **Goal:** Load and display a video file frame-by-frame.  

```cpp
#include "cv.h"
#include "highgui.h"

int main(int argc, char** argv) {
    CvCapture* capture = cvCreateFileCapture(argv[1]);  // Load video
    IplImage* frame;

    while ((frame = cvQueryFrame(capture)) != NULL) {
        cvShowImage("Video Example", frame);
        if (cvWaitKey(33) >= 0) break;  // 30 FPS delay (33ms)
    }

    cvReleaseCapture(&capture);
    cvDestroyWindow("Video Example");
    return 0;
}
```

### **Breakdown of the Code:**  
- **`cvCreateFileCapture(argv[1])`** → Opens the video file.  
- **`cvQueryFrame()`** → Reads each frame one by one.  
- **`cvShowImage()`** → Displays the current frame.  
- **`cvWaitKey(33)`** → Waits 33ms per frame (~30 FPS).  
- **`cvReleaseCapture()`** → Releases memory after finishing.  
- **`cvDestroyWindow()`** → Closes the window properly.  

**💡 Trick to Remember:** **"QVD-RD"** → **Q**uery frame, **V**iew, **D**elay, **R**elease, **D**estroy  

---

## **4. Moving Around in an Image**  
### **Goal:** Access and modify individual pixels.  

```cpp
uchar* ptr = (uchar*) (img->imageData + y * img->widthStep);
uchar pixel_value = ptr[x * img->nChannels + channel];
```

### **Key Points:**  
- **Each image is stored as a 1D array in memory.**  
- **`widthStep`** → Bytes per row (important for accessing pixels).  
- **`nChannels`** → Number of color channels (1 for grayscale, 3 for RGB).  
- **`imageData`** → The actual pixel data.  

#### **Example – Convert to Grayscale:**  
```cpp
for (int y = 0; y < img->height; y++) {
    for (int x = 0; x < img->width; x++) {
        uchar* ptr = (uchar*)(img->imageData + y * img->widthStep);
        uchar blue = ptr[x * 3 + 0];
        uchar green = ptr[x * 3 + 1];
        uchar red = ptr[x * 3 + 2];
        uchar gray = (red + green + blue) / 3;
        ptr[x * 3 + 0] = ptr[x * 3 + 1] = ptr[x * 3 + 2] = gray;
    }
}
```

---

## **5. Simple Image Transformation – Flip Image**  
```cpp
cvFlip(img, NULL, 1); // 1 = Flip horizontally, 0 = Flip vertically
```
- **1 = Left to Right flip**
- **0 = Top to Bottom flip**
- **-1 = Both flips**

---

## **6. Capturing Video from a Camera**  
```cpp
CvCapture* capture = cvCreateCameraCapture(0);  // 0 for default camera
```
- Uses the same logic as the video playback program.
- **Replace `cvCreateFileCapture()` with `cvCreateCameraCapture()`**.

---

## **7. Writing Video to an AVI File**  
```cpp
CvVideoWriter* writer = cvCreateVideoWriter("output.avi",
                CV_FOURCC('M','J','P','G'), 30, cvSize(width, height));
```
- **MJPG codec** is commonly used for AVI files.
- **30 FPS** for smooth playback.
- **Ensure image size matches video size**.

---

## **8. Exercises (Suggested for You to Try)**  
1. **Modify the image loading program to accept multiple image formats (PNG, BMP, JPG).**  
2. **Write a program to capture video from your webcam and apply a filter (e.g., grayscale).**  
3. **Modify the video player program to add a pause/resume feature using a key press.**  
4. **Write a program that flips an image both horizontally and vertically.**  

---

## **💡 Memory Tricks for Key Concepts**  

| **Concept**         | **Memory Trick** |
|---------------------|-----------------|
| **Basic Program Flow** | **LNWSR-D** (Load, Name, Wait, Show, Release, Destroy) |
| **Video Playback**  | **QVD-RD** (Query, View, Delay, Release, Destroy) |
| **Image Memory**    | **WiNC** (WidthStep, nChannels) |
| **Flipping Images** | **1 = Left-Right, 0 = Top-Bottom, -1 = Both** |

---

## **Conclusion**  
Chapter 2 introduces **basic OpenCV programming**—reading images, handling videos, and simple transformations. You now know:  
✔ How to **load and display images**.  
✔ How to **play and capture video**.  
✔ How to **access and manipulate pixels**.  
✔ How to **flip images and apply transformations**.  