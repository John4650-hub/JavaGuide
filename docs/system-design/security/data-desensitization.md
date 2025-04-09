I don't...
Title: Summary of data desensitization programmes\
Category: System design\
Tag:

- Clear.\
  I don't...

@include: @article-header.snippet.md--

> Reproduced from [Hutool: a line of code for data desensitization - Kyoto Cloud Developer](https://mp.weixin.qq.com/s/1qFWczesU50ndPPLtABHFg)

# What's data desensitization

## Definition of data desensitization

This is defined in the data desensitization encyclopedia:

> Data desensitization refers to data deformation through desensitization rules for certain sensitive information to achieve reliable protection of sensitive privacy data. This allows for the safe use of de-sensitized real data sets in development, testing, and other non-production environments, as well as in outsourced environments. In cases where customer security data or some commercially sensitive data are involved, and subject to the rules of the system, real data are adapted and tested for use, such as personal information such as ID numbers, mobile phone numbers, card numbers, customer numbers, etc., which are required for data desensitization. It's one of the database security techniques.

In general, data desensitization refers to the deformation of certain sensitive information through desensitization rules to achieve reliable protection of sensitive privacy data.

During data desensitization, different algorithms and techniques are usually used to process data according to different needs and scenarios. For example, in the case of identity card numbers, the previous numbers could be retained using masking algorithms, while the other places could be replaced by “X” or “\\”; in the case of names, the authentic name could be replaced by a randomly created pseudonym.

## Common de-sensitization rules

The commonly used de-sensitization rules are designed to protect the security of sensitive data by changing or modifying them when they are processed and stored.

The following are some of the common desensitization rules:

- Replace (commonly used): Replace a specific character or character sequence in sensitive data with other characters. For example, replace the middle numbers in the credit card number with an asterisk (\*) or other characters.
- Delete: Randomly delete parts of sensitive data. For example, delete a random 3-digit number of the phone number.
- Reordering: Disrupt the order of certain characters or fields in the original data. For example, random alternation of identification numbers.
- Noise: Inject some errors or noises into the data, with the effect of being allergic to the data. For example, add randomly generated characters to sensitive data.
- Encryption (commonly used): Use encryption algorithms to convert sensitive data into secret. For example, the bank card number is hashed using functions such as MD5 or SHA-256. A summary of common encryption algorithms can be found in this article: <https://javaguide.cn/system-design/security/encryption-algoriths.html>.
- ...

## Common de-sensitization tools

### Hutool

Hutool is a Java basic tool class that covers the JDK methods of file, flow, encrypted decryption, transcode, regular, thread, XML, etc., into a variety of utility tools, with the following components:

| Module             | Introduction                                                                                                  |
| :----------------- | :------------------------------------------------------------------------------------------------------------ |
| hutool-aop         | JDK Dynamic Proxy Envelope, providing cut-face support under non-IOC                                          |
| hutool-bloomFilter | Bloom filters, providing some of the Hash algorithm's Bloom filters                                           |
| hutool-cache       | Simple Cache                                                                                                  |
| hutool-core        | Core, including Bean Operations, Date, Util, etc.                                                             |
| hutool-cron        | Time job module, which provides a time job for a class Crontab expression                                     |
| hutool-crypto      | Encrypted decryption module to provide symmetric, asymmetric, and abstract algorithm encapsulation            |
| hutool-db          | JDBC encapsulated data operation based on ActiveRecord Thought                                                |
| hutool-dfa         | Multi-key Search based on DFA Model                                                                           |
| hutool-extra       | Extension module for third-party envelopes (team engine, mail, Servlet, 2D code, Emoji, FTP, semiwords, etc.) |
| hutool-http        | HTTP client envelope based on HttpUrlConnection                                                               |
| hutool-log         | Auto-identify the log's realization                                                                           |
| hutool-script      | Script execution envelopes such as Javascript                                                                 |
| hutool-setting     | More powerful setting configuration file and properties encapsulation                                         |
| hutool-system      | System Parameter Call Envelope (JVM Information, etc.)                                                        |
| hutool-json        | JSON processing                                                                                               |
| hutool-captcha     | Photo authentication code                                                                                     |
| hutool-poi         | Envelope for Excel and Word in POI                                                                            |
| hutool-socket      | Socket communication                                                                                          |
| hutool-jwt         | JSON Web Token (JWT) encapsulation                                                                            |

Each module can be introduced on a demand-driven basis, and all modules can be introduced by introducing `hutool-all`. The data desensitization tool used in this paper is in the \`
