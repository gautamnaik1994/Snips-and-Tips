# Numbers

Here’s a **complete list of numeric data types** (both **signed and unsigned**) with their **bit sizes, memory usage**, and **value ranges**.\


***

#### 🧮 1️⃣ Integer Types

| Type                                           | Signed?  | Bits | Bytes | Minimum Value              | Maximum Value                          |
| ---------------------------------------------- | -------- | ---- | ----- | -------------------------- | -------------------------------------- |
| **4-bit nibble**                               | Unsigned | 4    | 0.5   | 0                          | 15                                     |
| **4-bit nibble**                               | Signed   | 4    | 0.5   | -8                         | +7                                     |
| **8-bit (byte / int8 / char)**                 | Unsigned | 8    | 1     | 0                          | 255                                    |
| **8-bit (byte / int8 / char)**                 | Signed   | 8    | 1     | -128                       | +127                                   |
| **16-bit (short / int16 / smallint)**          | Unsigned | 16   | 2     | 0                          | 65,535                                 |
| **16-bit (short / int16 / smallint)**          | Signed   | 16   | 2     | -32,768                    | +32,767                                |
| **32-bit (int / int32 / long in C)**           | Unsigned | 32   | 4     | 0                          | 4,294,967,295                          |
| **32-bit (int / int32)**                       | Signed   | 32   | 4     | -2,147,483,648             | +2,147,483,647                         |
| **64-bit (bigint / long / int64)**             | Unsigned | 64   | 8     | 0                          | 18,446,744,073,709,551,615 (≈1.8×10¹⁹) |
| **64-bit (bigint / int64)**                    | Signed   | 64   | 8     | -9,223,372,036,854,775,808 | +9,223,372,036,854,775,807             |
| **128-bit (int128, not standard in C/Python)** | Unsigned | 128  | 16    | 0                          | 3.4×10³⁸                               |
| **128-bit (int128)**                           | Signed   | 128  | 16    | -1.7×10³⁸                  | +1.7×10³⁸                              |

***

#### 🔢 2️⃣ Floating-Point Types (IEEE 754 Standard)

| Type                           | Bits | Bytes | Approx. Range | Precision (significant digits) |
| ------------------------------ | ---- | ----- | ------------- | ------------------------------ |
| **Half precision (float16)**   | 16   | 2     | ±6.10×10⁴     | \~3 decimal digits             |
| **Single precision (float32)** | 32   | 4     | ±3.40×10³⁸    | \~7 decimal digits             |
| **Double precision (float64)** | 64   | 8     | ±1.80×10³⁰⁸   | \~15–16 decimal digits         |
| **Quad precision (float128)**  | 128  | 16    | ±1.20×10⁴⁹³²  | \~33–34 decimal digits         |

***

#### 🧠 3️⃣ Decimal / Arbitrary Precision (in databases or languages like Python)

| Type                  | Description                            | Bits      | Range                       | Memory             |
| --------------------- | -------------------------------------- | --------- | --------------------------- | ------------------ |
| **Decimal(p, s)**     | Fixed precision decimal (e.g., in SQL) | Variable  | Depends on precision `p`    | Usually 1–17 bytes |
| **Python `int`**      | Arbitrary precision                    | Unlimited | Limited by available memory | Dynamic allocation |
| **BigDecimal (Java)** | Arbitrary precision                    | Variable  | Depends on precision        | Dynamic allocation |

***

#### ⚙️ 4️⃣ Other Variants

| Type                      | Common Usage                                | Bits                         | Bytes | Range                                               |
| ------------------------- | ------------------------------------------- | ---------------------------- | ----- | --------------------------------------------------- |
| **bool / boolean**        | True/False values                           | 1 (usually stored as 8 bits) | 1     | 0 or 1                                              |
| **bfloat16**              | ML applications (reduced precision float16) | 16                           | 2     | Similar range as float32 but \~2–3 digits precision |
| **float8 (experimental)** | ML accelerators (e.g., NVIDIA Hopper)       | 8                            | 1     | Range varies; \~1–2 digits precision                |

***

#### 🧩 Memory Summary

| Bits | Bytes | Example Type(s)   |
| ---- | ----- | ----------------- |
| 1    | 0.125 | bool              |
| 4    | 0.5   | nibble            |
| 8    | 1     | byte, uint8, int8 |
| 16   | 2     | short, float16    |
| 32   | 4     | int32, float32    |
| 64   | 8     | int64, double     |
| 128  | 16    | int128, float128  |

***

## 🔢 Numeric Data Types — Ranges, Sizes, and Memory

Below is a complete reference for **integer**, **floating-point**, and **special numeric types**, including their **bit width**, **memory usage**, and **value ranges**.

***

### 🧮 Integer Types

| Type                                  | Signed?  | Bits | Bytes | Minimum Value              | Maximum Value              |
| ------------------------------------- | -------- | ---- | ----- | -------------------------- | -------------------------- |
| **4-bit nibble**                      | Unsigned | 4    | 0.5   | 0                          | 15                         |
| **4-bit nibble**                      | Signed   | 4    | 0.5   | −8                         | +7                         |
| **8-bit (byte / int8)**               | Unsigned | 8    | 1     | 0                          | 255                        |
| **8-bit (byte / int8)**               | Signed   | 8    | 1     | −128                       | +127                       |
| **16-bit (short / int16 / smallint)** | Unsigned | 16   | 2     | 0                          | 65,535                     |
| **16-bit (short / int16 / smallint)** | Signed   | 16   | 2     | −32,768                    | +32,767                    |
| **32-bit (int / int32)**              | Unsigned | 32   | 4     | 0                          | 4,294,967,295              |
| **32-bit (int / int32)**              | Signed   | 32   | 4     | −2,147,483,648             | +2,147,483,647             |
| **64-bit (bigint / int64)**           | Unsigned | 64   | 8     | 0                          | 18,446,744,073,709,551,615 |
| **64-bit (bigint / int64)**           | Signed   | 64   | 8     | −9,223,372,036,854,775,808 | +9,223,372,036,854,775,807 |
| **128-bit (int128)**                  | Unsigned | 128  | 16    | 0                          | ≈3.4×10³⁸                  |
| **128-bit (int128)**                  | Signed   | 128  | 16    | ≈−1.7×10³⁸                 | ≈+1.7×10³⁸                 |

***

### 🌊 Floating-Point Types (IEEE 754)

| Type                           | Bits | Bytes | Approx. Range | Precision (Significant Digits) |
| ------------------------------ | ---- | ----- | ------------- | ------------------------------ |
| **Half precision (float16)**   | 16   | 2     | ±6.10×10⁴     | \~3 digits                     |
| **Single precision (float32)** | 32   | 4     | ±3.40×10³⁸    | \~7 digits                     |
| **Double precision (float64)** | 64   | 8     | ±1.80×10³⁰⁸   | \~15–16 digits                 |
| **Quad precision (float128)**  | 128  | 16    | ±1.20×10⁴⁹³²  | \~33–34 digits                 |

***

### 💰 Decimal & Arbitrary Precision Types

| Type                | Description                           | Bits     | Range                    | Memory               |
| ------------------- | ------------------------------------- | -------- | ------------------------ | -------------------- |
| **Decimal(p, s)**   | Fixed precision decimal (used in SQL) | Variable | Depends on precision `p` | 1–17 bytes (typical) |
| **Python `int`**    | Arbitrary-precision integer           | Dynamic  | Limited by memory        | Grows dynamically    |
| **Java BigDecimal** | Arbitrary precision decimal           | Dynamic  | Depends on precision     | Grows dynamically    |

***

### ⚙️ Other Variants & Special Types

| Type                      | Usage                             | Bits                            | Bytes | Range                                         |
| ------------------------- | --------------------------------- | ------------------------------- | ----- | --------------------------------------------- |
| **bool / boolean**        | True/False                        | 1 (bit, often stored as 1 byte) | 1     | 0 or 1                                        |
| **bfloat16**              | ML accelerators (TPUs, GPUs)      | 16                              | 2     | Same range as float32, \~2–3 digits precision |
| **float8 (experimental)** | AI hardware (e.g., NVIDIA Hopper) | 8                               | 1     | Range and precision vary (\~1–2 digits)       |

***

### 🧠 Memory Summary

| Bits | Bytes | Common Type(s)   |
| ---- | ----- | ---------------- |
| 1    | 0.125 | bool             |
| 4    | 0.5   | nibble           |
| 8    | 1     | byte, int8       |
| 16   | 2     | short, float16   |
| 32   | 4     | int32, float32   |
| 64   | 8     | int64, double    |
| 128  | 16    | int128, float128 |

***

### 💡 Notes

* **Signed vs Unsigned:**\
  Signed types reserve one bit for sign representation (two’s complement).\
  Unsigned types use the full bit range for positive values.
* **Floating-point precision:**\
  Higher bit width → more accurate representation of decimals, not necessarily a wider range.
* **Arbitrary-precision numbers:**\
  Used when fixed bit-width numbers can overflow (e.g., cryptography, high-precision finance).
