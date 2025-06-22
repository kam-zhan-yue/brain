as string` vs `String()`

#### `as string`
- **TypeScript-only:**  
  This is a type assertion. It tells TypeScript “trust me, this value is a string,” but **it does not actually convert the value at runtime**.
- **No runtime effect:**  
  If the value is a number (e.g., `2`), `as string` just tells TypeScript to treat it as a string, but at runtime, it’s still a number.

#### `String()`
- **JavaScript function:**  
  This actually converts the value to a string at runtime.
- **Example:**  
  ```ts
  const num = 2;
  const str1 = num as string; // TypeScript thinks this is a string, but it's still a number at runtime!
  const str2 = String(num);   // This is actually "2" (a string) at runtime.
  ```

#### Demo:
```ts
const num = 2;
const fakeString = num as unknown as string;
console.log(typeof fakeString); // "number" at runtime!

const realString = String(num);
console.log(typeof realString); // "string" at runtime!