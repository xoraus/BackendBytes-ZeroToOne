# Converting Callbacks to Promises

## Overview

While modern JavaScript heavily relies on Promises and async/await, much legacy code and many Node.js APIs still use callbacks. Learning how to **promisify** — convert callback-based functions to Promise-based ones — is an essential skill for modernizing codebases, working with older libraries, and writing cleaner asynchronous logic.

---

## Why Convert to Promises?

### Callback Version (Hard to Read)

```javascript
fs.readFile("config.json", "utf8", (err, data) => {
  if (err) {
    console.error("Failed to read config:", err);
    return;
  }
  fs.writeFile("backup.json", data, (err) => {
    if (err) {
      console.error("Failed to write backup:", err);
      return;
    }
    console.log("Backup created successfully");
  });
});
```

### Promise Version (Clean and Flat)

```javascript
readFilePromise("config.json", "utf8")
  .then(data => writeFilePromise("backup.json", data))
  .then(() => console.log("Backup created successfully"))
  .catch(error => console.error("Failed:", error));
```

---

## The Node.js Callback Pattern

Most Node.js callbacks follow this convention:

```javascript
function asyncOperation(arg1, arg2, callback) {
  // callback signature: callback(error, result)
  // If error: callback(error)
  // If success: callback(null, result)
}
```

**Examples:**
- `fs.readFile(path, encoding, callback)`
- `fs.writeFile(path, data, callback)`
- `setTimeout(callback, delay)`

---

## Manual Conversion

### Wrapping a Single Function

```javascript
const fs = require("fs");

// Original callback-based function
// fs.readFile(path, encoding, callback)

// Promise wrapper
function readFilePromise(path, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(path, encoding, (error, data) => {
      if (error) {
        reject(error);
      } else {
        resolve(data);
      }
    });
  });
}

// Usage
readFilePromise("file.txt", "utf8")
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### Generic Promisify Function

```javascript
function promisify(fn) {
  return function(...args) {
    return new Promise((resolve, reject) => {
      fn(...args, (error, result) => {
        if (error) {
          reject(error);
        } else {
          resolve(result);
        }
      });
    });
  };
}

// Usage
const readFilePromise = promisify(fs.readFile);
const writeFilePromise = promisify(fs.writeFile);

readFilePromise("input.txt", "utf8")
  .then(data => writeFilePromise("output.txt", data))
  .then(() => console.log("Done!"))
  .catch(error => console.error(error));
```

---

## Node.js util.promisify

Node.js provides a built-in utility for this:

```javascript
const util = require("util");
const fs = require("fs");

// Convert callback-based functions
const readFile = util.promisify(fs.readFile);
const writeFile = util.promisify(fs.writeFile);
const readdir = util.promisify(fs.readdir);
const stat = util.promisify(fs.stat);

// Now use them with Promises!
async function listFiles(directory) {
  const files = await readdir(directory);
  for (const file of files) {
    const fileStat = await stat(`${directory}/${file}`);
    console.log(`${file}: ${fileStat.size} bytes`);
  }
}
```

### Functions with Custom `promisify` Symbol

Some Node.js functions have a built-in `Symbol.for('nodejs.util.promisify.custom')` property:

```javascript
const { exec } = require("child_process");
const util = require("util");

const execPromise = util.promisify(exec);

async function runCommand() {
  const { stdout, stderr } = await execPromise("ls -la");
  console.log(stdout);
}
```

---

## Converting Common Patterns

### setTimeout

```javascript
// Callback version
setTimeout(() => {
  console.log("Done!");
}, 1000);

// Promise version
function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

async function main() {
  console.log("Starting...");
  await delay(1000);
  console.log("Done!");
}
```

### setInterval (One-time)

```javascript
function waitForCondition(checkFn, intervalMs = 100, timeoutMs = 5000) {
  return new Promise((resolve, reject) => {
    const startTime = Date.now();

    const interval = setInterval(() => {
      if (checkFn()) {
        clearInterval(interval);
        resolve();
      }

      if (Date.now() - startTime > timeoutMs) {
        clearInterval(interval);
        reject(new Error("Timeout waiting for condition"));
      }
    }, intervalMs);
  });
}

// Usage
await waitForCondition(() => document.readyState === "complete");
```

### XMLHttpRequest

```javascript
function request(url, method = "GET", data = null) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open(method, url);

    xhr.onload = () => {
      if (xhr.status >= 200 && xhr.status < 300) {
        resolve(xhr.response);
      } else {
        reject(new Error(`HTTP ${xhr.status}: ${xhr.statusText}`));
      }
    };

    xhr.onerror = () => reject(new Error("Network error"));
    xhr.ontimeout = () => reject(new Error("Request timeout"));

    xhr.send(data);
  });
}

request("https://api.example.com/data")
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### Geolocation API

```javascript
function getCurrentPosition() {
  return new Promise((resolve, reject) => {
    navigator.geolocation.getCurrentPosition(resolve, reject);
  });
}

getCurrentPosition()
  .then(position => {
    console.log(position.coords.latitude, position.coords.longitude);
  })
  .catch(error => console.error(error.message));
```

### Database Queries

```javascript
// Callback version
connection.query("SELECT * FROM users", (error, results) => {
  if (error) throw error;
  console.log(results);
});

// Promise version
function queryPromise(sql, values) {
  return new Promise((resolve, reject) => {
    connection.query(sql, values, (error, results) => {
      if (error) reject(error);
      else resolve(results);
    });
  });
}

// Usage
const users = await queryPromise("SELECT * FROM users WHERE active = ?", [true]);
```

---

## Converting Event-Based APIs

Some APIs use events instead of callbacks. You can convert these too:

### File Stream Example

```javascript
const fs = require("fs");

function readStreamToString(stream) {
  return new Promise((resolve, reject) => {
    let data = "";

    stream.on("data", chunk => {
      data += chunk;
    });

    stream.on("end", () => resolve(data));
    stream.on("error", reject);
  });
}

const stream = fs.createReadStream("file.txt", "utf8");
const content = await readStreamToString(stream);
console.log(content);
```

### Child Process

```javascript
const { spawn } = require("child_process");

function runCommand(command, args) {
  return new Promise((resolve, reject) => {
    const process = spawn(command, args);
    let stdout = "";
    let stderr = "";

    process.stdout.on("data", data => stdout += data);
    process.stderr.on("data", data => stderr += data);

    process.on("close", code => {
      if (code === 0) {
        resolve({ stdout, stderr });
      } else {
        reject(new Error(`Process exited with code ${code}: ${stderr}`));
      }
    });

    process.on("error", reject);
  });
}
```

---

## Batch Conversion

### Promisify All Methods

```javascript
const util = require("util");
const fs = require("fs");

// Convert all fs methods at once
const fsPromises = {
  readFile: util.promisify(fs.readFile),
  writeFile: util.promisify(fs.writeFile),
  readdir: util.promisify(fs.readdir),
  stat: util.promisify(fs.stat),
  mkdir: util.promisify(fs.mkdir),
  access: util.promisify(fs.access)
};

// Or use the built-in fs.promises (Node.js 10+)
const fsPromisesBuiltIn = require("fs").promises;
```

### Converting a Module

```javascript
function promisifyAll(module) {
  const promisified = {};

  for (const key of Object.keys(module)) {
    if (typeof module[key] === "function") {
      promisified[key] = util.promisify(module[key]);
    }
  }

  return promisified;
}

const fsAsync = promisifyAll(fs);
```

---

## Callbackify (The Reverse)

Sometimes you need to go the other way — convert Promises back to callbacks:

```javascript
const util = require("util");

async function fetchData(url) {
  const response = await fetch(url);
  return response.json();
}

// Convert back to callback style
const fetchDataCallback = util.callbackify(fetchData);

fetchDataCallback("https://api.example.com/data", (error, result) => {
  if (error) {
    console.error(error);
  } else {
    console.log(result);
  }
});
```

---

## Common Mistakes

### Mistake 1: Not Handling the Error Parameter

```javascript
// ❌ Missing error handling
function badPromisify(fn) {
  return function(...args) {
    return new Promise(resolve => {
      fn(...args, (error, result) => resolve(result));
      // Error is silently ignored!
    });
  };
}

// ✅ Proper error handling
function goodPromisify(fn) {
  return function(...args) {
    return new Promise((resolve, reject) => {
      fn(...args, (error, result) => {
        if (error) reject(error);
        else resolve(result);
      });
    });
  };
}
```

### Mistake 2: Forgetting Callback Functions Have Multiple Success Values

```javascript
// ❌ Only capturing first result
const execPromise = util.promisify(require("child_process").exec);
// exec callback: (error, stdout, stderr)
// util.promisify handles this correctly by returning { stdout, stderr }

// Manual version that captures all:
function execPromise(command) {
  return new Promise((resolve, reject) => {
    require("child_process").exec(command, (error, stdout, stderr) => {
      if (error) {
        reject(error);
      } else {
        resolve({ stdout, stderr });
      }
    });
  });
}
```

### Mistake 3: Not Checking if Already Promisified

```javascript
// ❌ Double-wrapping
const readFile = util.promisify(fs.readFile);
const readFileAgain = util.promisify(readFile); // Unnecessary

// ✅ Check if already a Promise-based API
const fsPromises = fs.promises || {
  readFile: util.promisify(fs.readFile),
  // ...
};
```

---

## Practice Exercises

### Exercise 1: Promisify setTimeout

Write a `sleep(ms)` function that returns a Promise.

```javascript
function sleep(ms) {
  // Your code
}

async function demo() {
  console.log("Start");
  await sleep(1000);
  console.log("After 1 second");
}
```

### Exercise 2: Promisify LocalStorage

Convert `localStorage.getItem/setItem` to Promise-based API.

```javascript
const storage = {
  getItem: promisifyStorageGet(),
  setItem: promisifyStorageSet()
};

await storage.setItem("key", "value");
const value = await storage.getItem("key");
```

### Exercise 3: Promisify a Node-style Module

Convert this fictional database module to Promises:

```javascript
const db = {
  connect: (config, callback) => { /* ... */ },
  query: (sql, params, callback) => { /* ... */ },
  close: (callback) => { /* ... */ }
};
```

### Exercise 4: Promisify with Timeout

Create a wrapper that adds a timeout to any callback function.

```javascript
function promisifyWithTimeout(fn, timeoutMs) {
  // Return a promisified version that rejects if it takes too long
}
```

### Exercise 5: Converting Event Emitters

Convert a simple EventEmitter pattern to a Promise.

```javascript
function waitForEvent(emitter, eventName) {
  // Return a Promise that resolves when the event fires
}

// Usage:
await waitForEvent(myEmitter, "ready");
console.log("Ready event received!");
```

---

## Summary

- **Promisification** converts callback-based APIs to Promise-based ones
- Wrap callbacks in `new Promise((resolve, reject) => { ... })`
- Node.js provides `util.promisify()` for standard callback patterns
- Event-based APIs can be promisified by listening for specific events
- `fs.promises` and modern modules often have Promise APIs built-in
- Always handle both `resolve` and `reject` when converting
- Be aware of callbacks with **multiple success values** (like `exec`)
- The reverse operation (`util.callbackify`) converts Promises back to callbacks

---

## Next Steps

Now that you can convert any callback to a Promise:
- **Async/Await** — the cleanest syntax for working with Promises
- **Error Handling in Async Code** — robust patterns for production
- **Modern Node.js APIs** — many already use Promises natively

Happy coding! 🚀
