# Go's like error handling in TypeScript (sync/async)

## Usage

### Synchronous Operations

```ts
import { tryCatch } from './tryCatchUtils';

// JSON parsing
const [data, error] = tryCatch(() => JSON.parse('{"ok":true}'));
if (!error) {
  console.log("Success:", data);
} else {
  console.error("Error:", error);
}

// Division
const [result, divError] = tryCatch(() => {
  const divisor = 0;
  if (divisor === 0) throw new Error("Division by zero");
  return 10 / divisor;
});

if (!divError) {
  console.log("Result:", result);
}
```

### Asynchronous Operations

```ts
import { tryCatchAsync } from './tryCatchUtils';

// API calls
const [data, error] = await tryCatchAsync(async () => 
  fetch('/api/data').then(r => r.json())
);

if (!error) {
  console.log("Success:", data);
} else {
  console.error("Error:", error);
}

// Database queries
const [user, dbError] = await tryCatchAsync(async () => 
  db.users.findOne({ id: userId })
);

if (!dbError) {
  console.log("User:", user);
}
```

## Why Use This Pattern?

### Before (traditional try-catch):

```ts
let data;
try {
  const response = await fetch('/api/data');
  data = await response.json();
  console.log("Success:", data);
} catch (error) {
  console.error("Error:", error);
  return;
}
// Use data here - might be undefined
processData(data);
```

### After (Go-style):

```ts
const [data, error] = await tryCatchAsync(async () => 
  fetch('/api/data').then(r => r.json())
);

if (!error) {
  console.log("Success:", data);
  processData(data); // ts knows data is defined here
} else {
  console.error("Error:", error);
}
```
