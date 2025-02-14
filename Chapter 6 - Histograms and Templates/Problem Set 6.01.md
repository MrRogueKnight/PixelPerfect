Let's get hands-on with **coding exercises for Chapter 6** and master advanced techniques using **histograms and template matching in OpenCV**! 🚀  

---

# **1️⃣ Analyze Image Brightness and Contrast Using Histograms**  
### **Expected Behavior:**  
- Load an image and **compute its histogram**.  
- **Analyze brightness and contrast** by observing the histogram distribution.  
- **Display the histogram** alongside the original image.  

### **Solution (Python with OpenCV and Matplotlib)**
```python
import cv2
from matplotlib import pyplot as plt

# Load image in grayscale
img = cv2.imread('input.jpg', cv2.IMREAD_GRAYSCALE)

# Calculate histogram
hist = cv2.calcHist([img], [0], None, [256], [0, 256])

# Display image and histogram
cv2.imshow("Original Image", img)

plt.figure()
plt.title("Histogram")
plt.xlabel("Intensity Value")
plt.ylabel("Pixel Count")
plt.plot(hist, color='black')
plt.xlim([0, 256])
plt.show()

cv2.waitKey(0)
cv2.destroyAllWindows()
```

✅ **Key Takeaways:**  
✔ `cv2.calcHist()` → **Calculates the histogram** of an image.  
✔ **Peaks on the left** → Dark image (underexposed).  
✔ **Peaks on the right** → Bright image (overexposed).  
✔ **Uniform distribution** → Good contrast.  

---

# **2️⃣ Enhance Image Contrast Using Histogram Equalization**  
### **Expected Behavior:**  
- Load an image and **apply histogram equalization**.  
- **Improve contrast** and **enhance details**.  
- **Compare the original and equalized histograms**.  

### **Solution (Python with OpenCV and Matplotlib)**
```python
import cv2
from matplotlib import pyplot as plt

# Load image in grayscale
img = cv2.imread('input.jpg', cv2.IMREAD_GRAYSCALE)

# Apply histogram equalization
equalized_img = cv2.equalizeHist(img)

# Calculate histograms
hist_original = cv2.calcHist([img], [0], None, [256], [0, 256])
hist_equalized = cv2.calcHist([equalized_img], [0], None, [256], [0, 256])

# Display images and histograms
cv2.imshow("Original Image", img)
cv2.imshow("Equalized Image", equalized_img)

plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
plt.title("Original Histogram")
plt.plot(hist_original, color='black')
plt.xlim([0, 256])

plt.subplot(1, 2, 2)
plt.title("Equalized Histogram")
plt.plot(hist_equalized, color='black')
plt.xlim([0, 256])

plt.show()

cv2.waitKey(0)
cv2.destroyAllWindows()
```

✅ **Key Takeaways:**  
✔ `cv2.equalizeHist()` → **Equalizes the histogram** to enhance contrast.  
✔ **Stretches intensity values** across the entire range (0-255).  
✔ **Details are enhanced** in low-contrast images.  

---

# **3️⃣ Perform Template Matching to Find Objects in an Image**  
### **Expected Behavior:**  
- Load a main image and a smaller **template image**.  
- **Locate the template** within the main image.  
- **Draw a bounding box** around the best matching location.  

### **Solution (Python with OpenCV)**
```python
import cv2

# Load main image and template
img = cv2.imread('main.jpg', cv2.IMREAD_GRAYSCALE)
template = cv2.imread('template.jpg', cv2.IMREAD_GRAYSCALE)
w, h = template.shape[::-1]

# Perform template matching
result = cv2.matchTemplate(img, template, cv2.TM_CCOEFF_NORMED)
min_val, max_val, min_loc, max_loc = cv2.minMaxLoc(result)

# Draw rectangle around best match
top_left = max_loc
bottom_right = (top_left[0] + w, top_left[1] + h)
cv2.rectangle(img, top_left, bottom_right, (255, 0, 0), 2)

# Display result
cv2.imshow("Template Matching", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

✅ **Key Takeaways:**  
✔ `cv2.matchTemplate()` → **Slides template over the main image**.  
✔ `cv2.TM_CCOEFF_NORMED` → **Normalized correlation coefficient** for template matching.  
✔ **Draws a bounding box** around the best match.  

---

# **4️⃣ Apply Back Projection to Locate Colored Objects**  
### **Expected Behavior:**  
- Load an image and **select a region of interest (ROI)**.  
- **Calculate the color histogram** of the ROI.  
- **Locate and highlight regions** in the image that match the ROI color.  

### **Solution (Python with OpenCV)**
```python
import cv2
import numpy as np

# Load image and convert to HSV
img = cv2.imread('input.jpg')
hsv_img = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

# Select Region of Interest (ROI)
x, y, w, h = 100, 100, 50, 50  # Example coordinates
roi = hsv_img[y:y+h, x:x+w]

# Calculate histogram of ROI
roi_hist = cv2.calcHist([roi], [0, 1], None, [180, 256], [0, 180, 0, 256])
cv2.normalize(roi_hist, roi_hist, 0, 255, cv2.NORM_MINMAX)

# Apply Back Projection
back_proj = cv2.calcBackProject([hsv_img], [0, 1], roi_hist, [0, 180, 0, 256], 1)

# Apply convolution and thresholding for better visualization
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
back_proj = cv2.filter2D(back_proj, -1, kernel)
_, thresh = cv2.threshold(back_proj, 50, 255, cv2.THRESH_BINARY)

# Merge threshold with original image
result = cv2.bitwise_and(img, img, mask=thresh)

# Display results
cv2.imshow("Original Image", img)
cv2.imshow("Back Projection", back_proj)
cv2.imshow("Detected Regions", result)

cv2.waitKey(0)
cv2.destroyAllWindows()
```

✅ **Key Takeaways:**  
✔ `cv2.calcBackProject()` → **Locates regions** that match the ROI color.  
✔ **Uses color histograms** for object localization.  
✔ **Great for color-based tracking** (e.g., tracking a red ball).  

---

# **📌 Summary of What You Practiced:**  
✔ **Analyze brightness and contrast** using histograms.  
✔ **Enhance image contrast** with histogram equalization.  
✔ **Locate objects** with template matching.  
✔ **Find colored objects** using back projection.  

---

# **🚀 More Advanced Histogram and Template Matching Challenges – Are You Ready?**  
🔥 **1. Implement multi-channel histogram analysis (RGB).**  
🔥 **2. Perform multi-scale template matching for size-invariant detection.**  
🔥 **3. Develop a color-based object tracker using back projection and CamShift.**  