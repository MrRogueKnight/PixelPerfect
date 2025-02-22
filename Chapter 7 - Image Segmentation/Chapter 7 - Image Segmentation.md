

---

# **📖 Chapter 7: Image Segmentation**  

This chapter covers:  
✔ **Image Segmentation Basics** – Dividing images into meaningful regions  
✔ **Thresholding Techniques** – Binary, adaptive, and Otsu’s thresholding  
✔ **Edge-Based Segmentation** – Canny edge detection  
✔ **Region-Based Segmentation** – Connected components and watershed  
✔ **Advanced Techniques** – GrabCut for foreground extraction  

---

## **1️⃣ Thresholding Techniques**  
### **Binary Thresholding**  
```cpp
#include <opencv2/opencv.hpp>

int main() {
    cv::Mat img = cv::imread("input.jpg", cv::IMREAD_GRAYSCALE);
    if(img.empty()) {
        std::cout << "Error loading image!\n";
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
**💡 Key Points:**  
- Use `cv::threshold()` with `cv::THRESH_BINARY`  
- Ideal for images with clear foreground/background separation  

---

### **Adaptive Thresholding**  
```cpp
cv::Mat adaptiveThresh;
cv::adaptiveThreshold(img, adaptiveThresh, 255, 
                     cv::ADAPTIVE_THRESH_GAUSSIAN_C,
                     cv::THRESH_BINARY, 11, 2);
```
**Parameters:**  
- `11`: Block size (odd number)  
- `2`: Constant subtracted from mean  

---

### **Otsu's Thresholding**  
```cpp
double otsu_thresh = cv::threshold(img, binary, 0, 255, 
                                  cv::THRESH_BINARY | cv::THRESH_OTSU);
std::cout << "Otsu's threshold: " << otsu_thresh << std::endl;
```
**Best for:** Images with bimodal intensity distributions  

---

## **2️⃣ Edge-Based Segmentation (Canny Edge Detector)**  
```cpp
cv::Mat edges;
cv::Canny(img, edges, 100, 200);  // Lower & upper thresholds

// Enhance visualization
cv::Mat edge_visual;
cv::cvtColor(edges, edge_visual, cv::COLOR_GRAY2BGR);
edge_visual.setTo(cv::Scalar(0, 255, 0), edges);  // Green edges

cv::imshow("Canny Edges", edge_visual);
```
**Pro Tip:** Combine with Gaussian blur for noise reduction:  
```cpp
cv::GaussianBlur(img, img, cv::Size(3,3), 0);
```

---

## **3️⃣ Region-Based Segmentation**  
### **Connected Components Labeling**  
```cpp
cv::Mat labels, stats, centroids;
int num_labels = cv::connectedComponentsWithStats(binary, labels, stats, centroids);

// Create color-coded visualization
cv::Mat label_visual(labels.size(), CV_8UC3);
for(int i=1; i<num_labels; i++) { // Skip background (0)
    cv::Vec3b color(rand()%256, rand()%256, rand()%256);
    label_visual.setTo(color, labels == i);
}

cv::imshow("Connected Components", label_visual);
```

---

### **Watershed Algorithm**  
```cpp
// Create markers using thresholding
cv::Mat markers(binary.size(), CV_32S);
cv::Mat sure_bg;
cv::dilate(binary, sure_bg, cv::Mat(), cv::Point(-1,-1), 3);
cv::connectedComponents(sure_bg, markers);

// Apply watershed
cv::Mat color_img = cv::imread("input.jpg");
cv::watershed(color_img, markers);

// Visualize boundaries
color_img.setTo(cv::Scalar(0,255,0), markers == -1);
cv::imshow("Watershed Result", color_img);
```

---

## **4️⃣ Advanced Technique: GrabCut**  
```cpp
cv::Mat mask, bg_model, fg_model;
cv::Rect rect(50,50,400,300);  // Region containing foreground

cv::grabCut(img, mask, rect, bg_model, fg_model, 5, cv::GC_INIT_WITH_RECT);

// Refine mask
cv::Mat foreground = (mask == cv::GC_PR_FGD) | (mask == cv::GC_FGD);
cv::Mat result;
img.copyTo(result, foreground);

cv::imshow("GrabCut Result", result);
```

---

## **5️⃣ Best Practices & Pro Tips**  
1. **Preprocessing:**  
```cpp
cv::medianBlur(img, img, 3);  // Remove salt-and-pepper noise
cv::normalize(img, img, 0, 255, cv::NORM_MINMAX); // Enhance contrast
```

2. **Multi-Channel Processing:**  
```cpp
std::vector<cv::Mat> channels;
cv::split(color_img, channels);
// Process individual channels (e.g., channels[0] for BGR blue)
```

3. **Performance Optimization:**  
```cpp
cv::UMat uimg, uedges;  // Use OpenCL-accelerated matrices
img.copyTo(uimg);
cv::Canny(uimg, uedges, 100, 200);
```

4. **Visualization Tricks:**  
```cpp
cv::applyColorMap(labels, label_visual, cv::COLORMAP_JET);  // Heatmap
```

---

## **6️⃣ Exercises**  
1️⃣ **Implement automatic threshold selection using histogram analysis**  
2️⃣ **Compare watershed vs. GrabCut for object segmentation**  
3️⃣ **Create a real-time video segmentation pipeline**  
4️⃣ **Develop a custom region-growing algorithm**  

---

## **Key OpenCV Functions**  
| Function | Purpose |  
|----------|---------|  
| `cv::threshold()` | Basic thresholding operations |  
| `cv::adaptiveThreshold()` | Local threshold adaptation |  
| `cv::connectedComponents()` | Label connected regions |  
| `cv::watershed()` | Marker-based segmentation |  
| `cv::grabCut()` | Interactive foreground extraction |  

