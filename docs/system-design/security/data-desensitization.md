---
title: Data Masking Solution Summary
category: System Design
tag:
  - Security
---

<!-- @include: @article-header.snippet.md -->

> This article is a reprint and improvement of [Hutool: One Line of Code to Achieve Data Masking - JD Cloud Developer](https://mp.weixin.qq.com/s/1qFWczesU50ndPPLtABHFg).

## What is Data Masking

### Definition of Data Masking

Data masking is defined in Baidu Encyclopedia as follows:

> Data masking refers to the transformation of certain sensitive information through masking rules to achieve reliable protection of sensitive private data. This allows the masked real data set to be safely used in development, testing, and other non-production environments as well as outsourcing environments. In cases involving customer security data or some commercially sensitive data, real data is modified and provided for testing use without violating system rules, such as ID numbers, phone numbers, card numbers, customer numbers, and other personal information that need to be masked. It is one of the database security technologies.

In summary, data masking refers to the transformation of certain sensitive information through masking rules to achieve reliable protection of sensitive private data.

During the data masking process, different algorithms and techniques are typically employed to process the data according to different needs and scenarios. For example, for ID card numbers, a masking algorithm can be used to retain the first few digits while replacing the other digits with "X" or "\*"; for names, a pseudonymization algorithm can replace real names with randomly generated pseudonyms.

### Common Masking Rules

Common masking rules are designed to protect the security of sensitive data by transforming or modifying it during processing and storage.

Here are several common masking rules:

- Replacement (Common): Replace specific characters or character sequences in sensitive data with other characters. For example, replace the middle digits of a credit card number with asterisks (\*).
- Deletion: Randomly delete part of the sensitive data. For instance, delete 3 random digits from a phone number.
- Rearrangement: Shuffle the order of certain characters or fields in the original data. For example, randomly swap positions of digits in an ID card number.
- Noise Addition: Inject some errors or noise into the data to achieve the effect of data masking. For example, add some randomly generated characters to sensitive data.
- Encryption (Common): Use encryption algorithms to convert sensitive data into ciphertext. For example, hash a bank card number using MD5 or SHA-256. A summary of common encryption algorithms can be found in this article: <https://javaguide.cn/system-design/security/encryption-algorithms.html>.
- ……

## Common Masking Tools

### Hutool

Hutool is a Java utility class that encapsulates various JDK methods related to files, streams, encryption and decryption, encoding, regular expressions, threads, XML, etc., forming various utility classes, while providing the following components:

|       Module       |                                                              Description                                                              |
| :----------------: | :-----------------------------------------------------------------------------------------------------------------------------------: |
|     hutool-aop     |                                 JDK dynamic proxy encapsulation, providing aspect support without IOC                                 |
| hutool-bloomFilter |                                       Bloom filter, providing some hash algorithm bloom filters                                       |
|    hutool-cache    |                                                      Simple cache implementation                                                      |
|    hutool-core     |                                    Core, including Bean operations, dates, various utilities, etc.                                    |
|    hutool-cron     |                            Scheduled task module, providing scheduled tasks with Crontab-like expressions                             |
|   hutool-crypto    |                Encryption and decryption module, providing encapsulation of symmetric, asymmetric, and hash algorithms                |
|     hutool-db      |                                 Data operations encapsulated after JDBC, based on ActiveRecord ideas                                  |
|     hutool-dfa     |                                              Multi-keyword search based on the DFA model                                              |
|    hutool-extra    | Extension module, encapsulating third-party libraries (template engines, emails, Servlets, QR codes, Emojis, FTP, tokenization, etc.) |
|    hutool-http     |                                         HTTP client encapsulation based on HttpUrlConnection                                          |
|     hutool-log     |                                        Automatic recognition of logging facade implementations                                        |
|   hutool-script    |                                          Script execution encapsulation, such as Javascript                                           |
|   hutool-setting   |                                 More powerful Setting configuration file and Properties encapsulation                                 |
|   hutool-system    |                                   System parameter invocation encapsulation (JVM information, etc.)                                   |
|    hutool-json     |                                                          JSON implementation                                                          |
|   hutool-captcha   |                                                Image verification code implementation                                                 |
|     hutool-poi     |                                                Encapsulation for Excel and Word in POI                                                |
|   hutool-socket    |                                            Socket encapsulation based on Java NIO and AIO                                             |
|     hutool-jwt     |                                           JSON Web Token (JWT) encapsulation implementation                                           |

Each module can be introduced separately based on needs
