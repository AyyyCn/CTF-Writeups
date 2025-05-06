## 📷 Challenge Prompt

You are given **147 tiny maze images** (`maze_001.png` → `maze_147.png`). Each image hides one coloured pixel among grey walls and paths.

- **R value** of that pixel ⇒ index of the next image to open (`0` means **end**)
    
- **G value** ⇒ ASCII code of **one flag character**
    
- **B value** ⇒ checksum (irrelevant here)
    

Starting at `maze_001.png`, you must follow this breadcrumb trail until `R = 0`, collecting `G` at each step to reveal the complete flag.

---

## Solution Script

```python
import sys
from pathlib import Path
from PIL import Image

DIR = Path(__file__).parent  # folder containing script + images

def find_coloured_pixel(img):
    """
    Return (r, g, b) of the pixel that is not greyscale (i.e., r ≠ g ≠ b).
    This pixel contains navigation info and flag data.
    """
    px = img.load()
    for y in range(img.height):
        for x in range(img.width):
            r, g, b = px[x, y][:3]
            if not (r == g == b):
                return r, g, b
    raise ValueError("No coloured pixel found!")

def img_path(index: int) -> Path:
    return DIR / f"maze_{index:03d}.png"

def main():
    current = 1
    visited = set()
    flag_chars = []

    while current and current not in visited:
        visited.add(current)
        path = img_path(current)

        if not path.exists():
            sys.exit(f"[ERROR] File not found: {path.name}")

        with Image.open(path).convert("RGB") as img:
            r, g, _ = find_coloured_pixel(img)
            flag_chars.append(chr(g))  # G = character in flag
            current = r  # R = next image index (0 = end)

        if current and not (1 <= current <= 147):
            sys.exit(f"[WARNING] R value out of range: {current}")

    print("Flag reconstructed:", "".join(flag_chars))

if __name__ == "__main__":
    main()
```

---

### Key Points

|Step|Purpose|
|---|---|
|1|Locate the unique coloured (non-greyscale) pixel|
|2|Extract `R` and `G` values|
|3|Traverse the image chain recursively|

---

## Flag

```
KYBS{M4444zzzz333_N4v11g4t00r_L00uH@@y333333333dd}
```

---

## Notes / Lessons

- Greyscale detection is a simple and reliable stego hint: `r == g == b` helps filter noise.
    
- Maze-based traversal mimics pointer-based memory reading in exploitation contexts.
    
- Avoiding cycles and validating `R` bounds prevents infinite loops or corrupt chains.
    
- Pillow makes pixel-level image processing straightforward and scriptable.
    
- A checksum channel (`B`) can help validate or confuse—but is often ignorable in CTFs.