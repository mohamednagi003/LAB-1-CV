# LAB-1-CV
# Seam Carving: Content-Aware Image Resizing

This project implements an optimized version of the **seam carving algorithm** for content-aware image resizing. Seam carving intelligently resizes images by removing vertical seams (paths of least importance) while preserving critical visual content.

---

## Features
- **Energy Map Calculation**: Highlights important regions in the image using pixel gradients.
- **Dynamic Seam Selection**: Finds the vertical seam with the least cumulative energy using dynamic programming.
- **Incremental Energy Updates**: Optimizes performance by updating only the affected areas of the energy map after each seam removal.
- **Visualization**: Displays the original and resized images side by side using Matplotlib.

---

## Prerequisites
Make sure you have the following installed:
- Python 3.x
- `opencv-python`
- `numpy`
- `matplotlib`

Install required packages using pip:
```bash
pip install opencv-python numpy matplotlib
```

---



## How It Works

### Energy Map Calculation
The energy map highlights edges and important features in the image. The Sobel operator is used to compute gradients in the x and y directions:
```python
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
dx = np.abs(cv2.Sobel(gray, cv2.CV_64F, 1, 0, ksize=3))
dy = np.abs(cv2.Sobel(gray, cv2.CV_64F, 0, 1, ksize=3))
energy = dx + dy
```

### Seam Identification
Dynamic programming identifies the vertical seam with the least cumulative energy, ensuring that seams are connected across rows:
```python
for i in range(1, rows):
    for j in range(cols):
        min_energy = dp[i-1, j]
        min_index = j
        if j > 0 and dp[i-1, j-1] < min_energy:
            min_energy = dp[i-1, j-1]
            min_index = j-1
        if j < cols-1 and dp[i-1, j+1] < min_energy:
            min_energy = dp[i-1, j+1]
            min_index = j+1
        dp[i, j] += min_energy
        backtrack[i, j] = min_index
```

### Seam Removal
The identified seam is removed row by row to reduce the image width:
```python
output[i, :, :] = np.delete(img[i, :, :], seam[i], axis=0)
```

### Incremental Energy Map Update
Instead of recalculating the entire energy map after each seam removal, only the affected areas are updated:
```python
for i in range(rows):
    col = seam[i]
    start = max(col - 1, 0)
    end = min(col + 2, cols - 1)
    gray = cv2.cvtColor(img[i : i + 1, start:end], cv2.COLOR_BGR2GRAY)
    dx = np.abs(cv2.Sobel(gray, cv2.CV_64F, 1, 0, ksize=3))
    dy = np.abs(cv2.Sobel(gray, cv2.CV_64F, 0, 1, ksize=3))
    energy[i, start:end] = dx + dy
```

---



## Future Enhancements
- Add support for **horizontal seam removal** to resize height.
- Allow **region preservation** using masks to protect specific areas.
- Optimize performance further with **parallel processing**.

---


