
## 📷 Challenge Prompt

**Name**: (not provided)  
**Context**: A photo taken by Haary was allegedly stolen by his neighbor Melchior and used to win a photo contest. Haary claims to have proof the image is his, because he embedded a hidden mark in it.

> "Parfois, les détails se cachent dans le creux des vaguelettes."

**Goal**: Extract the hidden flag of the form `MAIF{...}` from the image.

---

## 🔍 Initial Recon

Image format: PNG  
![photo-concours](https://github.com/user-attachments/assets/976459f3-1d06-4918-bc88-a71d19d7d06c)

Tools tested:

- `strings` (no result)
    
- `steghide` (nothing hidden in metadata or embedded)
    
- `binwalk`, `zsteg`, `exiftool`, and other classic steg tools → ❌ No useful data found
    

The clue suggests that the hidden data is within the **wavelet** structure of the image, likely via **Haar wavelet transform** (`pywt`), hinted by:

- The phrase "dans le creux des vaguelettes" (likely referring to wavelets)
    
- The name "Haary" = pun on Haar
    
- Image looks like ripples = natural match for wavelet domain hiding
    

---

## 🧪 Attack Strategy

### Step 1 – Load and Preprocess the Image

```python
from PIL import Image
import numpy as np
image = Image.open("image.jpg").convert("L")
data = np.array(image, dtype=np.float32)
```

### Step 2 – Apply Haar Wavelet Transform (Level 1)

```python
import pywt
coeffs2 = pywt.dwt2(data, 'haar')
cA, (cH, cV, cD) = coeffs2
```

### Step 3 – Visualize Coefficients

```python
import matplotlib.pyplot as plt
fig, axes = plt.subplots(1, 4, figsize=(16, 4))
axes[0].imshow(cA, cmap='gray'); axes[0].set_title("Approximation")
axes[1].imshow(cH, cmap='gray'); axes[1].set_title("Level 1 - cH")
axes[2].imshow(cV, cmap='gray'); axes[2].set_title("Level 1 - cV")
axes[3].imshow(cD, cmap='gray'); axes[3].set_title("Level 1 - cD")
plt.suptitle("Wavelet: haar, Level: 1")
plt.show()
```

### ✅ Result

Hidden text **"MAIFCESTMAPHOTO"** was clearly visible in **Level 1 Vertical Details (cV)**.

---

## 🏁 Final Flag

```
MAIF{CESTMAPHOTO}
```

---

## 🧠 Lessons Learned

- Wavelet steganography is subtle and powerful – the message was invisible to basic steg tools.
    
- Visual inspection of wavelet domains (especially detail coefficients) can reveal embedded information.
    
- Clues like “vaguelettes” or visual motifs (ripples) are strong hints toward using transforms like DWT.
    

---
