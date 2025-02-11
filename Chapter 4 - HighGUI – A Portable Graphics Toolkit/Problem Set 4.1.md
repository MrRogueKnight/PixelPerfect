Let’s go through the **coding exercises** one by one.

---

## **1. Load and display an image, then allow the user to resize the window manually.**  
### **Expected Behavior:**
- The program **loads and displays an image**.  
- The user can **resize the window manually**.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    IplImage* img = cvLoadImage("input.jpg", CV_LOAD_IMAGE_COLOR);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    cvNamedWindow("Resizable Window", CV_WINDOW_NORMAL);  // Allow resizing
    cvShowImage("Resizable Window", img);
    
    cvWaitKey(0);  // Wait for a key press

    cvReleaseImage(&img);
    cvDestroyWindow("Resizable Window");
    return 0;
}
```

✅ **Key Takeaways:**  
✔ Use `CV_WINDOW_NORMAL` to allow **manual resizing** of the window.  
✔ `cvShowImage()` displays the image properly.  

---

## **2. Capture video from a webcam and save it in grayscale.**  
### **Expected Behavior:**
- The program **captures video** from the webcam.  
- The video is **converted to grayscale**.  
- The output is **saved to a file** (`grayscale_output.avi`).  

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

    IplImage* frame = cvQueryFrame(capture);
    CvSize size = cvSize(frame->width, frame->height);

    CvVideoWriter* writer = cvCreateVideoWriter("grayscale_output.avi",
                CV_FOURCC('M','J','P','G'), 30, size, 0);  // 0 = Grayscale

    IplImage* gray = cvCreateImage(size, IPL_DEPTH_8U, 1);

    while ((frame = cvQueryFrame(capture)) != NULL) {
        cvCvtColor(frame, gray, CV_BGR2GRAY);  // Convert to grayscale
        cvWriteFrame(writer, gray);  // Save grayscale frame

        cvShowImage("Grayscale Video", gray);
        if (cvWaitKey(30) >= 0) break;
    }

    cvReleaseCapture(&capture);
    cvReleaseVideoWriter(&writer);
    cvReleaseImage(&gray);
    cvDestroyWindow("Grayscale Video");
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvCvtColor(frame, gray, CV_BGR2GRAY);` **converts** the image to grayscale.  
✔ `cvWriteFrame(writer, gray);` **saves** the grayscale video frame by frame.  
✔ `CV_FOURCC('M','J','P','G')` sets the codec for AVI format.  

---

## **3. Modify the video player to allow pausing and resuming using a key press.**  
### **Expected Behavior:**
- The program **plays a video file**.  
- Pressing `Spacebar` **pauses/resumes** playback.  
- Pressing `Esc` **exits the player**.  

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
    int pause = 0;

    while (1) {
        if (!pause) {
            frame = cvQueryFrame(capture);
            if (!frame) break;
            cvShowImage("Video Player", frame);
        }

        char key = cvWaitKey(30);
        if (key == 27) break;  // Exit if 'Esc' is pressed
        if (key == 32) pause = !pause;  // Toggle pause on 'Spacebar'
    }

    cvReleaseCapture(&capture);
    cvDestroyWindow("Video Player");
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvWaitKey(30)` waits for user input while playing.  
✔ **If `Spacebar` is pressed**, `pause = !pause;` toggles pause mode.  
✔ **If `Esc` is pressed**, the program exits.  

---

## **4. Create a trackbar to control the contrast of an image interactively.**  
### **Expected Behavior:**
- The user **adjusts contrast using a trackbar**.  
- The modified image updates **in real-time**.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

IplImage* img, *modified;
int contrast = 50;  // Default contrast level

void on_trackbar(int value) {
    cvConvertScale(img, modified, value / 50.0, 0);  // Adjust contrast
    cvShowImage("Contrast Adjuster", modified);
}

int main() {
    img = cvLoadImage("input.jpg", CV_LOAD_IMAGE_GRAYSCALE);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    modified = cvCloneImage(img);

    cvNamedWindow("Contrast Adjuster", CV_WINDOW_AUTOSIZE);
    cvCreateTrackbar("Contrast", "Contrast Adjuster", &contrast, 100, on_trackbar);

    on_trackbar(contrast);  // Initial call to apply default contrast
    cvWaitKey(0);

    cvReleaseImage(&img);
    cvReleaseImage(&modified);
    cvDestroyAllWindows();
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvCreateTrackbar("Contrast", "Window", &contrast, 100, on_trackbar);` **creates a slider**.  
✔ `cvConvertScale(img, modified, value / 50.0, 0);` **adjusts contrast dynamically**.  
✔ **Trackbar allows real-time interaction with OpenCV.**  

---

## **📌 Summary of What You Practiced:**
✔ **Load, display, and resize images manually (`CV_WINDOW_NORMAL`).**  
✔ **Capture and save grayscale video from a webcam.**  
✔ **Modify a video player to support pause and resume (`Spacebar`).**  
✔ **Use trackbars to dynamically adjust contrast.**  

---

## **🚀 Next Steps – More Challenges!**
Want to **push your skills further?** Try these:  
1️⃣ **Modify the video player to play videos in slow-motion or fast-forward.**  
2️⃣ **Allow the user to adjust video brightness using a trackbar.**  
3️⃣ **Add text overlays to a live webcam feed using `cvPutText()`.**  
