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
#include <opencv2/opencv.hpp>
#include <iostream>

int main() {
    cv::Mat mat(5, 5, CV_32SC1);  // 5x5 integer matrix
    int value = 1;

    // Fill the matrix
    for (int i = 0; i < mat.rows; i++) {
        for (int j = 0; j < mat.cols; j++) {
            mat.at<int>(i, j) = value++;
        }
    }

    // Display the matrix
    std::cout << "Matrix:\n" << mat << std::endl;

    return 0;
}
```

---

## **2. Load an image, convert it to grayscale, and save the output.**
### **Solution:**
```cpp
#include <opencv2/opencv.hpp>

int main() {
    // Load the image
    cv::Mat img = cv::imread("input.jpg");
    if (img.empty()) {
        std::cout << "Error: Could not load image!" << std::endl;
        return -1;
    }

    // Convert to grayscale
    cv::Mat gray;
    cv::cvtColor(img, gray, cv::COLOR_BGR2GRAY);

    // Save grayscale image
    cv::imwrite("grayscale_output.jpg", gray);

    // Display images
    cv::imshow("Original Image", img);
    cv::imshow("Grayscale Image", gray);
    cv::waitKey(0);

    return 0;
}
```

---

## **3. Modify an image by drawing a red rectangle around a detected object.**
### **Solution:**
```cpp
#include <opencv2/opencv.hpp>

int main() {
    // Load the image
    cv::Mat img = cv::imread("input.jpg");
    if (img.empty()) {
        std::cout << "Error: Could not load image!" << std::endl;
        return -1;
    }

    // Define rectangle coordinates
    cv::Point pt1(50, 50);  // Top-left corner
    cv::Point pt2(200, 200); // Bottom-right corner

    // Draw rectangle (Red color, thickness 2)
    cv::rectangle(img, pt1, pt2, cv::Scalar(0, 0, 255), 2);

    // Show and save the modified image
    cv::imshow("Modified Image", img);
    cv::imwrite("output_with_rectangle.jpg", img);

    cv::waitKey(0);

    return 0;
}
```

---

## **4. Generate a random image and save it as a PNG file.**
### **Solution:**
```cpp
#include <opencv2/opencv.hpp>
#include <cstdlib>
#include <ctime>

int main() {
    int width = 256, height = 256;
    cv::Mat img(height, width, CV_8UC3);  // 3-channel image

    srand(time(0));  // Seed random generator

    for (int y = 0; y < height; y++) {
        for (int x = 0; x < width; x++) {
            img.at<cv::Vec3b>(y, x) = cv::Vec3b(
                rand() % 256,  // Blue
                rand() % 256,  // Green
                rand() % 256   // Red
            );
        }
    }

    // Save the random image
    cv::imwrite("random_image.png", img);

    // Display the image
    cv::imshow("Random Image", img);
    cv::waitKey(0);

    return 0;
}
```

---

## **5. Add noise to an image and then remove it using filtering.**
### **Solution:**
```cpp
#include <opencv2/opencv.hpp>
#include <cstdlib>
#include <ctime>

// Function to add noise to an image
void addNoise(cv::Mat& img) {
    srand(time(0));
    for (int y = 0; y < img.rows; y++) {
        for (int x = 0; x < img.cols; x++) {
            for (int c = 0; c < img.channels(); c++) {
                int noise = rand() % 50 - 25;  // Random noise between -25 to 25
                int newValue = img.at<cv::Vec3b>(y, x)[c] + noise;
                img.at<cv::Vec3b>(y, x)[c] = cv::saturate_cast<uchar>(newValue);
            }
        }
    }
}

int main() {
    // Load the image
    cv::Mat img = cv::imread("input.jpg");
    if (img.empty()) {
        std::cout << "Error: Could not load image!" << std::endl;
        return -1;
    }

    // Create noisy image
    cv::Mat noisyImg = img.clone();
    addNoise(noisyImg);

    // Create a filtered image
    cv::Mat filteredImg;
    cv::GaussianBlur(noisyImg, filteredImg, cv::Size(5, 5), 0);

    // Save images
    cv::imwrite("noisy_image.jpg", noisyImg);
    cv::imwrite("filtered_image.jpg", filteredImg);

    // Show images
    cv::imshow("Original Image", img);
    cv::imshow("Noisy Image", noisyImg);
    cv::imshow("Filtered Image", filteredImg);

    cv::waitKey(0);

    return 0;
}
```

---

### **📌 Summary of What You Practiced:**
✔ **Create and manipulate matrices (`cv::Mat`).**  
✔ **Load, modify, and save images (`cv::imread`, `cv::imwrite`).**  
✔ **Perform basic image processing (grayscale conversion, noise addition, filtering).**  
✔ **Draw shapes (rectangles) on images (`cv::rectangle`).**  
✔ **Generate and save random images.**  

---

## **Next Steps**
Try experimenting with these programs by:  
1. **Changing parameters** (e.g., different filter sizes, noise levels).  
2. **Combining multiple effects** (e.g., noise + grayscale + edge detection).  
3. **Loading images from a webcam instead of files.** 