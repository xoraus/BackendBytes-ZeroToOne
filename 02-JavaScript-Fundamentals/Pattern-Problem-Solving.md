# Pattern Problem Solving in JavaScript

## Overview

Pattern problems are classic programming exercises that help you master nested loops, string manipulation, and logical thinking. They're not just for interviews — they build the foundational skills needed to work with grids, matrices, UI layouts, and data visualization. This tutorial covers the most essential patterns and the strategies to solve them.

---

## The Strategy for Solving Patterns

Before jumping into code, follow this systematic approach:

### Step 1: Analyze the Pattern
- How many rows?
- How many columns in each row?
- What changes from row to row?
- What's the relationship between row number and the output?

### Step 2: Identify Variables
- Outer loop: controls rows (usually `i`)
- Inner loop: controls columns (usually `j`)

### Step 3: Find the Formula
- What's printed at position `(row, col)`?
- Is it based on row number, column number, or both?

### Step 4: Code and Test
- Write the loops
- Add the print logic
- Test with small inputs first

---

## Pattern 1: Right-Angled Triangle

```
*
**
***
****
*****
```

**Analysis:**
- Row 1: 1 star
- Row 2: 2 stars
- Row `i`: `i` stars

```javascript
function rightAngledTriangle(n) {
  for (let i = 1; i <= n; i++) {
    let row = "";
    for (let j = 1; j <= i; j++) {
      row += "*";
    }
    console.log(row);
  }
}

rightAngledTriangle(5);
```

**Alternative (using `repeat`):**
```javascript
function rightAngledTriangle(n) {
  for (let i = 1; i <= n; i++) {
    console.log("*".repeat(i));
  }
}
```

---

## Pattern 2: Inverted Right-Angled Triangle

```
*****
****
***
**
*
```

**Analysis:**
- Row 1: `n` stars
- Row 2: `n-1` stars
- Row `i`: `n - i + 1` stars

```javascript
function invertedTriangle(n) {
  for (let i = 1; i <= n; i++) {
    console.log("*".repeat(n - i + 1));
  }
}

invertedTriangle(5);
```

---

## Pattern 3: Pyramid (Centered Triangle)

```
    *
   ***
  *****
 *******
*********
```

**Analysis:**
- Row 1: 4 spaces, 1 star
- Row 2: 3 spaces, 3 stars
- Row `i`: `n - i` spaces, `2*i - 1` stars

```javascript
function pyramid(n) {
  for (let i = 1; i <= n; i++) {
    let spaces = " ".repeat(n - i);
    let stars = "*".repeat(2 * i - 1);
    console.log(spaces + stars);
  }
}

pyramid(5);
```

---

## Pattern 4: Inverted Pyramid

```
*********
 *******
  *****
   ***
    *
```

**Analysis:**
- Row 1: 0 spaces, `2*n - 1` stars
- Row 2: 1 space, `2*n - 3` stars
- Row `i`: `i - 1` spaces, `2*(n - i) + 1` stars

```javascript
function invertedPyramid(n) {
  for (let i = 1; i <= n; i++) {
    let spaces = " ".repeat(i - 1);
    let stars = "*".repeat(2 * (n - i) + 1);
    console.log(spaces + stars);
  }
}

invertedPyramid(5);
```

---

## Pattern 5: Diamond

```
    *
   ***
  *****
 *******
*********
 *******
  *****
   ***
    *
```

Combine the pyramid and inverted pyramid (excluding the middle row twice):

```javascript
function diamond(n) {
  // Upper half (including middle)
  for (let i = 1; i <= n; i++) {
    let spaces = " ".repeat(n - i);
    let stars = "*".repeat(2 * i - 1);
    console.log(spaces + stars);
  }

  // Lower half
  for (let i = n - 1; i >= 1; i--) {
    let spaces = " ".repeat(n - i);
    let stars = "*".repeat(2 * i - 1);
    console.log(spaces + stars);
  }
}

diamond(5);
```

---

## Pattern 6: Number Triangle

```
1
1 2
1 2 3
1 2 3 4
1 2 3 4 5
```

**Analysis:**
- Row `i`: numbers from 1 to `i`

```javascript
function numberTriangle(n) {
  for (let i = 1; i <= n; i++) {
    let row = "";
    for (let j = 1; j <= i; j++) {
      row += j + " ";
    }
    console.log(row.trim());
  }
}

numberTriangle(5);
```

---

## Pattern 7: Floyd's Triangle

```
1
2 3
4 5 6
7 8 9 10
```

**Analysis:**
- Use a counter that increments across all rows

```javascript
function floydsTriangle(n) {
  let num = 1;
  for (let i = 1; i <= n; i++) {
    let row = "";
    for (let j = 1; j <= i; j++) {
      row += num + " ";
      num++;
    }
    console.log(row.trim());
  }
}

floydsTriangle(4);
```

---

## Pattern 8: Pascal's Triangle

```
    1
   1 1
  1 2 1
 1 3 3 1
1 4 6 4 1
```

Each number is the sum of the two directly above it.

```javascript
function pascalsTriangle(n) {
  for (let i = 0; i < n; i++) {
    let row = "";
    // Leading spaces
    row += " ".repeat(n - i - 1);

    let num = 1;
    for (let j = 0; j <= i; j++) {
      row += num + " ";
      num = num * (i - j) / (j + 1);
    }
    console.log(row);
  }
}

pascalsTriangle(5);
```

---

## Pattern 9: Number Pyramid

```
    1
   232
  34543
 4567654
567898765
```

**Analysis:**
- Row `i` starts with number `i`
- Increases to `2*i - 1`
- Then decreases back

```javascript
function numberPyramid(n) {
  for (let i = 1; i <= n; i++) {
    let spaces = " ".repeat(n - i);
    let row = spaces;

    // Increasing part
    for (let j = i; j < 2 * i; j++) {
      row += j;
    }

    // Decreasing part
    for (let j = 2 * i - 2; j >= i; j--) {
      row += j;
    }

    console.log(row);
  }
}

numberPyramid(5);
```

---

## Pattern 10: Hollow Square

```
*****
*   *
*   *
*   *
*****
```

```javascript
function hollowSquare(n) {
  for (let i = 1; i <= n; i++) {
    let row = "";
    for (let j = 1; j <= n; j++) {
      if (i === 1 || i === n || j === 1 || j === n) {
        row += "*";
      } else {
        row += " ";
      }
    }
    console.log(row);
  }
}

hollowSquare(5);
```

---

## Pattern 11: Hollow Diamond

```
    *
   * *
  *   *
 *     *
*       *
 *     *
  *   *
   * *
    *
```

```javascript
function hollowDiamond(n) {
  // Upper half
  for (let i = 1; i <= n; i++) {
    let spaces = " ".repeat(n - i);
    if (i === 1) {
      console.log(spaces + "*");
    } else {
      let middleSpaces = " ".repeat(2 * i - 3);
      console.log(spaces + "*" + middleSpaces + "*");
    }
  }

  // Lower half
  for (let i = n - 1; i >= 1; i--) {
    let spaces = " ".repeat(n - i);
    if (i === 1) {
      console.log(spaces + "*");
    } else {
      let middleSpaces = " ".repeat(2 * i - 3);
      console.log(spaces + "*" + middleSpaces + "*");
    }
  }
}

hollowDiamond(5);
```

---

## Pattern 12: Butterfly Pattern

```
*        *
**      **
***    ***
****  ****
**********
****  ****
***    ***
**      **
*        *
```

```javascript
function butterfly(n) {
  // Upper half
  for (let i = 1; i <= n; i++) {
    let stars = "*".repeat(i);
    let spaces = " ".repeat(2 * (n - i));
    console.log(stars + spaces + stars);
  }

  // Lower half
  for (let i = n - 1; i >= 1; i--) {
    let stars = "*".repeat(i);
    let spaces = " ".repeat(2 * (n - i));
    console.log(stars + spaces + stars);
  }
}

butterfly(5);
```

---

## Pattern 13: Zig-Zag Pattern

```
  *   *
 * * * *
*   *   *
```

```javascript
function zigZag(n) {
  for (let i = 1; i <= 3; i++) {
    let row = "";
    for (let j = 1; j <= n; j++) {
      if ((i + j) % 4 === 0 || (i === 2 && j % 4 === 0)) {
        row += "*";
      } else {
        row += " ";
      }
    }
    console.log(row);
  }
}

zigZag(9);
```

---

## Pattern 14: Binary Triangle

```
1
0 1
1 0 1
0 1 0 1
1 0 1 0 1
```

```javascript
function binaryTriangle(n) {
  for (let i = 1; i <= n; i++) {
    let row = "";
    for (let j = 1; j <= i; j++) {
      row += ((i + j) % 2 === 0 ? "1" : "0") + " ";
    }
    console.log(row.trim());
  }
}

binaryTriangle(5);
```

---

## Pattern 15: Hourglass

```
*********
 *******
  *****
   ***
    *
   ***
  *****
 *******
*********
```

```javascript
function hourglass(n) {
  // Upper inverted pyramid
  for (let i = n; i >= 1; i--) {
    let spaces = " ".repeat(n - i);
    let stars = "*".repeat(2 * i - 1);
    console.log(spaces + stars);
  }

  // Lower pyramid
  for (let i = 2; i <= n; i++) {
    let spaces = " ".repeat(n - i);
    let stars = "*".repeat(2 * i - 1);
    console.log(spaces + stars);
  }
}

hourglass(5);
```

---

## Key Formulas Reference

| Pattern Element | Formula |
|----------------|---------|
| Stars in row `i` (pyramid) | `2*i - 1` |
| Spaces in row `i` (centered) | `n - i` |
| Stars in row `i` (inverted) | `2*(n-i) + 1` |
| Numbers in row `i` | `1` to `i` |
| Total elements in row `i` | Usually equals row number |

---

## Tips for Solving Any Pattern

1. **Start with a small value** (like `n=3` or `n=4`) to trace through manually
2. **Draw a grid** and label row and column numbers
3. **Look for symmetry** — many patterns are mirrored
4. **Break complex patterns** into simpler ones (e.g., diamond = pyramid + inverted pyramid)
5. **Use `repeat()`** for cleaner code when possible
6. **Test edge cases**: `n=1`, `n=0`, large `n`

---

## Practice Exercises

### Exercise 1: Right-Aligned Number Triangle

```
    1
   12
  123
 1234
12345
```

### Exercise 2: Palindromic Pyramid

```
    1
   121
  12321
 1234321
123454321
```

### Exercise 3: Spiral Matrix

Fill an `n x n` matrix in spiral order and print it:

```
1  2  3  4
12 13 14 5
11 16 15 6
10 9  8  7
```

### Exercise 4: Number Crown

```
1        1
12      21
123    321
1234  4321
1234554321
```

### Exercise 5: Rhombus

```
    *****
   *****
  *****
 *****
*****
```

---

## Summary

- Pattern problems train **nested loop** control and **logical reasoning**
- Always analyze: **rows**, **columns**, **spaces**, and **values**
- Common formulas: `2*i-1` stars, `n-i` spaces
- Combine simple patterns to build complex ones
- Use string methods like `repeat()` for cleaner code
- Practice is key — start with simple patterns and work up

---

## Next Steps

Apply your pattern skills to:
- **Problem Solving on Loops** — algorithmic challenges
- **Arrays and Matrices** — 2D data structures
- **Object-Oriented JavaScript** — organizing complex logic

Happy coding! 🚀
