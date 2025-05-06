


## 📷 Challenge Prompt

> _"I decided to create a simple image-to-audio conversion method, which I used to hide a secret image. Can you retrieve it?"_

We were given a `.wav` file and a clue suggesting that an image had been converted into sound samples, likely through mapping RGB pixel values into audio samples.

### 🧠 Initial Observations

- The audio waveform showed **three distinct zones**, hinting at a possible **RGB chunk mapping**.
    
- The challenge title "Pixels and Samples" confirmed the idea of converting image pixels into audio samples.
    
- Steganographic hint: the solution involves **extracting and reversing** the transformation.
    

---

## 🔍 Strategy

### 1. **Audio Sample Extraction**

Using Python and the `wave` module:

- Opened the `.wav` file and extracted raw samples.
    
- Converted them to 8-bit format, handling possible 16-bit signed conversion.
    

```python
if samp_width == 2:
    data = ((np.frombuffer(raw_data, dtype=np.int16).astype(np.int32) + 32768) >> 8).clip(0, 255).astype(np.uint8)
```

### 2. **RGB Reconstruction**

Two hypotheses were tested:

- **Chunk-based RGB**: first 1/3 = R, second 1/3 = G, last 1/3 = B
    
- **Interleaved RGB**: every 3rd sample is R, G, B respectively
    

We used NumPy to split and reshape the samples.

### 3. **Bruteforce Dimensions**

Since image dimensions were unknown:

- We factored the total number of pixels to generate all possible `(height × width)` combinations.
    
- We saved every possibility in a folder for visual inspection.
    

```python
for (h, w) in factor_pairs(num_pixels):
    rgb = np.column_stack((R, G, B)).reshape((h, w, 3))
    Image.fromarray(rgb, "RGB").save(f"output_images/interleaved_{h}x{w}.png")
```

---

## 🖼️ Solution

- The image that revealed the flag was: `gray_1273x2325.png`
    ![[gray_1273x2325 1.png]]
- It showed a **cat wrapped in a blanket** with a caption
- After horizontally flipping the image, the flag was clearly readable:
    

```
Securinets{d1d_y0u_3nj0y_th3_mus1c????}
```

---

## ✅ Lessons Learned

- Audio files can encode images by using **sample-level pixel mapping**.
    
- RGB mapping can be **chunked or interleaved**, and both should be tested.
    
- Brute-forcing dimensions is a powerful fallback when metadata is missing.
    
- Visual clues in stego images often provide final steps (e.g. **mirroring** the image).
    

---

## 🧩 Flag

```
Securinets{d1d_y0u_3nj0y_th3_mus1c????}
```

---

**Tools Used**: Python, NumPy, Pillow, `wave`, Obsidian

**Category**: Steganography / Forensics

**Difficulty**: 🟨 Medium