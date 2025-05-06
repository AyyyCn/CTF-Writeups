
**Category**: Misc  
**Challenge Name**: base-X  
**Tags**: `#Base65536` `#base64` `#tar` `#image-decoding` `#Base256`

---

## 📷 Challenge Prompt

We were given a file `base.txt` and a hint:

> _"Whatever happens, keep going."_

This hinted at **multiple layers of encoding**, likely requiring a chained decoding process.

---

## 🔓 Solution

### Step 1: Initial Observations & Failed Attempts

We started by assuming the file contained data encoded in a traditional base such as Base64, Base32, Base85, or even hexadecimal. We wrote a script to try decoding the content using **all bases from 2 to 128**, interpreting the string as a number and attempting to reconstruct text. However, nothing yielded readable output.

We then noticed the file contained a **large sequence of unusual Unicode characters**, including some that resembled **Chinese ideographs**. This strongly hinted that the data might be encoded using **Base65536**, a rare encoding that maps two bytes of data to a single Unicode character from the BMP (Basic Multilingual Plane).

---

### Step 2: Base65536 Decoding

We confirmed the format and decoded the content using Python:

```python
import base65536

with open("base.txt", "r", encoding="utf-8") as f:
    base65536_data = f.read().strip()

base64_bytes = base65536.decode(base65536_data)
```

The output of this step appeared to be a **base64 string**, which we verified using **CyberChef**. CyberChef also detected that decoding this base64 string resulted in a **TAR archive**.

---

### Step 3: Base64 Decoding & TAR Extraction

```python
import base64
import tarfile
import io

# Base64 decode
tar_bytes = base64.b64decode(base64_bytes)

# Extract TAR content
with tarfile.open(fileobj=io.BytesIO(tar_bytes), mode='r:*') as tar:
    tar.extractall("output")
```

We extracted a file named `flag.png`.

---

### Step 4: Analyze the PNG

The image displayed 16 colored blocks. We extracted the RGB values using `PIL` and decoded the data in two ways:

#### Red Channel Only:

```python
decoded = ''.join(chr(r) for r, g, b in colors if 32 <= r < 127)
# Output: Ceee{68490710
```

This seemed like the beginning of a flag but was clearly incomplete.

#### Base256 Full RGB Decoding:

```python
# Treat RGB triplets as sequential bytes
byte_data = bytes([value for triplet in colors for value in triplet])
decoded = byte_data.decode('utf-8', errors='ignore')
# Output: Cyberbenders{586cf8c849c9730ea7b21120}
```

This gave us the **full flag**, confirming each RGB component contributed one byte to the final message.

---

## 🎉 Flag

```
Cyberbenders{586cf8c849c9730ea7b21120}
```

---

## 🧠 Notes

- Base65536 is rarely used in real-life apps but common in CTFs involving Unicode trickery.
    
- The image was not meant to be visually decoded but processed programmatically using color analysis.
    
- Combining all RGB values into a byte stream (Base256) is a powerful method when each pixel or block encodes structured data.
    

---