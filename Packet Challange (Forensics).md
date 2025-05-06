## 🧠 Challenge Prompt

We were given a `.pcapng` file capturing **loopback SMTP traffic**. The challenge hinted that someone named _Jarbou3_ was missing parts of a flag, and that we needed to reconstruct the full flag by analyzing the intercepted emails.

The expected format was:

```
KYBS{...}
```

---

## 🔓 Solution

### Step 1: Analyze SMTP Traffic

Opening the `.pcapng` in **Wireshark**, we noticed several SMTP sessions between a fake domain (`kyubisec.com`) and local users (`decode1`, `decode2`, `decode3`). Each session contained a portion of the flag.

We filtered with:

```
tcp contains "decode"
```

Then right-clicked → Follow TCP Stream to read each message.

---

### Step 2: Extract FLAG PART 1

From an email to `decode1@kyubisec.com`:

```
Oh shiit Jarbou3 is misssing a part of the flag:

NU1UUA==
```

Decoding `NU1UUA==` (Base64) gave:

```
5MTP
```

Interpreted semantically as:

```
SMTP
```

---

### Step 3: Extract FLAG PART 2

From `decode2@kyubisec.com`, we found a base64-encoded attachment. Decoding it yielded:

```
Oh shiit Jarbou3 is missing a part of the flag:
31735f4e30545f7333
```

Decoding the hex `31735f4e30545f7333` gave:

```
1s_N0T_s3
```

---

### Step 4: Extract FLAG PART 3

From `decode3@kyubisec.com`:

```
('Part 3', 'ecur3_hhhhh')
```

Final fragment:

```
ecur3_hhhhh
```

---

## 🛡️ Final Flag Assembly

Putting everything together:

```
SMTP + 1s_N0T_s3 + ecur3_hhhhh
```

Final flag:

```
KYBS{SMTP1s_N0T_s3cur3_hhhhh}
```

> Note: An initial decoding error gave `5MUP` instead of `5MTP`, but this was corrected upon semantic inspection.

---

## 💡 Notes

- This challenge demonstrates how **unsecured SMTP** can be used to exfiltrate sensitive data.
    
- All traffic was over loopback, suggesting a local misconfiguration or intentional leak.
    
- A humorous nod to classic infosec advice: _"SMTP is not secure."_