I don't...
title: BigDecimal Detail
Category:
Tag:

- Java Foundation.
  I don't...

The Ali Baba Java Development Manual states: “In order to avoid the loss of precision, `BigDecimal` may be used for the calculation of floating point numbers.”

Do you still have the risk of loss of precision in the calculation of floating point numbers? Yes!

Example code:

```Java
float a = 2.0f - 1.9f;
float b = 1.8f - 1.7f;
System.out.println(a);//0.1000024
System.out.println(b);// 0.099999995
System.out.println(a == b);// false
```

**Why is there a risk of loss of precision when floating point `float` or `double` operations?**

This has much to do with the mechanism of computer preservation of decimals. We know that computers are binary and that the width of computers when they represent a number is limited, and the infinity of the cycle is stored on the computer and can only be cut off, so it leads to loss of decimal precision. This explains why decimals cannot be expressed precisely in binary form.

For example, 0.2 under Decimal cannot be accurately converted to binary decimals:

```Java
// 0.2 The process of conversion to binary numbers is to multiply by two continuously until no decimals exist,
// In this calculation, the integer portion obtained is the result of a binary.
0.2 * 2 = 0.4 -> 0
0.4 * 2 = 0.8 -> 0
0.8 * 2 = 1.6 -> 1
0.6 * 2 = 1.2 -> 1
0.2 * 2 = 0.4 -> 0
I don't know.
```

For more information on floating point numbers, it is suggested to look at the article [of (iv) base of computer systems](http://kaito-kiddd.com/2018/08/08/computer-system-float-point/).

# BigDecimal Introduction

`BigDecimal` may achieve the calculation of decimals without loss of precision.

As a rule, most of the business scenarios (e.g., those involving money) that require the accurate calculation of decimals are made through `BigDecimal`.

The Ali Baba Java Development Manual mentions that: **Equivalent values between floating point numbers cannot be compared to basic data types and packaging data types cannot be judged by equals.**

![Image](https://oss.javaguide.cn/javaguide/image-202112113166884.png)

For the specific reasons we have detailed above, we will not mention them here.

In order to solve the problem of loss of the precision of floating point operations, the value of decimals can be defined directly by using `BigDecimal`, followed by the operation of decimals.

```Java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
BigDecimal c = new BigDecimal("0.8");

BigDecimal x = a.subtract(b);
BigDecimal y = b.subtract(c);

System.out.println(x.compareTo(y));//0
```

# BigDecimal Common Method

Create

When we use `BigDecimal`, in order to prevent the loss of precision, we recommend the use of its `BigDecimal` construction method or `BigDecimal.valueOf` static method.

This is also mentioned in the Ali Baba Java Development Manual, as shown in the figure below.

![Image](https://oss.javaguide.cn/javaguide/image-2021120222601.png)

## Add, Subtract, Multiply, Divide

The `add` method is used to add up two `BigDecimal` objects and the `subtract` method is used to reduce two `BigDecimal` objects. The `multiply` method is used to multiply two `BigDecimal` objects and the `divide` method is used to separate two `BigDecimal` objects.

```Java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
System.out.println(a.add(b)); // 1.9
System.out.println(a.subtract(b)); // 0.1
System.out.println(a.multiply(b)); // 0.90
System.out.println(a.divide(b)); // Unable to remove, throw ArithmeticException abnormal
System.out.println(a.divide(b, 2, RoundingMode.HALF_UP)); // 1.11
```

It is important to note here that when we use the `divide` method, we try to use as many as possible three versions of parameters and `RoundingMode` does not choose `UNNECESSARY`, otherwise there is a high risk of `ArithmeticException`, where `scale` indicates the retention of several decimals, `RoundingMode` represents the reservation rule.

```Java
public BigDecimal divide
I don't know.
♪ I'm sorry ♪
```

There are a
