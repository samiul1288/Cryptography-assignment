# Number Theory and Cryptography Workshop
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1hFRc_HlBeZMC_K1w3MxtUyMAc9Nk854K)
**Author:** Md. Samiul Haque Ruf
**ID:** IT-23011
**File:** `Number_Theory_and_Cryptography_Workshop.ipynb` (Python 3, Jupyter / Google Colab)

A hands-on notebook that implements the core algorithms of elementary number theory, the building blocks behind modern cryptography (RSA, Diffie-Hellman, primality testing).

## Topics Covered

1. Divisibility and Modular Arithmetic
2. GCD and Euclid's Algorithm
3. Extended Euclid
4. LCM
5. Diophantine Equations
6. Modular Inverse and Division
7. Fast Modular Exponentiation
8. Chinese Remainder Theorem
9. Fermat's Little Theorem and Euler's Theorem
10. Primes and the Sieve of Eratosthenes
11. Primality Tests (Fermat test, pseudoprimes, Carmichael numbers)
12. Mersenne Primes, Lucas-Lehmer test, Perfect Numbers

## Requirements

- Python 3.8+
- Standard library only (`math`, `random`)
- Jupyter Notebook, JupyterLab, or Google Colab

## How to Run

```bash
jupyter notebook Number_Theory_and_Cryptography_Workshop.ipynb
```

Or upload the file to [Google Colab](https://colab.research.google.com) and choose **Runtime → Run all**.

## Algorithms at a Glance

Variable names below are simplified for readability.

### GCD (Euclid)
```python
def gcd(x, y):
    return gcd(y, x % y) if y > 0 else x
```

### Extended Euclid
Returns `(g, s, t)` with `g = gcd(m, n) = m*s + n*t`.
```python
def extended_gcd(m, n):
    if n == 0:
        return m, 1, 0
    g, s, t = extended_gcd(n, m % n)
    return g, t, s - (m // n) * t
```
Example: `extended_gcd(391, 299)` → `(23, -3, 4)`.

### LCM
```python
def lcm(x, y):
    return x // gcd(max(x, y), min(x, y)) * y
```

### Linear Diophantine Equation `p·x + q·y = r`
Solvable only when `gcd(p, q)` divides `r`.
```python
def diophantine(p, q, r):
    if p >= q:
        g, u0, v0 = extended_gcd(p, q)
    else:
        g, v0, u0 = extended_gcd(q, p)
    assert r % g == 0, "no integer solution"
    k = r // g
    return u0 * k, v0 * k
```
Example: `diophantine(34, 51, 170)` → `(-10, 10)`.

### Modular Inverse and Division
```python
def modInverse(val, mod):
    val %= mod
    g, _, inv = extended_gcd(mod, val)
    assert g == 1, "inverse does not exist"
    return inv % mod

def divide(num, den, mod):
    return (modInverse(den, mod) * num) % mod
```
Example: `divide(7, 2, 9)` → `8`.

### Fast Modular Exponentiation
Repeated squaring, O(log exp) multiplications.
```python
def FastModularExponentiation(base, exp, mod):
    acc, cur = 1 % mod, base % mod
    while exp > 0:
        if exp & 1:
            acc = (acc * cur) % mod
        cur = (cur * cur) % mod
        exp >>= 1
    return acc
```
Example: `7^13 mod 11` → `2`.

### Chinese Remainder Theorem
Solves `n ≡ r1 (mod m1)` and `n ≡ r2 (mod m2)` for coprime `m1, m2`.
```python
def ChineseRemainderTheorem(m1, r1, m2, r2):
    if m1 >= m2:
        g, s, t = extended_gcd(m1, m2)
    else:
        g, t, s = extended_gcd(m2, m1)
    assert g == 1
    return (r1 * m2 * t + r2 * m1 * s) % (m1 * m2)
```
Example: `ChineseRemainderTheorem(13, 7, 6, 5)` → `59`.

### Euler's Totient
```python
def phi(n):
    return sum(1 for k in range(n) if math.gcd(k, n) == 1) if n > 1 else 1
```

### Sieve of Eratosthenes
```python
def sieve(limit):
    flags = [True] * (limit + 1)
    flags[0] = flags[1] = False
    for i in range(2, int(limit ** 0.5) + 1):
        if flags[i]:
            for j in range(i * i, limit + 1, i):
                flags[j] = False
    return [i for i, ok in enumerate(flags) if ok]
```

### Fermat Primality Test and Carmichael Numbers
```python
def fermat_test(n, base):
    return pow(base, n - 1, n) == 1

def prime_factors(n):
    factors, d = [], 2
    while d * d <= n:
        while n % d == 0:
            factors.append(d)
            n //= d
        d += 1
    if n > 1:
        factors.append(n)
    return factors

def is_carmichael(n):
    fs = prime_factors(n)
    return len(fs) > 1 and len(set(fs)) == len(fs) and all((n - 1) % (p - 1) == 0 for p in fs)
```
Carmichael numbers below 3000: `561, 1105, 1729, 2465, 2821`. `341` is a base-2 pseudoprime.

### Lucas-Lehmer Test (Mersenne Primes)
```python
def lucas_lehmer(p):
    if p == 2:
        return True
    mersenne, term = 2 ** p - 1, 4
    for _ in range(p - 2):
        term = (term * term - 2) % mersenne
    return term == 0
```
Mersenne exponents up to 130: `2, 3, 5, 7, 13, 17, 19, 31, 61, 89, 107, 127`.
Perfect numbers from them: `6, 28, 496, 8128`.

## Key Results Verified in the Notebook

| Check | Result |
|---|---|
| Last two digits of 99^99 | 99 |
| `gcd(1849639579327, 790933790547)` | 3416723 |
| `lcm(24, 16)` | 48 |
| `phi(55)` | 40 |
| Fermat and Euler theorems | Verified for small primes and moduli |
| Primes below 100,000 | 9592 (estimate x/ln x ≈ 8686) |
| Custom fast exponentiation vs built-in `pow` | Matches on 500 random tests |

## Notes

- `extended_gcd` and `gcd` assume `a >= b >= 0`; the helper functions order their arguments accordingly.
- `diophantine`, `modInverse` and `ChineseRemainderTheorem` raise an `AssertionError` when no solution exists.
- `phi(n)` is brute force (O(n log n)); it is meant for demonstration, not large inputs.
