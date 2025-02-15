Let's go through the **coding exercises** one by one, and I'll provide **solutions** for each.

---

## **1. Create a 5x5 matrix and initialize it with values from 1 to 25.**
### **Expected Output:**
```
1  2  3  4  5  
6  7  8  9 10  
11 12 13 14 15  
16 17 18 19 20  
21 22 23 24 25  
```

### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"
#include <iostream>

int main() {
    CvMat* mat = cvCreateMat(5, 5, CV_32SC1);  // 5x5 Integer matrix
    int value = 1;

    // Fill the matrix
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            cvSetReal2D(mat, i, j, value++);
        }
    }

    // Display the matrix
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            std::cout << cvGetReal2D(mat, i, j) << "\t";
        }
        std::cout << std::endl;
    }

    cvReleaseMat(&mat);
    return 0;
}
```

---

## **2. Load an image, convert it to grayscale, and save the output.**
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

    // Create grayscale image
    IplImage* gray = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 1);
    cvCvtColor(img, gray, CV_BGR2GRAY);

    // Save grayscale image
    cvSaveImage("grayscale_output.jpg", gray);

    // Display images
    cvShowImage("Original Image", img);
    cvShowImage("Grayscale Image", gray);

    cvWaitKey(0);

    // Cleanup
    cvReleaseImage(&img);
    cvReleaseImage(&gray);
    cvDestroyAllWindows();
    return 0;
}
```

---

## **3. Modify an image by drawing a red rectangle around a detected object.**
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

    // Define rectangle coordinates
    CvPoint pt1 = cvPoint(50, 50);  // Top-left corner
    CvPoint pt2 = cvPoint(200, 200); // Bottom-right corner

    // Draw rectangle (Red color, thickness 2)
    cvRectangle(img, pt1, pt2, CV_RGB(255, 0, 0), 2);

    // Show and save the modified image
    cvShowImage("Modified Image", img);
    cvSaveImage("output_with_rectangle.jpg", img);

    cvWaitKey(0);

    cvReleaseImage(&img);
    cvDestroyAllWindows();
    return 0;
}
```

---

## **4. Generate a random image and save it as a PNG file.**
### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"
#include <cstdlib>
#include <ctime>

int main() {
    int width = 256, height = 256;
    IplImage* img = cvCreateImage(cvSize(width, height), IPL_DEPTH_8U, 3);

    srand(time(0));  // Seed random generator

    for (int y = 0; y < height; y++) {
        uchar* row = (uchar*)(img->imageData + y * img->widthStep);
        for (int x = 0; x < width; x++) {
            row[x * 3 + 0] = rand() % 256;  // Blue
            row[x * 3 + 1] = rand() % 256;  // Green
            row[x * 3 + 2] = rand() % 256;  // Red
        }
    }

    cvSaveImage("random_image.png", img);

    cvShowImage("Random Image", img);
    cvWaitKey(0);

    cvReleaseImage(&img);
    cvDestroyAllWindows();
    return 0;
}
```

---

## **5. Add noise to an image and then remove it using filtering.**
### **Solution:**
```cpp
#include "cv.h"
#include "highgui.h"
#include <cstdlib>
#include <ctime>

// Function to add noise to an image
void addNoise(IplImage* img) {
    srand(time(0));
    for (int y = 0; y < img->height; y++) {
        uchar* row = (uchar*)(img->imageData + y * img->widthStep);
        for (int x = 0; x < img->width; x++) {
            int noise = rand() % 50 - 25; // Random noise between -25 to 25
            for (int c = 0; c < img->nChannels; c++) {
                int newValue = row[x * img->nChannels + c] + noise;
                row[x * img->nChannels + c] = (uchar)cv::saturate_cast<uchar>(newValue);
            }
        }
    }
}

int main() {
    IplImage* img = cvLoadImage("input.jpg", CV_LOAD_IMAGE_COLOR);
    if (!img) {
        printf("Error: Could not load image\n");
        return -1;
    }

    // Create noisy image
    IplImage* noisyImg = cvCloneImage(img);
    addNoise(noisyImg);

    // Create a filtered image
    IplImage* filteredImg = cvCreateImage(cvGetSize(img), IPL_DEPTH_8U, 3);
    cvSmooth(noisyImg, filteredImg, CV_GAUSSIAN, 5, 5);

    // Save images
    cvSaveImage("noisy_image.jpg", noisyImg);
    cvSaveImage("filtered_image.jpg", filteredImg);

    // Show images
    cvShowImage("Original Image", img);
    cvShowImage("Noisy Image", noisyImg);
    cvShowImage("Filtered Image", filteredImg);

    cvWaitKey(0);

    // Cleanup
    cvReleaseImage(&img);
    cvReleaseImage(&noisyImg);
    cvReleaseImage(&filteredImg);
    cvDestroyAllWindows();
    return 0;
}
```

---

### **📌 Summary of What You Practiced:**
✔ **Create and manipulate matrices (`CvMat`).**  
✔ **Load, modify, and save images (`IplImage`).**  
✔ **Perform basic image processing (grayscale conversion, noise addition, filtering).**  
✔ **Draw shapes (rectangles) on images.**  
✔ **Generate and save random images.**  

---

## **Next Steps**
Try experimenting with these programs by:  
1. **Changing parameters** (e.g., different filter sizes, noise levels).  
2. **Combining multiple effects** (e.g., noise + grayscale + edge detection).  
3. **Loading images from a webcam instead of files.**  