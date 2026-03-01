# Wrapper Classes in Java

This document explains:

- What wrapper classes are  
- Why they exist  
- How they work internally  
- Boxing  
- Unboxing  
- Autoboxing  
- Auto-unboxing  
- Memory behavior  
- Integer caching  
- Common pitfalls  

---

## 1️⃣ What Are Wrapper Classes?

Java has **primitive data types**:

```
int, double, char, boolean, byte, short, long, float
```

These are:
- Not objects  
- Stored directly in memory  
- Faster than objects  

But Java is an object-oriented language, and features like **Collections and Generics** require objects.

So Java provides special classes to wrap primitive values inside objects.

These are called **Wrapper Classes**.

| Primitive | Wrapper Class |
|-----------|---------------|
| int       | Integer       |
| double    | Double        |
| char      | Character     |
| boolean   | Boolean       |
| byte      | Byte          |
| short     | Short         |
| long      | Long          |
| float     | Float         |

Example:

```java
int a = 10;            // primitive
Integer b = 10;        // wrapper object (autoboxing)
```

---

## 2️⃣ Why Wrapper Classes Are Needed

### ✅ 1. Collections Require Objects

This will NOT work:

```java
ArrayList<int> list = new ArrayList<>(); // ❌ Error
```

This works:

```java
ArrayList<Integer> list = new ArrayList<>();
```

Because Collections store **objects**, not primitives.

---

### ✅ 2. Utility Methods

Wrapper classes provide useful methods:

```java
Integer.parseInt("123");
Double.parseDouble("3.14");
```

Primitive types don’t have methods.

---

### ✅ 3. Null Values Support

Primitive cannot store null:

```java
int a = null; // ❌ Not allowed
```

Wrapper can:

```java
Integer a = null; // ✅ Allowed
```

Important in databases and APIs.

---

## 3️⃣ Boxing

**Boxing = Converting primitive → wrapper object**

Manual Boxing (Old style):

```java
int a = 10;
Integer obj = new Integer(a);
```

Primitive 10 is wrapped into an Integer object.

---

## 4️⃣ Unboxing

**Unboxing = Converting wrapper → primitive**

Manual Unboxing:

```java
Integer obj = new Integer(20);
int a = obj.intValue();
```

Extract primitive value from object.

---

## 5️⃣ Autoboxing (Java 5+)

Java automatically converts primitive → wrapper.

```java
int a = 10;
Integer obj = a;   // Autoboxing
```

Internally Java does:

```java
Integer obj = Integer.valueOf(a);
```

---

## 6️⃣ Auto-Unboxing

Wrapper → primitive automatically.

```java
Integer obj = 50;
int a = obj;  // Auto-unboxing
```

Internally Java does:

```java
int a = obj.intValue();
```

---

## 7️⃣ Memory Behavior

### Primitive Example

```java
int a = 10;
```

- Stored directly in stack memory

---

### Wrapper Example

```java
Integer obj = 10;
```

Memory behavior:

- Reference stored in stack  
- Actual Integer object created in heap  

Wrapper uses:
- More memory  
- Slightly slower  
- Because it is an object  

---

## 8️⃣ Integer Caching (Important Interview Concept)

Java caches Integer values between:

```
-128 to 127
```

Example:

```java
Integer a = 100;
Integer b = 100;

System.out.println(a == b);
```

Output:

```
true
```

Now:

```java
Integer a = 200;
Integer b = 200;

System.out.println(a == b);
```

Output:

```
false
```

Because 200 is outside cache range.

Always use `.equals()` to compare wrapper values:

```java
a.equals(b);
```

---

## 9️⃣ NullPointerException Trap

```java
Integer a = null;
int b = a;   // Auto-unboxing
```

This throws:

```
NullPointerException
```

Because internally:

```java
int b = a.intValue();
```

Since `a` is null → exception occurs.

---

## 🔟 Performance Consideration

Wrapper classes:
- Use more memory  
- Slower than primitives  
- Cause extra object creation  

Bad practice:

```java
for (Integer i = 0; i < 1000000; i++)
```

Better:

```java
for (int i = 0; i < 1000000; i++)
```

Use primitives in performance-critical code.

---

## 1️⃣1️⃣ Complete Example

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {

        int a = 10;              // primitive

        Integer obj = a;         // autoboxing

        int b = obj;             // auto-unboxing

        System.out.println(a);
        System.out.println(obj);
        System.out.println(b);

        ArrayList<Integer> list = new ArrayList<>();
        list.add(100);           // autoboxing

        System.out.println(list);
    }
}
```

---

## 🔥 Summary

| Concept | Meaning |
|----------|----------|
| Wrapper Class | Object version of primitive |
| Boxing | primitive → object |
| Unboxing | object → primitive |
| Autoboxing | Automatic primitive → object |
| Auto-unboxing | Automatic object → primitive |
| Integer Cache | -128 to 127 cached |
| Risk | NullPointerException |
