# Problem Solving on Loops

## Overview

Loops are the engine of programming — they let you process data, search for answers, and automate repetitive tasks. This tutorial covers fundamental algorithmic problems that specifically exercise your loop skills. Mastering these problems will prepare you for more complex challenges in data structures, algorithms, and real-world backend development.

---

## Problem-Solving Strategy

Before writing any loop, ask yourself:

1. **What am I iterating over?** — A range of numbers? An array? Until a condition is met?
2. **What do I need to track?** — A running sum? A maximum value? A counter?
3. **When do I stop?** — Fixed count? Specific condition? End of data?
4. **What do I return?** — A single value? A collection? A modified structure?

---

## Problem 1: Sum of First N Natural Numbers

**Problem:** Calculate the sum of numbers from 1 to N.

```javascript
function sumToN(n) {
  let sum = 0;
  for (let i = 1; i <= n; i++) {
    sum += i;
  }
  return sum;
}

console.log(sumToN(5));  // 15 (1+2+3+4+5)
console.log(sumToN(10)); // 55
```

**Mathematical shortcut:** `n * (n + 1) / 2`

```javascript
function sumToNFast(n) {
  return (n * (n + 1)) / 2;
}
```

---

## Problem 2: Factorial of a Number

**Problem:** Calculate n! = n × (n-1) × (n-2) × ... × 1

```javascript
function factorial(n) {
  if (n < 0) return undefined;
  if (n === 0 || n === 1) return 1;

  let result = 1;
  for (let i = 2; i <= n; i++) {
    result *= i;
  }
  return result;
}

console.log(factorial(5));  // 120
console.log(factorial(0));  // 1
console.log(factorial(10)); // 3628800
```

---

## Problem 3: Check if a Number is Prime

**Problem:** A prime number is only divisible by 1 and itself.

```javascript
function isPrime(n) {
  if (n <= 1) return false;
  if (n <= 3) return true;
  if (n % 2 === 0 || n % 3 === 0) return false;

  // Check up to square root only
  for (let i = 5; i * i <= n; i += 6) {
    if (n % i === 0 || n % (i + 2) === 0) {
      return false;
    }
  }
  return true;
}

console.log(isPrime(2));   // true
console.log(isPrime(17));  // true
console.log(isPrime(18));  // false
console.log(isPrime(97));  // true
```

**Optimization:** Only check up to √n. If n has a factor larger than √n, the corresponding pair factor must be smaller than √n.

---

## Problem 4: Print All Prime Numbers in a Range

```javascript
function primesInRange(start, end) {
  const primes = [];

  for (let num = start; num <= end; num++) {
    if (isPrime(num)) {
      primes.push(num);
    }
  }

  return primes;
}

console.log(primesInRange(1, 50));
// [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47]
```

---

## Problem 5: Fibonacci Series

**Problem:** Generate the first N Fibonacci numbers: 0, 1, 1, 2, 3, 5, 8, 13...

```javascript
function fibonacci(n) {
  if (n <= 0) return [];
  if (n === 1) return [0];

  const series = [0, 1];

  for (let i = 2; i < n; i++) {
    series.push(series[i - 1] + series[i - 2]);
  }

  return series;
}

console.log(fibonacci(10));
// [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

### Finding the Nth Fibonacci Number

```javascript
function nthFibonacci(n) {
  if (n <= 0) return 0;
  if (n === 1) return 1;

  let prev = 0;
  let curr = 1;

  for (let i = 2; i <= n; i++) {
    const next = prev + curr;
    prev = curr;
    curr = next;
  }

  return curr;
}

console.log(nthFibonacci(10)); // 55
```

---

## Problem 6: Reverse a Number

**Problem:** Reverse the digits of a number.

```javascript
function reverseNumber(num) {
  let reversed = 0;
  let n = Math.abs(num);

  while (n > 0) {
    const digit = n % 10;
    reversed = reversed * 10 + digit;
    n = Math.floor(n / 10);
  }

  return num < 0 ? -reversed : reversed;
}

console.log(reverseNumber(12345));  // 54321
console.log(reverseNumber(-987));   // -789
console.log(reverseNumber(1200));   // 21
```

---

## Problem 7: Check if a Number is Palindrome

**Problem:** A palindrome reads the same forwards and backwards.

```javascript
function isPalindromeNumber(num) {
  if (num < 0) return false;

  let reversed = 0;
  let original = num;

  while (num > 0) {
    reversed = reversed * 10 + (num % 10);
    num = Math.floor(num / 10);
  }

  return original === reversed;
}

console.log(isPalindromeNumber(121));   // true
console.log(isPalindromeNumber(123));   // false
console.log(isPalindromeNumber(12321)); // true
```

---

## Problem 8: Count Digits in a Number

```javascript
function countDigits(num) {
  if (num === 0) return 1;

  let count = 0;
  n = Math.abs(num);

  while (n > 0) {
    count++;
    n = Math.floor(n / 10);
  }

  return count;
}

console.log(countDigits(12345));  // 5
console.log(countDigits(0));      // 1
console.log(countDigits(-999));   // 3
```

---

## Problem 9: Armstrong (Narcissistic) Number

**Problem:** A number equal to the sum of its digits raised to the power of the number of digits.

```javascript
function isArmstrong(num) {
  const digits = String(num).split("").map(Number);
  const power = digits.length;

  let sum = 0;
  for (const digit of digits) {
    sum += Math.pow(digit, power);
  }

  return sum === num;
}

console.log(isArmstrong(153));  // true (1³ + 5³ + 3³ = 1 + 125 + 27 = 153)
console.log(isArmstrong(9474)); // true
console.log(isArmstrong(123));  // false
```

---

## Problem 10: GCD (Greatest Common Divisor)

**Problem:** Find the largest number that divides both numbers.

```javascript
function gcd(a, b) {
  while (b !== 0) {
    const temp = b;
    b = a % b;
    a = temp;
  }
  return a;
}

console.log(gcd(48, 18)); // 6
console.log(gcd(56, 98)); // 14
```

### LCM (Least Common Multiple)

```javascript
function lcm(a, b) {
  return (a * b) / gcd(a, b);
}

console.log(lcm(4, 6)); // 12
```

---

## Problem 11: Sum of Digits

```javascript
function sumOfDigits(num) {
  let sum = 0;
  let n = Math.abs(num);

  while (n > 0) {
    sum += n % 10;
    n = Math.floor(n / 10);
  }

  return sum;
}

console.log(sumOfDigits(12345)); // 15
console.log(sumOfDigits(999));   // 27
```

---

## Problem 12: Binary Representation

```javascript
function toBinary(num) {
  if (num === 0) return "0";

  let binary = "";
  let n = num;

  while (n > 0) {
    binary = (n % 2) + binary;
    n = Math.floor(n / 2);
  }

  return binary;
}

console.log(toBinary(10)); // "1010"
console.log(toBinary(255)); // "11111111"
```

---

## Problem 13: Perfect Number

**Problem:** A number equal to the sum of its proper divisors (excluding itself).

```javascript
function isPerfect(num) {
  if (num <= 1) return false;

  let sum = 1; // 1 is always a divisor

  for (let i = 2; i * i <= num; i++) {
    if (num % i === 0) {
      sum += i;
      if (i !== num / i) {
        sum += num / i;
      }
    }
  }

  return sum === num;
}

console.log(isPerfect(6));   // true (1 + 2 + 3 = 6)
console.log(isPerfect(28));  // true (1 + 2 + 4 + 7 + 14 = 28)
console.log(isPerfect(12));  // false
```

---

## Problem 14: Power Calculation

```javascript
function power(base, exponent) {
  if (exponent === 0) return 1;
  if (exponent < 0) return 1 / power(base, -exponent);

  let result = 1;
  for (let i = 0; i < exponent; i++) {
    result *= base;
  }
  return result;
}

console.log(power(2, 10));  // 1024
console.log(power(5, 0));   // 1
console.log(power(2, -3));  // 0.125
```

---

## Problem 15: Collatz Conjecture (Hailstone Sequence)

**Problem:** Start with any positive integer n. If n is even, divide by 2. If odd, multiply by 3 and add 1. Repeat until you reach 1.

```javascript
function collatzSequence(n) {
  const sequence = [n];

  while (n !== 1) {
    if (n % 2 === 0) {
      n = n / 2;
    } else {
      n = 3 * n + 1;
    }
    sequence.push(n);
  }

  return sequence;
}

console.log(collatzSequence(6));
// [6, 3, 10, 5, 16, 8, 4, 2, 1]
```

---

## Problem 16: Sieve of Eratosthenes

**Problem:** Find all primes up to N efficiently.

```javascript
function sieveOfEratosthenes(n) {
  const isPrime = new Array(n + 1).fill(true);
  isPrime[0] = isPrime[1] = false;

  for (let i = 2; i * i <= n; i++) {
    if (isPrime[i]) {
      for (let j = i * i; j <= n; j += i) {
        isPrime[j] = false;
      }
    }
  }

  const primes = [];
  for (let i = 2; i <= n; i++) {
    if (isPrime[i]) primes.push(i);
  }

  return primes;
}

console.log(sieveOfEratosthenes(50));
// [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47]
```

---

## Common Patterns Summary

| Pattern | Technique | Example |
|---------|-----------|---------|
| Running total | Accumulate in a variable | Sum, product |
| Find max/min | Compare each element | Largest, smallest |
| Digit extraction | `% 10` and `/ 10` | Reverse, palindrome |
| Range iteration | Fixed `for` loop | Factorial, sum |
| Conditional iteration | `while` with condition | GCD, Collatz |
| Nested iteration | Loop inside loop | Primes in range |
| Boolean flag | Track state | Prime check |

---

## Practice Exercises

### Exercise 1: Strong Number

A strong number equals the sum of the factorial of its digits.

```javascript
console.log(isStrong(145)); // true (1! + 4! + 5! = 1 + 24 + 120 = 145)
```

### Exercise 2: Harshad Number

A number divisible by the sum of its digits.

```javascript
console.log(isHarshad(18)); // true (1+8=9, 18%9===0)
```

### Exercise 3: Decimal to Any Base

Convert a decimal number to any base (2-16).

```javascript
console.log(toBase(255, 16)); // "FF"
console.log(toBase(10, 2));   // "1010"
```

### Exercise 4: Trailing Zeros in Factorial

Count trailing zeros in n! without calculating the factorial.

```javascript
console.log(trailingZeros(100)); // 24
```

### Exercise 5: Square Root (Babylonian Method)

Calculate square root without using `Math.sqrt()`.

```javascript
console.log(squareRoot(25));  // 5
console.log(squareRoot(2));   // ~1.414
```

---

## Summary

- Loops are essential for **iteration**, **accumulation**, and **search**
- Always look for **mathematical optimizations** (e.g., √n for primes)
- **Digit manipulation** uses `% 10` (last digit) and `/ 10` (remove last digit)
- **Running totals** and **comparisons** are the most common loop patterns
- Practice converting problems into **step-by-step algorithms**
- Test with edge cases: 0, 1, negative numbers, large inputs

---

## Next Steps

Build on these fundamentals with:
- **Arrays** — working with collections of data
- **Functions** — organizing code into reusable blocks
- **Object-Oriented JavaScript** — structuring larger programs

Happy coding! 🚀
