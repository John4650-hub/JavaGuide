I don't...
title: Common encryption algorithm summary
Category: System design
Tag:

- Clear.
  I don't...

Encryption algorithms are a mathematical conversion technique designed to protect data from being read or modified by unauthorized persons. Encryption algorithms can be divided into three main categories: symmetric encryption algorithms, asymmetric encryption algorithms, and Hash algorithms (also known as summary algorithms).

The scenarios that often require encryption algorithms in day-to-day development:

1. The passwords stored in the database need to be encrypted using Hash algorithms (e.g. BCrypt) after salting.
1. Sensitive data such as bank card numbers and identity numbers, which are kept in a database, need to be preserved using symmetric encryption algorithms (e.g. AES).
1. Sensitive data transmitted by the network, such as bank card numbers and identity numbers, require HTTPS + asymmetric encryption algorithms (e.g. RSA) to ensure the security of the data transmitted.
1. ...

Ps: Strictly speaking, the Hash algorithm does not really belong to an encrypted algorithm, but it can be used in certain encryption scenarios (e.g. encryption), which can be seen as a parallel. Encryption algorithms usually refer to algorithms that can be converted to a secret and can be restored to the specified algorithm in some way (e.g. a key). The Hash algorithm is a one-way process that converts the input into a fixed-length, seemingly random Hash value, but the process is irreversible, that is, the original information cannot be recovered from the Hash value.

# Hash algorithm

The Hash algorithm is also called a hash function or a summary algorithm, and its function is to generate a single identifier of a fixed length for data of any length, also known as a Hash value, a hash value, or a summary of messages (collectively referred to as a Hash value).

![Hash Algorithmic Effects Demonstration](https://oss.javaguide.cn/github/javaguide/system-design/security/encryption-algoriths/hash-finance-effect-development.png)

The Hash algorithm is irreversible, and you can't get the original value through the value after Hash.

The Hash value function can be used to validate data integrity and consistency.

Two practical examples:

- Encryption by using the Hash algorithm when saving a password to a database can be judged by comparing the Hash values entered by the user with the Hash values stored in the database.
- When downloading a document, we can judge whether the document has been altered or damaged by comparing the value of the document with the value of the officially provided Hash;

This algorithm is characterized by irreversibility:

- The original data cannot be returned from the Hash value.
- Any change in the raw data would lead to a significant change in the Hash value.

Hash algorithms can be divided into two simple categories:

1. **Encrypted Hash algorithm**: A more secure Hash algorithm, which provides a measure of data integrity protection and data protection against tampering, can withstand a measure of attack, is relatively high in safety, but less in performance, and applies to scenarios that require high security requirements. For example, SHA2, SHA3, SM3, RIPEMD-160, BLAKE2, SipHash, etc.
1. **Unencrypted Hash algorithm**: The relatively less secure Hash algorithm, which is vulnerable to violent means of attack, such as cracking and conflict attacks, but is of high performance and suitable for an operating scenario that does not require security. For example, CRC32, MurmurHash3, SipHash, etc.

In addition to these two, there are special Hash algorithms, such as the more secure **Slow Hash algorithm**.

Common Hash algorithms are:

- MD (Message Digest algorithm): MD2, MD4, MD5, etc., are no longer recommended.
- SHA (Secure Hash Algorithm, safe Hash algorithm): low security for SHA-1 series, high security for SHA2, SHA3 series.
- State secret algorithms: for example, SM2, SM3, SM4, of which SM2 is an asymmetric encryption algorithm, SM4 is a symmetric encryption algorithm, SM3 is a Hash algorithm (security, efficiency, and SHA-256 are equivalent, but more appropriate for domestic applications).
- Bcrypt (Class Hash algorithm): Hash algorithm based on Blowfish encryption algorithm, specially designed for password encryption, high security, slow Hash algorithm.
- MAC (Message Information Code, Message Authentication Code algorithm): HMAC is a MAC based on Hash and can be used in conjunction with any safe HASH algorithm, such as SHA-256.
- CRC: (Cyclic Redundancy Check, Circulation Redundancy Check): CRC32 is a CRC algorithm that is characterized by the generation of 32-bit validation values, usually used for data integrity verification, document validation, etc.
- SipHash: Encrypted Hash algorithm, designed to strike a balance between speed and safety and to defend against [Hash DoS attack](https://aumasson.jp/sipash/sipashdos_29c3_slides.pdf). Rust defaults to use SipHash as a Hash algorithm, starting with Redis 4
