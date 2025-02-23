Let's get hands-on with coding exercises for Chapter 7 and master image segmentation techniques in OpenCV! 🚀

---

## **1️⃣ Binary Thresholding (C++)**
```cpp
#include <opencv2/opencv.hpp>

int main() {
    cv::Mat img = cv::imread("input.jpg", cv::IMREAD_GRAYSCALE);
    if(img.empty()) {
        std::cerr << "Error loading image!" << std::endl;
        return -1;
    }

    cv::Mat binary;
    cv::threshold(img, binary, 127, 255, cv::THRESH_BINARY);

    cv::imshow("Original", img);
    cv::imshow("Binary Threshold", binary);
    cv::waitKey(0);
    
    return 0;
}
```

---

## **2️⃣ Canny Edge Detection (C++)**
```cpp
#include <opencv2/opencv.hpp>

int main() {
    cv::Mat img = cv::imread("input.jpg", cv::IMREAD_GRAYSCALE);
    if(img.empty()) {
        std::cerr << "Error loading image!" << std::endl;
        return -1;
    }

    cv::Mat edges;
    cv::Canny(img, edges, 100, 200);

    // Create colored edge visualization
    cv::Mat edge_visual;
    cv::cvtColor(img, edge_visual, cv::COLOR_GRAY2BGR);
    edge_visual.setTo(cv::Scalar(0, 255, 0), edges);

    cv::imshow("Canny Edges", edge_visual);
    cv::waitKey(0);

    return 0;
}
```

---

## **3️⃣ Otsu's Thresholding (C++)**
```cpp
#include <opencv2/opencv.hpp>

int main() {
    cv::Mat img = cv::imread("input.jpg", cv::IMREAD_GRAYSCALE);
    if(img.empty()) {
        std::cerr << "Error loading image!" << std::endl;
        return -1;
    }

    cv::Mat otsu;
    double thresh = cv::threshold(img, otsu, 0, 255, 
                                cv::THRESH_BINARY | cv::THRESH_OTSU);
    std::cout << "Otsu's threshold: " << thresh << std::endl;

    cv::imshow("Otsu Result", otsu);
    cv::waitKey(0);

    return 0;
}
```

---

## **4️⃣ Connected Components (C++)**
```cpp
#include <opencv2/opencv.hpp>
#include <random>

int main() {
    cv::Mat img = cv::imread("input.jpg", cv::IMREAD_GRAYSCALE);
    if(img.empty()) {
        std::cerr << "Error loading image!" << std::endl;
        return -1;
    }

    cv::Mat binary;
    cv::threshold(img, binary, 127, 255, cv::THRESH_BINARY);

    cv::Mat labels, stats, centroids;
    int num_labels = cv::connectedComponentsWithStats(binary, labels, stats, centroids);

    // Create color-coded visualization
    std::vector<cv::Vec3b> colors(num_labels);
    std::mt19937 rng(12345);
    std::uniform_int_distribution<int> dist(0, 255);
    
    colors[0] = cv::Vec3b(0, 0, 0); // Background
    for(int i=1; i<num_labels; i++) {
        colors[i] = cv::Vec3b(dist(rng), dist(rng), dist(rng));
    }

    cv::Mat colored(img.size(), CV_8UC3);
    for(int y=0; y<colored.rows; y++) {
        for(int x=0; x<colored.cols; x++) {
            int label = labels.at<int>(y, x);
            colored.at<cv::Vec3b>(y, x) = colors[label];
        }
    }

    cv::imshow("Connected Components", colored);
    cv::waitKey(0);

    return 0;
}
```

---

## **5️⃣ GrabCut Segmentation (C++)**
```cpp
#include <opencv2/opencv.hpp>

int main() {
    cv::Mat img = cv::imread("input.jpg");
    if(img.empty()) {
        std::cerr << "Error loading image!" << std::endl;
        return -1;
    }

    cv::Mat mask, bgModel, fgModel;
    cv::Rect rect(50, 50, 400, 400); // ROI containing object

    // Initialize mask
    mask.create(img.size(), CV_8UC1);
    mask.setTo(cv::GC_BGD);
    rect.x = std::max(0, rect.x);
    rect.y = std::max(0, rect.y);
    rect.width = std::min(rect.width, img.cols-rect.x);
    rect.height = std::min(rect.height, img.rows-rect.y);
    mask(rect).setTo(cv::GC_PR_FGD);

    // Apply GrabCut
    cv::grabCut(img, mask, rect, bgModel, fgModel, 5, cv::GC_INIT_WITH_MASK);

    // Create foreground mask
    cv::Mat foreground = (mask == cv::GC_FGD) | (mask == cv::GC_PR_FGD);
    cv::Mat result;
    img.copyTo(result, foreground);

    cv::imshow("GrabCut Result", result);
    cv::waitKey(0);

    return 0;
}
```

---

### **Key Differences from Python:**
1. **Memory Management:** Uses RAII (automatic memory management) with `cv::Mat`
2. **Type Safety:** Strong typing for matrices and operations
3. **Performance:** Direct access to matrix elements using `at<>()` method
4. **Visualization:** More explicit color space conversions and mask operations

### **Best Practices:**
- Always check if images load successfully
- Use `cv::Mat::empty()` for image validity checks
- Prefer modern OpenCV C++ API (post version 2.4)
- Use `cv::imshow()` for debugging and visualization
- Compile with proper OpenCV linking:
  ```bash
  g++ -std=c++11 your_code.cpp -o output `pkg-config --cflags --libs opencv4`
  ```

