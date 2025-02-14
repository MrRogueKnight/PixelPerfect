---

### **Chapter 2: Introduction to OpenCV**  

This chapter focuses on setting up OpenCV, writing basic programs, handling images and videos, and understanding some fundamental transformations.

---

## **1. Getting Started with OpenCV**  
After installing OpenCV, the first step is to set up the programming environment and write simple programs.  

- **Required Libraries** (to be linked in your project):  
  - `opencv_core.lib` (Core OpenCV functions)  
  - `opencv_highgui.lib` (GUI & file handling)  
  - `opencv_imgproc.lib` (Image processing functions)  
  - `opencv_videoio.lib` (Video I/O operations)  

- **Include Directories Required:**  
  ```
  C:/opencv/build/include
  ```

**💡 Memory Trick:** Remember **"CHIV"** → **C**ore, **H**ighGUI, **I**mgproc, **V**ideoIO  

---

## **2. First OpenCV Program – Display an Image**  
### **Goal:** Load an image from disk and display it.  

```cpp
#include <opencv2/opencv.hpp>
#include <iostream>

int main(int argc, char** argv) {
    if (argc != 2) {
        std::cout << "Usage: ./display_image <image_path>\n";
        return -1;
    }

    cv::Mat img = cv::imread(argv[1]);  // Load image
    if (img.empty()) {
        std::cout << "Could not open or find the image!\n";
        return -1;
    }

    cv::namedWindow("Example1", cv::WINDOW_AUTOSIZE);  // Create window
    cv::imshow("Example1", img);  // Show image
    cv::waitKey(0);  // Wait for key press
    cv::destroyWindow("Example1");  // Destroy window
    return 0;
}
```

### **Breakdown of the Code:**  
- **`cv::imread(argv[1])`** → Loads the image file.  
- **`cv::namedWindow()`** → Creates a resizable window.  
- **`cv::imshow()`** → Displays the image.  
- **`cv::waitKey(0)`** → Waits indefinitely until a key is pressed.  
- **`cv::destroyWindow()`** → Closes the window properly.  

**💡 Memory Trick:** Think of **"LNWD"** → **L**oad, **N**ame, **W**ait, **D**estroy  

---

## **3. Handling Video Files in OpenCV**  
### **Goal:** Load and display a video file frame-by-frame.  

```cpp
#include <opencv2/opencv.hpp>
#include <iostream>

int main(int argc, char** argv) {
    if (argc != 2) {
        std::cout << "Usage: ./play_video <video_path>\n";
        return -1;
    }

    cv::VideoCapture capture(argv[1]);  // Load video
    if (!capture.isOpened()) {
        std::cout << "Could not open the video file!\n";
        return -1;
    }

    cv::Mat frame;
    while (capture.read(frame)) {
        cv::imshow("Video Example", frame);
        if (cv::waitKey(33) >= 0) break;  // 30 FPS delay (33ms)
    }

    capture.release();
    cv::destroyWindow("Video Example");
    return 0;
}
```

### **Breakdown of the Code:**  
- **`cv::VideoCapture(argv[1])`** → Opens the video file.  
- **`capture.read(frame)`** → Reads each frame one by one.  
- **`cv::imshow()`** → Displays the current frame.  
- **`cv::waitKey(33)`** → Waits 33ms per frame (~30 FPS).  
- **`capture.release()`** → Releases memory after finishing.  
- **`cv::destroyWindow()`** → Closes the window properly.  

**💡 Trick to Remember:** **"RVWD"** → **R**ead frame, **V**iew, **W**ait, **D**estroy  

---

## **4. Moving Around in an Image**  
### **Goal:** Access and modify individual pixels.  

```cpp
cv::Vec3b& pixel = img.at<cv::Vec3b>(y, x);
uchar blue = pixel[0];
uchar green = pixel[1];
uchar red = pixel[2];
```

### **Key Points:**  
- **`cv::Vec3b`** → Represents a 3-channel pixel (BGR format).  
- **`img.at<cv::Vec3b>(y, x)`** → Accesses the pixel at (x, y).  

#### **Example – Convert to Grayscale:**  
```cpp
for (int y = 0; y < img.rows; y++) {
    for (int x = 0; x < img.cols; x++) {
        cv::Vec3b& pixel = img.at<cv::Vec3b>(y, x);
        uchar gray = (pixel[0] + pixel[1] + pixel[2]) / 3;
        pixel[0] = pixel[1] = pixel[2] = gray;
    }
}
```

---

## **5. Simple Image Transformation – Flip Image**  
```cpp
cv::Mat flipped;
cv::flip(img, flipped, 1); // 1 = Flip horizontally, 0 = Flip vertically
```
- **1 = Left to Right flip**
- **0 = Top to Bottom flip**
- **-1 = Both flips**

---

## **6. Capturing Video from a Camera**  
```cpp
cv::VideoCapture capture(0);  // 0 for default camera
```
- Uses the same logic as the video playback program.
- **Replace `cv::VideoCapture(argv[1])` with `cv::VideoCapture(0)`**.

---

## **7. Writing Video to an AVI File**  
```cpp
cv::VideoWriter writer("output.avi", cv::VideoWriter::fourcc('M','J','P','G'), 30, cv::Size(width, height));
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
| **Basic Program Flow** | **LNWD** (Load, Name, Wait, Destroy) |
| **Video Playback**  | **RVWD** (Read, View, Wait, Destroy) |
| **Image Memory**    | **Vec3B** (BGR pixel access) |
| **Flipping Images** | **1 = Left-Right, 0 = Top-Bottom, -1 = Both** |

---

## **Conclusion**  
Chapter 2 introduces **basic OpenCV programming**—reading images, handling videos, and simple transformations. You now know:  
✔ How to **load and display images**.  
✔ How to **play and capture video**.  
✔ How to **access and manipulate pixels**.  
✔ How to **flip images and apply transformations**.  

---

### **Additional Resources**  
- [OpenCV Documentation](https://docs.opencv.org)  
- [OpenCV GitHub Repository](https://github.com/opencv/opencv)  
- [OpenCV Python Tutorials](https://docs.opencv.org/master/d6/d00/tutorial_py_root.html)  

---