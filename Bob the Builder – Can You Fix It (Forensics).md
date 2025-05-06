
## 📷 Challenge Prompt

> _"bob the builder can u fix it? yes we can!"_

We were given a mysterious file:

```
flag.atast
```

Running the `file` or `magic` command on it returned:

```
data
```

---

## 🔍 Step 1: Investigating the File

Using the `strings` command:

```
strings flag.atast
```

We spotted familiar PNG chunk markers:

```
IHDR, IDAT, IEND, sRGB, gAMA, pHYs
```

This hinted that the file is **likely a corrupted PNG** image.

---

## 🔎 Step 2: Hex Inspection

Running a hex dump (`xxd` or `hexdump`) revealed the file starts with:

```
2e 38 39 2e 0d 0a 1a 0a ...
```

This does **not match the PNG header**, which should be:

```
89 50 4E 47 0D 0A 1A 0A
```

---

## 🧹 Step 3: Repairing the PNG

To fix the file, we replaced the corrupted header with the correct one using Python:

```python
correct_header = bytes.fromhex("89504E470D0A1A0A")

with open("flag.atast", "rb") as f:
    corrupted_data = f.read()

with open("repaired_image.png", "wb") as f:
    f.write(correct_header + corrupted_data[8:])
```

---

## 🖼️ Step 4: Viewing the Image

Opening `repaired_image.png` displayed the flag:
![[Pasted image 20250506181951.png]]

```
Cyberbenders{corrupt_image_rescuer}
```

---

## 🏋️‍♂️ Flag

```
Cyberbenders{corrupt_image_rescuer}
```

---

## ✅ Takeaways

- Even when `file` fails, `strings` and `hex` can reveal format hints.
    
- PNG structure is robust — headers like `IHDR`, `IDAT`, and `IEND` help diagnose.
    
- Fixing corrupt headers manually can fully restore access to the content.