# Go's like error handling in TypeScript (sync/async)

## Installation
```bash
npm install go-error-handling
```

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

## Implementation

Create a `tryCatchUtils.ts` file:

```ts
/**
 * Executes a synchronous function and returns a tuple of [result, error]
 * @param fn - The function to execute
 * @returns A tuple where the first element is the result (or null on error)
 *          and the second element is the error (or null on success)
 */
export function tryCatch<T>(
  fn: () => T
): [T, null] | [null, Error] {
  try {
    const result = fn();
    return [result, null];
  } catch (error) {
    return [null, error instanceof Error ? error : new Error(String(error))];
  }
}

/**
 * Executes an asynchronous function and returns a tuple of [result, error]
 * @param fn - The async function to execute
 * @returns A promise that resolves to a tuple where the first element is the result
 *          (or null on error) and the second element is the error (or null on success)
 */
export async function tryCatchAsync<T>(
  fn: () => Promise<T>
): Promise<[T, null] | [null, Error]> {
  try {
    const result = await fn();
    return [result, null];
  } catch (error) {
    return [null, error instanceof Error ? error : new Error(String(error))];
  }
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
// Use data here (might be undefined!)
processData(data);
```

### After (Go-style):

```ts
const [data, error] = await tryCatchAsync(async () => 
  fetch('/api/data').then(r => r.json())
);

if (!error) {
  console.log("Success:", data);
  processData(data); // TypeScript knows data is defined here
} else {
  console.error("Error:", error);
}
```
