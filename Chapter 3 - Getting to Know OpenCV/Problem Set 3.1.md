---

## **1. Create a 5x5 matrix and initialize it with values from 1 to 25.**  
### **Corrections and Improvements:**
- The original code is mostly correct, but it's better to initialize the matrix with the correct type and use `CV_32S` for consistency.  
- Improved readability by enhancing the output format.

### **Code:**
```cpp
#include <opencv2/opencv.hpp>
#include <iostream>

int main() {
    // Create a 5x5 matrix of integers
    cv::Mat mat(5, 5, CV_32S);
    int value = 1;

    // Fill the matrix
    for (int i = 0; i < mat.rows; i++) {
        for (int j = 0; j < mat.cols; j++) {
            mat.at<int>(i, j) = value++;
        }
    }

    // Display the matrix in a formatted way
    std::cout << "Matrix:" << std::endl;
    for (int i = 0; i < mat.rows; i++) {
        for (int j = 0; j < mat.cols; j++) {
            std::cout << mat.at<int>(i, j) << "\t";
        }
        std::cout << std::endl;
    }

    return 0;
}
```

---

## **2. Load an image, convert it to grayscale, and save the output.**  
### **Corrections and Improvements:**
- Added error handling for image saving.
- Used `cv::IMREAD_COLOR` for clarity when loading the image.

### **Code:**
```cpp
#include <opencv2/opencv.hpp>
#include <iostream>

int main() {
    // Load the image in color mode
    cv::Mat img = cv::imread("input.jpg", cv::IMREAD_COLOR);
    if (img.empty()) {
        std::cerr << "Error: Could not load image!" << std::endl;
        return -1;
    }

    // Convert to grayscale
    cv::Mat gray;
    cv::cvtColor(img, gray, cv::COLOR_BGR2GRAY);

    // Save grayscale image
    if (!cv::imwrite("grayscale_output.jpg", gray)) {
        std::cerr << "Error: Could not save grayscale image!" << std::endl;
        return -1;
    }

    // Display images
    cv::imshow("Original Image", img);
    cv::imshow("Grayscale Image", gray);
    cv::waitKey(0);

    return 0;
}
```

---

## **3. Modify an image by drawing a red rectangle around a detected object.**  
### **Corrections and Improvements:**
- Enhanced error handling.
- Added comments for clarity.

### **Code:**
```cpp
#include <opencv2/opencv.hpp>
#include <iostream>

int main() {
    // Load the image
    cv::Mat img = cv::imread("input.jpg", cv::IMREAD_COLOR);
    if (img.empty()) {
        std::cerr << "Error: Could not load image!" << std::endl;
        return -1;
    }

    // Define rectangle coordinates
    cv::Point pt1(50, 50);   // Top-left corner
    cv::Point pt2(200, 200); // Bottom-right corner

    // Draw rectangle (Red color, thickness 2)
    cv::rectangle(img, pt1, pt2, cv::Scalar(0, 0, 255), 2);

    // Show and save the modified image
    cv::imshow("Modified Image", img);

    if (!cv::imwrite("output_with_rectangle.jpg", img)) {
        std::cerr << "Error: Could not save the modified image!" << std::endl;
        return -1;
    }

    cv::waitKey(0);

    return 0;
}
```

---

## **4. Generate a random image and save it as a PNG file.**  
### **Corrections and Improvements:**
- Used `cv::RNG` for random number generation, which is more suitable in OpenCV.
- Improved performance by avoiding multiple calls to `rand()`.

### **Code:**
```cpp
#include <opencv2/opencv.hpp>
#include <iostream>

int main() {
    int width = 256, height = 256;
    cv::Mat img(height, width, CV_8UC3);  // 3-channel image

    // Using OpenCV RNG
    cv::RNG rng(cv::getTickCount());

    for (int y = 0; y < height; y++) {
        for (int x = 0; x < width; x++) {
            img.at<cv::Vec3b>(y, x) = cv::Vec3b(
                rng.uniform(0, 256),  // Blue
                rng.uniform(0, 256),  // Green
                rng.uniform(0, 256)   // Red
            );
        }
    }

    // Save the random image
    if (!cv::imwrite("random_image.png", img)) {
        std::cerr << "Error: Could not save random image!" << std::endl;
        return -1;
    }

    // Display the image
    cv::imshow("Random Image", img);
    cv::waitKey(0);

    return 0;
}
```

---

## **5. Add noise to an image and then remove it using filtering.**  
### **Corrections and Improvements:**
- Optimized noise addition by using `cv::randn()` for Gaussian noise.
- Made the filtering step more efficient.

### **Code:**
```cpp
#include <opencv2/opencv.hpp>
#include <iostream>

// Function to add noise to an image
void addNoise(cv::Mat& img) {
    cv::Mat noise(img.size(), img.type());
    cv::randn(noise, 0, 25);  // Gaussian noise with mean 0 and stddev 25
    img += noise;
}

int main() {
    // Load the image
    cv::Mat img = cv::imread("input.jpg", cv::IMREAD_COLOR);
    if (img.empty()) {
        std::cerr << "Error: Could not load image!" << std::endl;
        return -1;
    }

    // Create noisy image
    cv::Mat noisyImg = img.clone();
    addNoise(noisyImg);

    // Filter the noisy image
    cv::Mat filteredImg;
    cv::GaussianBlur(noisyImg, filteredImg, cv::Size(5, 5), 0);

    // Save images
    if (!cv::imwrite("noisy_image.jpg", noisyImg) || 
        !cv::imwrite("filtered_image.jpg", filteredImg)) {
        std::cerr << "Error: Could not save images!" << std::endl;
        return -1;
    }

    // Show images
    cv::imshow("Original Image", img);
    cv::imshow("Noisy Image", noisyImg);
    cv::imshow("Filtered Image", filteredImg);

    cv::waitKey(0);

    return 0;
}
```

---

### **Summary of Corrections:**
- Improved error handling for image loading and saving.
- Used `cv::RNG` for random number generation for better performance.
- Optimized noise addition using `cv::randn()` for Gaussian noise.
- Enhanced readability and maintainability of the code.
- Consistency in OpenCV function usage and parameter choices.