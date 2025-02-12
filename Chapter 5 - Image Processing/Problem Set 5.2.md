Let's dive into **advanced image processing exercises** that take OpenCV to the next level! These challenges will help you master **contour detection, image segmentation, feature extraction, and object tracking**! 🚀  

---

# **1️⃣ Detect and Draw Contours in an Image**  
### **Expected Behavior:**  
- Load an image and **detect all contours**.  
- **Draw the detected contours** on a copy of the original image.  
- Display the **number of contours** detected.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"
#include <iostream>

int main() {
    // Load and convert to grayscale
    IplImage* img = cvLoadImage("shapes.jpg", CV_LOAD_IMAGE_GRAYSCALE);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    // Apply binary threshold
    IplImage* binary = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);
    cvThreshold(img, binary, 128, 255, CV_THRESH_BINARY);

    // Find contours
    CvMemStorage* storage = cvCreateMemStorage(0);
    CvSeq* contours = NULL;
    cvFindContours(binary, storage, &contours, sizeof(CvContour), CV_RETR_EXTERNAL, CV_CHAIN_APPROX_SIMPLE);

    // Create a copy to draw contours on
    IplImage* contourImg = cvLoadImage("shapes.jpg", CV_LOAD_IMAGE_COLOR);

    // Draw all contours
    cvDrawContours(contourImg, contours, CV_RGB(0, 255, 0), CV_RGB(0, 0, 255), 1, 2);

    // Count contours
    int contourCount = 0;
    for (CvSeq* c = contours; c != NULL; c = c->h_next) {
        contourCount++;
    }
    std::cout << "Number of contours detected: " << contourCount << std::endl;

    // Display results
    cvNamedWindow("Contours", CV_WINDOW_AUTOSIZE);
    cvShowImage("Contours", contourImg);
    cvWaitKey(0);

    // Cleanup
    cvReleaseImage(&img);
    cvReleaseImage(&binary);
    cvReleaseImage(&contourImg);
    cvReleaseMemStorage(&storage);
    cvDestroyAllWindows();
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvFindContours()` → **Detects object boundaries** as contours.  
✔ `cvDrawContours()` → **Draws contours** on the original image.  
✔ Can be **extended to detect shapes like circles, squares, etc.**  

---

# **2️⃣ Image Segmentation Using Watershed Algorithm**  
### **Expected Behavior:**  
- **Segment different regions** in an image using the **Watershed algorithm**.  
- Display **segmented regions** with different colors.  
- Useful for **object separation** in cluttered images.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    // Load and convert to grayscale
    IplImage* img = cvLoadImage("coins.jpg", CV_LOAD_IMAGE_COLOR);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    IplImage* gray = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);
    cvCvtColor(img, gray, CV_BGR2GRAY);

    // Apply threshold
    IplImage* binary = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);
    cvThreshold(gray, binary, 100, 255, CV_THRESH_BINARY_INV);

    // Distance transform and threshold
    IplImage* dist = cvCreateImage(cvGetSize(img), IPL_DEPTH_32F, 1);
    cvDistTransform(binary, dist, CV_DIST_L2, 3);
    cvThreshold(dist, dist, 0.4 * cvAvg(dist).val[0], 255, CV_THRESH_BINARY);

    // Convert to 8-bit image
    IplImage* markers = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);
    cvConvertScale(dist, markers, 1, 0);

    // Find contours for markers
    CvMemStorage* storage = cvCreateMemStorage(0);
    CvSeq* contours = NULL;
    cvFindContours(markers, storage, &contours, sizeof(CvContour), CV_RETR_CCOMP, CV_CHAIN_APPROX_SIMPLE);

    // Draw contours as markers
    int idx = 0;
    for (CvSeq* c = contours; c != NULL; c = c->h_next) {
        cvDrawContours(markers, c, cvScalarAll(++idx), cvScalarAll(idx), -1, -1);
    }

    // Apply Watershed
    cvWatershed(img, markers);

    // Display result
    cvNamedWindow("Watershed Segmentation", CV_WINDOW_AUTOSIZE);
    cvShowImage("Watershed Segmentation", img);
    cvWaitKey(0);

    // Cleanup
    cvReleaseImage(&img);
    cvReleaseImage(&gray);
    cvReleaseImage(&binary);
    cvReleaseImage(&dist);
    cvReleaseImage(&markers);
    cvReleaseMemStorage(&storage);
    cvDestroyAllWindows();
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvDistTransform()` → **Calculates distance** from the nearest zero pixel.  
✔ `cvWatershed()` → **Segments overlapping objects** using distance maps.  
✔ **Great for separating touching objects**, like coins or cells.  

---

# **3️⃣ Feature Extraction Using Harris Corner Detection**  
### **Expected Behavior:**  
- Detect **corners (features)** in an image using **Harris Corner Detection**.  
- **Draw circles** on detected corners.  
- Useful for **object recognition and tracking**.  

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"

int main() {
    // Load and convert to grayscale
    IplImage* img = cvLoadImage("building.jpg", CV_LOAD_IMAGE_GRAYSCALE);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    // Create images for corner detection
    IplImage* harris = cvCreateImage(cvGetSize(img), IPL_DEPTH_32F, 1);

    // Apply Harris Corner Detection
    cvCornerHarris(img, harris, 3);

    // Normalize the result
    cvNormalize(harris, harris, 0, 255, CV_MINMAX);
    cvConvertScale(harris, harris, 1, 0);

    // Draw circles on detected corners
    for (int y = 0; y < harris->height; y++) {
        for (int x = 0; x < harris->width; x++) {
            float val = cvGetReal2D(harris, y, x);
            if (val > 200) {
                cvCircle(img, cvPoint(x, y), 5, CV_RGB(0, 0, 255), 2);
            }
        }
    }

    // Display result
    cvNamedWindow("Harris Corners", CV_WINDOW_AUTOSIZE);
    cvShowImage("Harris Corners", img);
    cvWaitKey(0);

    // Cleanup
    cvReleaseImage(&img);
    cvReleaseImage(&harris);
    cvDestroyAllWindows();
    return 0;
}
```

✅ **Key Takeaways:**  
✔ `cvCornerHarris()` → Detects **corner points** in an image.  
✔ **Great for feature matching, object recognition, and tracking.**  
✔ Can be combined with **SIFT or SURF** for advanced feature extraction.  

---

# **📌 Summary of What You Practiced:**  
✔ **Contour detection and drawing** to extract object boundaries.  
✔ **Watershed algorithm** for **advanced image segmentation.**  
✔ **Harris Corner Detection** for **feature extraction and tracking.**  

---

# **🚀 More Advanced Image Processing Challenges – Are You Ready?**  
🔥 **1. Implement image stitching to create panoramic images.**  
🔥 **2. Perform object tracking using optical flow.**  
🔥 **3. Develop a custom object detector using HOG (Histogram of Oriented Gradients).**  