I don't...
title: Character Collection
Category: Database
Tag:

- Database base
  I don't...

MySQL character coding consists of two sets of UTF-8 codes: **`utf8`** and **`utf8mb4`**.

If you use **`utf8`**, the storage of emoji symbols and some of the more complex Chinese characters would result in an error.

Why is that? This article can answer you from the source.

What's the character set?

Characters are the synonyms of various words and symbols, including country text, pegs, expressions, numbers, etc. **Character set** is a set of characters. The character set is of a larger variety, and the range of characters that each character set can express is usually different; for example, some character sets are not able to indicate certain characters.

**Computers can only store binary data. How do you store characters like English, Chinese, and emojis?**

We have to match these characters with binary data; for example, the character "a" corresponds to "0110001" and the other way around, "0110001" corresponds to "a". We call the character-to-binary data process "**character encoding**" and, conversely, the binary data-to-character process "**character decoding**".

# What's the character code?

Character coding is a method of converting a character into binary data in a computer, which can be considered a mapping rule. In other words, character coding is intended to enable computers to store and transmit text messages.

Each character set has its own character coding rules, and the usual character set coding rules are ASCII, GB2312, GBK, GB18030, Big5, UTF-8, UTF-16, etc.

# What are the common character sets?

Common character sets are: ASCII, GB2312, GB18030, GBK, Unicode...

The main differences between different character sets are:

- Range of characters
- Encoding method

# ASCII

**ASCII** (**A**merican**S**tandard**C**ode for**I**nformation**I**nterchange, United States Standard Code for Information Exchange) is a set of characters (which is also the limitation of the ASCII character set) used mainly in modern American English.

**Why is the ASCII charset not taking into account other characters such as Chinese?** Because the computer was invented by the Americans at a time when computer development was still in its infancy and had not yet been used on a large scale in other countries. Therefore, the U.S. release of the ASCII character set did not take into account language compatibility with other countries.

The ASCII character set has so far defined 128 characters, of which 33 control characters (e.g., return, delete) cannot be displayed.

One ASCII code length is one byte or eight bits; for example, the "a" corresponding ASCII code is "0110001". However, the highest position is 0, which is used only as a checkpoint, and the remaining 7 bits are combined with 0 and 1, so the ASCII character set can define 128 (2^7) characters.

Because the ASCII code can represent very few characters, later, the extension was **ASCII Extended Character Set**. The ASCII Extended Character Set uses 8 bits as a character, so the ASCII Extended Character Set can define 256 (2^8) characters.

[ASCII character code](https://oss.javaguide.cn/github/javaguide/csdn/c1c6375d08ca268690cef2b13591a5b4.png)

# GB2312

As we said above, the ASCII character set is a modern American English character set. As a result, many countries have developed character sets for their own national languages.

The GB2312 character set is a more homogenous set of characters that contains more than 6700 Chinese characters and basically covers the vast majority of commonly used Chinese characters. However, the GB2312 character set does not support the vast majority of rare and complex characters.

For English characters, the GB2312 code and the ASCII code are the same, and 1 byte code is sufficient. For non-English characters, 2 byte encoding is required.

# GBK

The GBK character set can be seen as an extension of the GB2312 character set, compatible with the GB2312 character set, containing more than 20,000 Chinese characters.

K in GBK is the initial of "Kuo" in the Chinese spelling Kuo Zhan (extended).

# GB18030

GB18030 is fully compatible with the GB2312 and GBK charsets, which incorporate the text of the national minorities in China and include Japanese and Korean characters, and is the most comprehensive set of characters to date, with more than 70,000.

# BIG5

The BIG5 is mainly aimed at Chinese and has over 13,000 Chinese characters.

# Unicode & UTF-8

In order to be more appropriate
