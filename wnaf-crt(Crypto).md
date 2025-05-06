
## 🧠 Challenge Prompt

We were given a Python script that asks the user to input a hexadecimal number `X` and checks it against a chain of mathematical constraints involving the Chinese Remainder Theorem (CRT), bitwise operations, windowed non-adjacent form (WNAF), and a CRC32 checksum.

The final output had to match the format:

```
KYBS{X}
```

---

## 🔓 Solution

### Step 1: Solving the CRT

We were given two congruences:

```
A ≡ 0xDEADBEEF mod 0x1337C0DE  
A ≡ 0xDEADBEEF + 2*42 mod 0xDEADFACE
```

This was solved using the **Chinese Remainder Theorem** (CRT). The values were coprime, so we computed the modular inverse and reconstructed the unique solution modulo lcm(m1, m2).

---

### Step 2: Compute B with Bit Tricks

Using the obtained `A`, we calculated:

```
B = (A XOR 0xFEEDFACE) + (A << 5) - (A >> 3)
```

We enforced a 32-bit wrap using `& 0xFFFFFFFF`.

---

### Step 3: WNAF Representation

We computed the **4-Window Non-Adjacent Form (4-NAF)** of `B`, and counted the **sum of absolute values of non-zero digits**. This total was assigned to `C`.

---

### Step 4: Compute X

The final value `X` was calculated as:

```
X = ((C * 0xDEAD) XOR 0xBEEF) % 2^32
```

A final tweak was applied using:

```
X ^= CRC32("By_bz7") % 0x1337
```

We used Python’s `zlib.crc32` to compute the checksum.

---

## 🔪 Final Value

The final value of `X` that satisfied all conditions was:

```
00162CCF
```

---

## 🎉 Flag

```
KYBS{00162CCF}
```

---

## 💡 Notes

- WNAF is usually used in cryptographic optimizations like scalar multiplication on elliptic curves, but here it was cleverly used as a constraint mechanism.
    
- CRT allowed reconstructing `A` even with a slight difference in remainders.
    
- This challenge combines modular arithmetic, bit manipulation, and uncommon number representations—ideal for deep logic and math lovers.