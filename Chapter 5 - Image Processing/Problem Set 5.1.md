Let’s tackle the **coding exercises** for Chapter 5 and **master image processing** in OpenCV. 🚀  

---

# **1️⃣ Load an Image and Apply Gaussian & Median Blur**  
### **Expected Behavior:**  
- Load an image and **apply both Gaussian and Median blur**.  
- **Compare the results** to see how each blur affects the image.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    // Load the image
    IplImage* img = cvLoadImage("input.jpg", CV_LOAD_IMAGE_COLOR);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    // Create output images for blurring
    IplImage* gaussianBlur = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 3);
    IplImage* medianBlur = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 3);

    // Apply Gaussian Blur
    cvSmooth(img, gaussianBlur, CV_GAUSSIAN, 15, 15);

    // Apply Median Blur
    cvSmooth(img, medianBlur, CV_MEDIAN, 15);

    // Display the results
    cvNamedWindow("Original Image", CV_WINDOW_AUTOSIZE);
    cvNamedWindow("Gaussian Blur", CV_WINDOW_AUTOSIZE);
    cvNamedWindow("Median Blur", CV_WINDOW_AUTOSIZE);

    cvShowImage("Original Image", img);
    cvShowImage("Gaussian Blur", gaussianBlur);
    cvShowImage("Median Blur", medianBlur);

    cvWaitKey(0);

    // Cleanup
    cvReleaseImage(&img);
    cvReleaseImage(&gaussianBlur);
    cvReleaseImage(&medianBlur);
    cvDestroyAllWindows();
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `CV_GAUSSIAN` → **Smoothens the image** while preserving edges.  
✔ `CV_MEDIAN` → **Removes salt-and-pepper noise** effectively.  
✔ **Gaussian Blur is more natural**, while **Median Blur preserves edges better**.  

---

# **2️⃣ Apply Canny Edge Detection with Different Thresholds**  
### **Expected Behavior:**  
- Load an image and **apply Canny edge detection**.  
- **Experiment with different threshold values** to observe changes.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    // Load and convert to grayscale
    IplImage* img = cvLoadImage("input.jpg", CV_LOAD_IMAGE_GRAYSCALE);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    // Create output image for edges
    IplImage* edges = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);

    // Apply Canny Edge Detection with different thresholds
    cvCanny(img, edges, 50, 150);
    cvNamedWindow("Edges (50, 150)", CV_WINDOW_AUTOSIZE);
    cvShowImage("Edges (50, 150)", edges);

    cvCanny(img, edges, 100, 200);
    cvNamedWindow("Edges (100, 200)", CV_WINDOW_AUTOSIZE);
    cvShowImage("Edges (100, 200)", edges);

    cvCanny(img, edges, 150, 250);
    cvNamedWindow("Edges (150, 250)", CV_WINDOW_AUTOSIZE);
    cvShowImage("Edges (150, 250)", edges);

    cvWaitKey(0);

    // Cleanup
    cvReleaseImage(&img);
    cvReleaseImage(&edges);
    cvDestroyAllWindows();
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvCanny()` → Detects **edges by analyzing gradients** in intensity.  
✔ **Lower thresholds** → More edges but more noise.  
✔ **Higher thresholds** → Clearer edges but risk of losing details.  

---

# **3️⃣ Extract Text from an Image Using Thresholding**  
### **Expected Behavior:**  
- Load an image containing text.  
- Apply **binary thresholding** to extract the text.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    // Load and convert to grayscale
    IplImage* img = cvLoadImage("text_image.jpg", CV_LOAD_IMAGE_GRAYSCALE);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    // Create output image for thresholding
    IplImage* thresholded = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);

    // Apply Binary Thresholding
    cvThreshold(img, thresholded, 128, 255, CV_THRESH_BINARY);

    // Display results
    cvNamedWindow("Original Image", CV_WINDOW_AUTOSIZE);
    cvNamedWindow("Thresholded Image", CV_WINDOW_AUTOSIZE);

    cvShowImage("Original Image", img);
    cvShowImage("Thresholded Image", thresholded);

    cvWaitKey(0);

    // Cleanup
    cvReleaseImage(&img);
    cvReleaseImage(&thresholded);
    cvDestroyAllWindows();
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvThreshold()` → Converts image to **black & white**.  
✔ **Useful for OCR** (Optical Character Recognition) to extract text.  
✔ Can be **enhanced using Adaptive Thresholding** for uneven lighting.  

---

# **4️⃣ Perform Morphological Operations on a Binary Image**  
### **Expected Behavior:**  
- Load a **binary image**.  
- Perform **erosion, dilation, opening, and closing** operations.  
- **Analyze the effect** of each morphological transformation.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    // Load the binary image
    IplImage* img = cvLoadImage("binary_image.jpg", CV_LOAD_IMAGE_GRAYSCALE);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    // Create output images for morphological operations
    IplImage* eroded = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);
    IplImage* dilated = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);
    IplImage* opened = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);
    IplImage* closed = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);

    // Apply Morphological Operations
    cvErode(img, eroded, 0, 2);  // Erosion
    cvDilate(img, dilated, 0, 2); // Dilation
    cvMorphologyEx(img, opened, 0, 0, CV_MOP_OPEN, 1);  // Opening
    cvMorphologyEx(img, closed, 0, 0, CV_MOP_CLOSE, 1); // Closing

    // Display the results
    cvNamedWindow("Original Image", CV_WINDOW_AUTOSIZE);
    cvNamedWindow("Eroded", CV_WINDOW_AUTOSIZE);
    cvNamedWindow("Dilated", CV_WINDOW_AUTOSIZE);
    cvNamedWindow("Opened", CV_WINDOW_AUTOSIZE);
    cvNamedWindow("Closed", CV_WINDOW_AUTOSIZE);

    cvShowImage("Original Image", img);
    cvShowImage("Eroded", eroded);
    cvShowImage("Dilated", dilated);
    cvShowImage("Opened", opened);
    cvShowImage("Closed", closed);

    cvWaitKey(0);

    // Cleanup
    cvReleaseImage(&img);
    cvReleaseImage(&eroded);
    cvReleaseImage(&dilated);
    cvReleaseImage(&opened);
    cvReleaseImage(&closed);
    cvDestroyAllWindows();
    return 0;
}
```

✅ **Key Takeaways:**  
✔ **Erosion** → Shrinks objects, removes noise.  
✔ **Dilation** → Expands objects, fills holes.  
✔ **Opening** → Removes noise but retains shape.  
✔ **Closing** → Fills small holes in objects.  

---

# **📌 Summary of What You Practiced:**  
✔ **Gaussian & Median Blurring for noise reduction.**  
✔ **Canny edge detection for boundary extraction.**  
✔ **Thresholding for text extraction.**  
✔ **Morphological operations for shape manipulation.**  