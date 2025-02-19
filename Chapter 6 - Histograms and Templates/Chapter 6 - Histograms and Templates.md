

---

# **📖 Chapter 6: Histograms and Templates**  

This chapter covers:  
✔ **Histograms for Image Analysis** – Analyzing pixel intensity distributions.  
✔ **Histogram Equalization** – Enhancing image contrast.  
✔ **Template Matching** – Finding objects within images.  
✔ **Back Projection** – Locating objects using color histograms.  

---

## **1️⃣ Understanding Histograms in Image Processing**  
### **What is a Histogram?**  
- A **histogram** represents the **distribution of pixel intensities** in an image.  
- **Grayscale:** 1D distribution (0-255).  
- **Color:** 3D distribution (BGR channels).  

### **Computing Histograms (Modern C++ API)**  
```cpp
#include <opencv2/opencv.hpp>

int main() {
    cv::Mat img = cv::imread("input.jpg", cv::IMREAD_GRAYSCALE);
    if (img.empty()) {
        std::cout << "Error: Image not found!\n";
        return -1;
    }

    // Configure histogram
    int histSize = 256;    // Number of bins
    float range[] = {0, 256};
    const float* histRange = {range};
    bool uniform = true, accumulate = false;

    // Calculate histogram
    cv::Mat hist;
    cv::calcHist(&img, 1, 0, cv::Mat(), hist, 1, &histSize, &histRange, uniform, accumulate);

    // Plot histogram (optional)
    int bin_w = 2;
    cv::Mat histImg(256, 256, CV_8UC1, cv::Scalar(255));
    cv::normalize(hist, hist, 0, histImg.rows, cv::NORM_MINMAX);

    for (int i=0; i<histSize; i++) {
        cv::line(histImg, cv::Point(bin_w*i, 256),
                 cv::Point(bin_w*i, 256 - cvRound(hist.at<float>(i))),
                 cv::Scalar(0));
    }

    cv::imshow("Histogram", histImg);
    cv::waitKey(0);
    return 0;
}
```
✅ **Key Takeaways:**  
✔ Use `cv::calcHist()` for efficient histogram calculation.  
✔ Visualize histograms to analyze brightness/contrast issues.  

---

## **2️⃣ Histogram Equalization (Contrast Enhancement)**  
### **Why Equalize?**  
- **Redistributes intensity values** to cover full 0-255 range.  
- **Improves visibility** in low-contrast images.  

### **Modern Equalization Code**  
```cpp
cv::Mat equalizeHistogram(const cv::Mat& input) {
    cv::Mat output;
    cv::equalizeHist(input, output);
    return output;
}

int main() {
    cv::Mat img = cv::imread("low_contrast.jpg", cv::IMREAD_GRAYSCALE);
    cv::Mat equalized = equalizeHistogram(img);

    cv::imshow("Original", img);
    cv::imshow("Equalized", equalized);
    cv::waitKey(0);
    return 0;
}
```
**💡 Pro Tip:** For color images, convert to **HSV/YCrCb** and equalize the luminance channel only.  

---

## **3️⃣ Template Matching**  
### **Find Objects Using `cv::matchTemplate()`**  
```cpp
cv::Mat img = cv::imread("scene.jpg", cv::IMREAD_COLOR);
cv::Mat templ = cv::imread("object.jpg", cv::IMREAD_COLOR);

// Create result matrix
cv::Mat result;
cv::matchTemplate(img, templ, result, cv::TM_CCOEFF_NORMED);

// Find best match
double minVal, maxVal;
cv::Point minLoc, maxLoc;
cv::minMaxLoc(result, &minVal, &maxVal, &minLoc, &maxLoc);

// Draw rectangle around match
cv::rectangle(img, maxLoc, cv::Point(maxLoc.x + templ.cols, maxLoc.y + templ.rows), 
              cv::Scalar(0, 255, 0), 2);

cv::imshow("Result", img);
cv::waitKey(0);
```
**✅ Matching Methods:**  
- `TM_SQDIFF`: Best for exact matches  
- `TM_CCOEFF_NORMED`: Robust to lighting changes  

---

## **4️⃣ Back Projection**  
### **Color-Based Object Localization**  
```cpp
cv::Mat target = cv::imread("target.jpg");       // Object to find
cv::Mat scene = cv::imread("scene.jpg");         // Search area

// Convert to HSV
cv::Mat hsv_target, hsv_scene;
cv::cvtColor(target, hsv_target, cv::COLOR_BGR2HSV);
cv::cvtColor(scene, hsv_scene, cv::COLOR_BGR2HSV);

// Calculate histogram of target object
int channels[] = {0};  // Hue channel
int histSize[] = {180}; // 0-180 range for Hue
float range[] = {0, 180};
const float* ranges[] = {range};

cv::Mat hist;
cv::calcHist(&hsv_target, 1, channels, cv::Mat(), hist, 1, histSize, ranges);

// Normalize and back project
cv::normalize(hist, hist, 0, 255, cv::NORM_MINMAX);
cv::Mat backProj;
cv::calcBackProject(&hsv_scene, 1, channels, hist, backProj, ranges);

// Threshold to find probable regions
cv::threshold(backProj, backProj, 50, 255, cv::THRESH_BINARY);
cv::imshow("Back Projection", backProj);
cv::waitKey(0);
```
**💡 Pro Tip:** Use **morphological operations** to clean up the back projection result.  

---

## **5️⃣ Exercises**  
1️⃣ **Compare histograms of underexposed vs. properly exposed images.**  
2️⃣ **Implement adaptive histogram equalization using `cv::createCLAHE()`.**  
3️⃣ **Detect multiple template matches in an image using thresholding on result matrix.**  
4️⃣ **Track a colored object in a video using back projection.**  

---

## **📌 Key Concepts**  
| **Concept** | **Use Case** | **OpenCV Function** |
|-------------|--------------|---------------------|
| **Histogram** | Analyze intensity distribution | `cv::calcHist()` |
| **Equalization** | Enhance contrast | `cv::equalizeHist()` |
| **Template Matching** | Object detection | `cv::matchTemplate()` |
| **Back Projection** | Color-based localization | `cv::calcBackProject()` |

---

### **Best Practices**  
- **For Color Images:**  
  - Use **HSV color space** for histogram analysis (separates color from brightness).  
  - Equalize only the **Value (V)** channel to avoid color distortion.  
- **Template Matching:**  
  - Resize templates proportionally for scale invariance.  
  - Use **multi-scale matching** for objects of unknown size.  
- **Back Projection:**  
  - Combine with **CAMShift algorithm** for robust object tracking.  

