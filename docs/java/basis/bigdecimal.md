---
title: BigDecimal Explained
category: Java
tag:
  - Java Basics
---

The "Alibaba Java Development Manual" mentions: "To avoid precision loss, you can use `BigDecimal` for floating-point arithmetic."

Is there really a risk of precision loss when performing floating-point arithmetic? Indeed, there is!

Example code:

```java
float a = 2.0f - 1.9f;
float b = 1.8f - 1.7f;
System.out.println(a);// 0.100000024
System.out.println(b);// 0.099999905
System.out.println(a == b);// false
```

**Why is there a risk of precision loss when performing operations with floating-point numbers `float` or `double`?**

This is largely related to the way computers store decimal numbers. We know that computers use binary representation, and they have a limited width when representing a number. Infinite repeating decimals can only be truncated when stored, which leads to loss of precision. This explains why decimal fractions cannot be accurately represented in binary.

For example, the decimal 0.2 cannot be precisely converted to its binary representation:

```java
// The process of converting 0.2 to binary involves continuously multiplying by 2 
// until there's no decimal part left. The integer parts obtained during this 
// calculation form the binary result.
0.2 * 2 = 0.4 -> 0
0.4 * 2 = 0.8 -> 0
0.8 * 2 = 1.6 -> 1
0.6 * 2 = 1.2 -> 1
0.2 * 2 = 0.4 -> 0 (looping occurs)
...
```

For more information about floating-point numbers, it is recommended to read the article [Computer System Basics (Four) Floating-point Numbers](http://kaito-kidd.com/2018/08/08/computer-system-float-point/).

## Introduction to BigDecimal

`BigDecimal` can be used to perform arithmetic on decimal numbers without causing precision loss.

Typically, most business scenarios that require precise decimal calculations (such as those involving money) utilize `BigDecimal`.

The "Alibaba Java Development Manual" states: **When comparing equality between floating-point numbers, primitive data types should not use `==`, and wrapper data types should not use `equals()`.**

![](https://oss.javaguide.cn/javaguide/image-20211213101646884.png)

We have already discussed the specific reasons in detail above, so we won't elaborate further.

To solve the problem of precision loss during floating-point arithmetic, you can define decimal values using `BigDecimal` and then perform arithmetic operations.

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
BigDecimal c = new BigDecimal("0.8");

BigDecimal x = a.subtract(b);
BigDecimal y = b.subtract(c);

System.out.println(x.compareTo(y));// 0
```

## Common Methods of BigDecimal

### Creation

When using `BigDecimal`, to prevent precision loss, it is recommended to use its `BigDecimal(String val)` constructor or `BigDecimal.valueOf(double val)` static method to create instances.

The "Alibaba Java Development Manual" mentions this part as shown in the image below.

![](https://oss.javaguide.cn/javaguide/image-20211213102222601.png)

### Addition, Subtraction, Multiplication, Division

The `add` method is used to add two `BigDecimal` objects, and the `subtract` method is used to subtract them. The `multiply` method is for multiplying two `BigDecimal` objects, while `divide` is for dividing them.

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
System.out.println(a.add(b));// 1.9
System.out.println(a.subtract(b));// 0.1
System.out.println(a.multiply(b));// 0.90
System.out.println(a.divide(b));// Throws ArithmeticException due to non-integer result
System.out.println(a.divide(b, 2, RoundingMode.HALF_UP));// 1.11
```

It's important to note that when using the `divide` method, you should preferably use the version with three parameters, and do not choose `RoundingMode.UNNECESSARY`, otherwise you may encounter `ArithmeticException` (in cases where division results in infinite repeating decimals). The `scale` represents the number of decimal places to retain, and `roundingMode` indicates the rounding rule.

```java
public BigDecimal divide(BigDecimal divisor, int scale, RoundingMode roundingMode) {
    return divide(divisor, scale, roundingMode.oldMode);
}
```

There are many rounding rules; here are a few:

```java
public enum RoundingMode {
   // 2.5 -> 3 , 1.6 -> 2
   // -1.6 -> -2 , -2.5 -> -3
   UP(BigDecimal.ROUND_UP),
   // 2.5 -> 2 , 1.6 -> 1
   // -1.6 -> -1 , -2.5 -> -2
   DOWN(BigDecimal.ROUND_DOWN),
   // 2.5 -> 3 , 1.6 -> 2
   // -1.6 -> -1 , -2.5 -> -2
   CEILING(BigDecimal.ROUND_CEILING),
   // 2.5 -> 2 , 1.6 -> 1
   // -1.6 -> -2 , -2.5 -> -3
   FLOOR(BigDecimal.ROUND_FLOOR),
   // 2.5 -> 3 , 1.6 -> 2
   // -1.6 -> -2 , -2.5 -> -3
   HALF_UP(BigDecimal.ROUND_HALF_UP),
   //......
}
```

### Size Comparison

`a.compareTo(b)`: returns -1 if `a` is less than `b`, 0 if `a` equals `b`, and 1 if `a` is greater than `b`.

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
System.out.println(a.compareTo(b));// 1
```

### Setting Decimal Places

Use the `setScale` method to specify how many decimal places to retain and the rounding rule. There are many rounding rules, and you don't need to memorize them as the IDE will offer hints.

```java
BigDecimal m = new BigDecimal("1.255433");
BigDecimal n = m.setScale(3, RoundingMode.HALF_DOWN);
System.out.println(n);// 1.255
```

## Equality Comparison Issues with BigDecimal

The "Alibaba Java Development Manual" points out:

![](https://oss.javaguide.cn/github/javaguide/java/basis/image-20220714161315993.png)

Example of code where using `equals()` for equality comparison with `BigDecimal` causes issues:

```java
BigDecimal a = new BigDecimal("1");
BigDecimal b = new BigDecimal("1.0");
System.out.println(a.equals(b));// false
```

This is because the `equals()` method compares both the value and the scale, whereas `compareTo()` ignores the scale.

The scale of 1.0 is 1, and the scale of 1 is 0. Therefore, `a.equals(b)` results in false.

![](https://oss.javaguide.cn/github/javaguide/java/basis/image-20220714164706390.png)

The `compareTo()` method can be used to compare the values of two `BigDecimal` instances; if they are equal, it returns 0. If the first number is greater than the second, it returns 1, otherwise it returns -1.

```java
BigDecimal a = new BigDecimal("1");
BigDecimal b = new BigDecimal("1.0");
System.out.println(a.compareTo(b));// 0
```

## BigDecimal Utility Class Sharing

There is a widely used `BigDecimal` utility class available online, which provides several static methods to simplify operations with `BigDecimal`.

I have made some simple improvements to it, sharing the source code below:

```java
import java.math.BigDecimal;
import java.math.RoundingMode;

/**
 * A utility class to simplify BigDecimal calculations
 */
public class BigDecimalUtil {

    /**
     * Default precision for division operations
     */
    private static final int DEF_DIV_SCALE = 10;

    private BigDecimalUtil() {
    }

    /**
     * Provides precise addition.
     *
     * @param v1 The first addend
     * @param v2 The second addend
     * @return The sum of the two parameters
     */
    public static double add(double v1, double v2) {
        BigDecimal b1 = BigDecimal.valueOf(v1);
        BigDecimal b2 = BigDecimal.valueOf(v2);
        return b1.add(b2).doubleValue();
    }

    /**
     * Provides precise subtraction.
     *
     * @param v1 The minuend
     * @param v2 The subtrahend
     * @return The difference of the two parameters
     */
    public static double subtract(double v1, double v2) {
        BigDecimal b1 = BigDecimal.valueOf(v1);
        BigDecimal b2 = BigDecimal.valueOf(v2);
        return b1.subtract(b2).doubleValue();
    }

    /**
     * Provides precise multiplication.
     *
     * @param v1 The multiplicand
     * @param v2 The multiplier
     * @return The product of the two parameters
     */
    public static double multiply(double v1, double v2) {
        BigDecimal b1 = BigDecimal.valueOf(v1);
        BigDecimal b2 = BigDecimal.valueOf(v2);
        return b1.multiply(b2).doubleValue();
    }

    /**
     * Provides (relatively) precise division. When non-integer results occur, the result
     * is kept to 10 decimal places, with rounding.
     *
     * @param v1 The dividend
     * @param v2 The divisor
     * @return The quotient of the two parameters
     */
    public static double divide(double v1, double v2) {
        return divide(v1, v2, DEF_DIV_SCALE);
    }

    /**
     * Provides (relatively) precise division. When non-integer results occur, the scale
     * parameter specifies the precision, with rounding.
     *
     * @param v1    The dividend
     * @param v2    The divisor
     * @param scale Specifies how many decimal places to keep.
     * @return The quotient of the two parameters
     */
    public static double divide(double v1, double v2, int scale) {
        if (scale < 0) {
            throw new IllegalArgumentException(
                    "The scale must be a positive integer or zero");
        }
        BigDecimal b1 = BigDecimal.valueOf(v1);
        BigDecimal b2 = BigDecimal.valueOf(v2);
        return b1.divide(b2, scale, RoundingMode.HALF_EVEN).doubleValue();
    }

    /**
     * Provides precise rounding of decimal places.
     *
     * @param v     The number to be rounded
     * @param scale The number of decimal places to keep
     * @return The rounded result
     */
    public static double round(double v, int scale) {
        if (scale < 0) {
            throw new IllegalArgumentException(
                    "The scale must be a positive integer or zero");
        }
        BigDecimal b = BigDecimal.valueOf(v);
        BigDecimal one = new BigDecimal("1");
        return b.divide(one, scale, RoundingMode.HALF_UP).doubleValue();
    }

    /**
     * Provides precise type conversion to Float.
     *
     * @param v The number to be converted
     * @return The conversion result
     */
    public static float convertToFloat(double v) {
        BigDecimal b = new BigDecimal(v);
        return b.floatValue();
    }

    /**
     * Provides precise type conversion to Int without rounding.
     *
     * @param v The number to be converted
     * @return The conversion result
     */
    public static int convertsToInt(double v) {
        BigDecimal b = new BigDecimal(v);
        return b.intValue();
    }

    /**
     * Provides precise type conversion to Long.
     *
     * @param v The number to be converted
     * @return The conversion result
     */
    public static long convertsToLong(double v) {
        BigDecimal b = new BigDecimal(v);
        return b.longValue();
    }

    /**
     * Returns the larger of two values.
     *
     * @param v1 The first number to compare
     * @param v2 The second number to compare
     * @return The larger of the two values
     */
    public static double returnMax(double v1, double v2) {
        BigDecimal b1 = new BigDecimal(v1);
        BigDecimal b2 = new BigDecimal(v2);
        return b1.max(b2).doubleValue();
    }

    /**
     * Returns the smaller of two values.
     *
     * @param v1 The first number to compare
     * @param v2 The second number to compare
     * @return The smaller of the two values
     */
    public static double returnMin(double v1, double v2) {
        BigDecimal b1 = new BigDecimal(v1);
        BigDecimal b2 = new BigDecimal(v2);
        return b1.min(b2).doubleValue();
    }

    /**
     * Precisely compares two numbers.
     *
     * @param v1 The first number to compare
     * @param v2 The second number to compare
     * @return If the two numbers are equal, returns 0; if the first number is greater,
     * returns 1; otherwise returns -1
     */
    public static int compareTo(double v1, double v2) {
        BigDecimal b1 = BigDecimal.valueOf(v1);
        BigDecimal b2 = BigDecimal.valueOf(v2);
        return b1.compareTo(b2);
    }

}
```

Related issue: [Propose to set the rounding rule to RoundingMode.HALF_EVEN, that is, round half away from zero, #2129](https://github.com/Snailclimb/JavaGuide/issues/2129).

![RoundingMode.HALF_EVEN](https://oss.javaguide.cn/github/javaguide/java/basis/RoundingMode.HALF_EVEN.png)

## Conclusion

Floating-point numbers cannot be accurately represented in binary, hence there is a risk of precision loss.

However, Java offers `BigDecimal` for working with floating-point numbers. The implementation of `BigDecimal` utilizes `BigInteger` (for handling large integers), and the key difference is that `BigDecimal` incorporates the concept of decimal places.
