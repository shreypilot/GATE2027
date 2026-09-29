# Computer Organization & Architecture (COA)
## Lecture 04: Floating Point Representation (IEEE 754)

**Source:** GfG GATE CS Crash Course (Vijay Sir) — COA Lecture 4: Floating Point Representation

---

## 📌 Topic Priority Matrix (GATE CSE)

| Priority | Topic | Key Focus Area |
| :--- | :--- | :--- |
| ★★★★★ | **IEEE 754 Single & Double Format** | $1+8+23=32$ bits (Bias 127), $1+11+52=64$ bits (Bias 1023) |
| ★★★★★ | **Stored Exponent $E$ vs True Exponent $e$** | $E = e + \text{Bias}$, $e = E - \text{Bias}$ |
| ★★★★★ | **Implicit vs Explicit Mantissa** | Hidden bit ($1.M$) vs Explicit ($0.M$); precision gain |
| ★★★★★ | **Bias Formula & Derivation** | $\text{Bias} = 2^{k-1}-1$ (leaving $E=255$ for Inf/NaN) |
| ★★★★☆ | **Special $E$ & $M$ Values** | Zero ($E=0, M=0$), Subnormal ($E=0, M\neq 0$), Inf ($E=255, M=0$), NaN ($E=255, M\neq 0$) |
| ★★★★☆ | **Range & Precision Math** | Smallest/Largest normalized numbers, smallest subnormal |
| ★★★☆☆ | **Decimal to IEEE 754 Conversion** | Step-by-step encoding & decoding numericals |

---

## 1. What a Floating-Point Representation Means

A floating-point representation expresses numbers in scientific notation to cover a wide dynamic range with a fixed word length:

```text
+----------+-----------------------+----------------------------------+
| Sign (S) | Stored Exponent (E)   | Mantissa / Fraction Field (M)    |
+----------+-----------------------+----------------------------------+
  1 bit          k bits                        m bits
```

$$\text{Value} = (-1)^{S} \times (\text{Significand}) \times 2^{e}$$

* **Sign Bit ($S$):** $0 \implies \text{Positive}$, $1 \implies \text{Negative}$.
* **Mantissa / Fraction ($M$):** Determines the **precision** of the representation. More bits in $M \implies$ higher accuracy and finer resolution.
* **Exponent ($e$):** Determines the **dynamic range** (scale) of the numbers that can be represented.

> **Trade-off:** For a fixed word length $N = 1 + k + m$:
> * Increasing $m$ increases **precision** but decreases range.
> * Increasing $k$ increases **range** but decreases precision.

---

## 2. Normalization: Explicit vs. Implicit (IEEE 754)

### 2.1 Explicit Normalization (Non-IEEE / Custom Formats)
The mantissa is stored as a fractional binary number with an explicit leading zero or bit:
$$\text{Significand} = 0.M \quad \implies \quad \text{Value} = (-1)^{S} \times (0.M)_2 \times 2^{e}$$
* Example: $29.75_{10} = 11101.11_2 = 0.1110111_2 \times 2^5$.

### 2.2 Implicit Normalization (IEEE 754 Standard — Hidden Bit)
In normalized IEEE 754 numbers, the binary significand is normalized to the form $1.M$ where $1 \le \text{significand} < 2$. Since the leading bit before the binary point is **always 1**, it is **NOT stored in memory**.
$$\text{Significand} = 1.M \quad \implies \quad \text{Value} = (-1)^{S} \times (1.M)_2 \times 2^{E - \text{Bias}}$$
* **Hidden Bit Advantage:** Implicit normalization provides **1 extra bit of precision** for free! A 23-bit fraction field effectively delivers **24 bits of precision**.

---

## 3. Biased (Excess) Exponent Representation

### 3.1 Why Use Biased Exponents?
The actual exponent $e$ can be positive or negative. Storing $e$ directly as a 2's complement number makes floating-point comparisons complex (requires sign bit checking).  
By adding a fixed **Bias** to $e$, the stored exponent $E = e + \text{Bias}$ becomes an **unsigned integer** ($E \ge 0$). This allows direct hardware magnitude comparisons using simple integer comparators.

### 3.2 Bias Formula Derivation
For a $k$-bit exponent field:
$$\mathbf{\text{Bias} = 2^{k-1} - 1}$$

| Format | Total Bits | $k$ (Exponent Bits) | Bias ($2^{k-1}-1$) | Fraction Bits ($m$) | Implicit Precision |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **IEEE 754 Single Precision** | 32 bits | 8 bits | **127** ($2^7 - 1$) | 23 bits | 24 bits |
| **IEEE 754 Double Precision** | 64 bits | 11 bits | **1023** ($2^{10} - 1$) | 52 bits | 53 bits |

#### Why $\text{Bias} = 127$ instead of $128$ for $k=8$?
* $k=8$ bits gives unsigned range $0 \dots 255$.
* In IEEE 754, $E=0$ and $E=255$ are **reserved** for special values.
* Usable $E$ range for normalized numbers $= 1 \dots 254$.
* If $\text{Bias}$ were $128$, the maximum exponent $e_{\max} = +127$ would require $E = 127 + 128 = 255$, which collides with the reserved pattern!
* With $\text{Bias} = 127$, $e_{\max} = +127 \implies E = 127 + 127 = 254$, leaving $E = 255$ free for Infinity and NaN.

---

## 4. IEEE 754 Single Precision: Special Exponent Values

```
           +-------------------------------------------------------------+
           |                   IEEE 754 Single Precision                 |
           +-------------------------------------------------------------+
           |  Stored E = 0        |  1 <= E <= 254       |  Stored E = 255 |
           +----------------------+----------------------+-----------------+
           |                      |                      |                 |
     +-----+-----+          +-----+-----+          +-----+-----+
     | M = 0     | M != 0   |  Normalized|         | M = 0     | M != 0
     v           v          |  Hidden 1  |         v           v
   +-0 / -0   Subnormal     +------------+      +Inf / -Inf   NaN
```

| Stored $E$ (8 bits) | Fraction $M$ (23 bits) | Classification | Value Formula / Interpretation |
| :---: | :---: | :--- | :--- |
| `00000000` ($0$) | `000...000` ($0$) | **Signed Zero ($\pm 0$)** | $(-1)^S \times 0.0$ |
| `00000000` ($0$) | Non-zero ($M \neq 0$) | **Subnormal / Denormal** | $(-1)^S \times (0.M)_2 \times 2^{1 - 127} = (-1)^S \times (0.M)_2 \times 2^{-126}$ |
| `00000001` to `11111110` ($1 \dots 254$) | Any | **Normalized Real** | $(-1)^S \times (1.M)_2 \times 2^{E - 127}$ |
| `11111111` ($255$) | `000...000` ($0$) | **Infinity ($\pm \infty$)** | Overflow / Division by zero |
| `11111111` ($255$) | Non-zero ($M \neq 0$) | **NaN (Not a Number)** | Invalid operations (e.g. $0/0$, $\sqrt{-1}$) |

---

## 5. Range & Precision Math (Single Precision Summary)

1. **Smallest Positive Normalized Number:**
   $$E = 1 \implies e = 1 - 127 = -126, \quad M = 0 \implies \mathbf{1.0_2 \times 2^{-126} \approx 1.175 \times 10^{-38}}$$
2. **Largest Positive Normalized Number:**
   $$E = 254 \implies e = 254 - 127 = +127, \quad M = \text{all 1s} \implies \mathbf{(2 - 2^{-23}) \times 2^{127} \approx 3.402 \times 10^{38}}$$
3. **Smallest Positive Subnormal Number:**
   $$E = 0, \quad M = 000\dots01 \implies 0.000\dots01_2 \times 2^{-126} = \mathbf{2^{-23} \times 2^{-126} = 2^{-149} \approx 1.4 \times 10^{-45}}$$
4. **Machine Epsilon ($\epsilon$):**
   Difference between $1.0$ and the next larger representable floating-point number $= \mathbf{2^{-23} \approx 1.19 \times 10^{-7}}$.

---

## 6. Step-by-Step Numerical Examples

### Example 1: Convert Decimal $-29.75_{10}$ to IEEE 754 Single Precision (32-bit)

1. **Sign:** Number is negative $\implies \mathbf{S = 1}$.
2. **Binary Conversion:**
   $$29_{10} = 11101_2, \quad 0.75_{10} = 0.11_2 \implies 29.75_{10} = 11101.11_2$$
3. **Normalize (Implicit 1.M):**
   $$11101.11_2 = 1.110111_2 \times 2^{4} \implies \text{True exponent } e = 4, \quad M = 110111_2$$
4. **Calculate Stored Exponent $E$:**
   $$E = e + \text{Bias} = 4 + 127 = 131_{10} = 10000011_2$$
5. **Pack Fraction $M$ (Pad to 23 bits):**
   $$M = 11011100000000000000000_2$$
6. **Final 32-bit Hex Representation:**
   * Binary: `1 | 10000011 | 11011100000000000000000`
   * Group 4 bits: `1100 0001 1110 1100 0000 0000 0000 0000`
   * Hexadecimal: $\mathbf{\text{0xC1EC0000}}$

---

### Example 2: Decode IEEE 754 Hex `0x41440000` to Decimal

1. **Convert to Binary:**
   `0x41440000` = `0100 0001 0100 0100 0000 0000 0000 0000`
2. **Field Extraction:**
   * $S = 0$ (Positive)
   * $E = 10000010_2 = 130_{10}$
   * $M = 10001000000000000000000_2 = .10001_2$
3. **Calculate True Exponent $e$:**
   $$e = E - 127 = 130 - 127 = 3$$
4. **Reconstruct Value:**
   $$\text{Value} = +1.10001_2 \times 2^3 = 1100.01_2 = 12 + 0.25 = \mathbf{12.25_{10}}$$

---

## 7. GATE Exam Formula & Trap Summary

1. **Single Precision:** $1 + 8 + 23 = 32\text{ bits}, \text{Bias} = 127$.
2. **Double Precision:** $1 + 11 + 52 = 64\text{ bits}, \text{Bias} = 1023$.
3. **Bias Formula:** $\text{Bias} = 2^{k-1} - 1$.
4. **Exponent Traps:**
   * $E = 0 \implies$ Denormalized / Subnormal ($e = 1 - \text{Bias} = -126$).
   * $E = 255 \implies \text{Inf}$ (if $M=0$) or $\text{NaN}$ (if $M \neq 0$).
5. **Mantissa Trap:** IEEE 754 mantissa uses **sign-magnitude**, NEVER 2's complement!
6. **Implicit Bit Trap:** Remember to add back the implicit leading $1$ when evaluating normalized numbers ($1.M$).
