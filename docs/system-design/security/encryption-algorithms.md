---
title: Summary of Common Encryption Algorithms
category: System Design
tag:
  - Security
---

Encryption algorithms are mathematical methods used to transform data with the aim of protecting its security and preventing unauthorized individuals from reading or altering it. Encryption algorithms can be divided into three main categories: symmetric encryption algorithms, asymmetric encryption algorithms, and hash algorithms (also known as digest algorithms).

Common scenarios in daily development where encryption algorithms are needed include:

1. Passwords stored in databases need to be salted and then encrypted using hash algorithms (such as BCrypt).
1. Sensitive data such as bank card numbers and ID numbers stored in databases should be saved using symmetric encryption algorithms (such as AES).
1. Sensitive data transmitted over the network, such as bank card numbers and ID numbers, needs to be secured using HTTPS and asymmetric encryption algorithms (like RSA) to ensure the safety of the transmitted data.
1. ……

P.S.: Strictly speaking, hash algorithms do not belong to encryption algorithms; they can only be used in certain encryption scenarios (for example, password encryption). The two can be seen as parallel. Encryption algorithms typically refer to those that can convert plaintext into ciphertext and can subsequently restore the ciphertext back to plaintext through a method (such as a key). Hash algorithms, on the other hand, are a one-way process that converts input data into a fixed-length, seemingly random hash value; this process is irreversible, meaning the original information cannot be restored from the hash value.

## Hash Algorithms

Hash algorithms are also called hash functions or digest algorithms. They generate a fixed-length unique identifier from data of any length, known as a hash value, hash code, or message digest (collectively referred to as hash value in the following text).

![Hash Algorithm Effect Demonstration](https://oss.javaguide.cn/github/javaguide/system-design/security/encryption-algorithms/hash-function-effect-demonstration.png)

Hash algorithms are irreversible; you cannot retrieve the original value from the hash result.

The purpose of a hash value is to verify the integrity and consistency of data.

Here are two practical examples:

- When saving a password to a database, you can check if the hash value of the user's input password matches the hash value stored in the database to determine if the password is correct.
- When downloading a file, you can compare the hash value of the file with the official hash value to check if the file has been tampered with or corrupted.

The characteristics of this algorithm include irreversibility:

- You cannot restore the original data from the hash value.
- Any change to the original data will result in a significant change in the hash value.

Hash algorithms can be simply divided into two categories:

1. **Cryptographic Hash Algorithms**: These are hash algorithms with higher security that provide certain data integrity protection and data tampering resistance. They can withstand certain types of attacks, but their performance can be poor, making them suitable for scenarios requiring high security. Examples include SHA2, SHA3, SM3, RIPEMD-160, BLAKE2, SipHash, etc.
1. **Non-Cryptographic Hash Algorithms**: These hash algorithms have relatively lower security and are more susceptible to brute-force attacks and collision attacks, but their performance is higher and they are suitable for business scenarios where security is not a requirement, such as CRC32, MurMurHash3, SipHash, etc.

In addition to these two categories, there are some special hash algorithms, such as **slow hash algorithms** with higher security.

Common hash algorithms include:

- MD (Message Digest, message digest algorithm): MD2, MD4, MD5, etc., which are no longer recommended for use.
- SHA (Secure Hash Algorithm, secure hash algorithm): SHA-1 series has lower security, while SHA2 and SHA3 series have higher security.
- National encryption algorithms: such as SM2, SM3, SM4, where SM2 is an asymmetric encryption algorithm, SM4 is a symmetric encryption algorithm, and SM3 is a hash algorithm (security and efficiency comparable to SHA-256 but more suitable for domestic applications).
- Bcrypt (password hash algorithm): A password hash algorithm based on the Blowfish encryption algorithm, designed specifically for password encryption, which offers high security and belongs to the slow hash algorithms.
- MAC (Message Authentication Code, message authentication code algorithm): HMAC is a type of MAC based on hash that can be used in conjunction with any secure hash algorithm like SHA-256.
- CRC: (Cyclic Redundancy Check): CRC32 is a CRC algorithm characterized by the generation of a 32-bit checksum, commonly used in data integrity checking, file verification, etc.
- SipHash: is a cryptographic hash algorithm designed to achieve a balance between speed and security, used to defend against [hash flooding DoS attacks](https://aumasson.jp/siphash/siphashdos_29c3_slides.pdf). Rust uses SipHash as the default hash algorithm, replacing the hash algorithm with SipHash starting from Redis 4.0.
- MurMurHash: A classic fast non-cryptographic hash algorithm, with the latest version being MurMurHash3, which can generate 32-bit or 128-bit hash values;
- ……

Hash algorithms generally do not require keys, but there are some special hash algorithms that do. For example, MAC and SipHash are key-based hash algorithms that add a key to the hash algorithm, such that only individuals who know the key can verify the data's integrity and origin.

### MD

The MD algorithm consists of several versions, including MD2, MD4, MD5, etc., with MD5 being the most commonly used. From a security standpoint: MD5 > MD4 > MD2. In addition to these versions, there are also some algorithms based on improvements to MD4 or MD5, such as RIPEMD, HAVAL, etc.

Even the safest MD algorithm, MD5, carries the risk of being compromised. Attackers may find a hash value matching the original data through brute-force approaches or rainbow table attacks.

To increase the difficulty of cracking, you can choose to add salt. Salt in cryptography refers to inserting a specific string at a fixed position in the password, making the hash result different from using the original password’s hash; this process is called "salting."

Is it safe after salting? Not necessarily. This simply increases the difficulty of cracking, not guaranteeing that it is unbreakable. Additionally, the MD5 algorithm itself has a weak collision problem, whereby different inputs can produce the same MD5 value.

Thus, the MD algorithm is no longer recommended for use; it is advisable to use more secure hash algorithms like SHA-2 or Bcrypt.

Java provides support for the MD algorithm series, including MD2 and MD5.

MD5 code example (without salt):

```java
String originalString = "Java study + Interview guide: javaguide.cn";
// Create an MD5 digest object
MessageDigest messageDigest = MessageDigest.getInstance("MD5");
messageDigest.update(originalString.getBytes(StandardCharsets.UTF_8));
// Calculate hash value
byte[] result = messageDigest.digest();
// Convert hash value to hexadecimal string
String hexString = new HexBinaryAdapter().marshal(result);
System.out.println("Original String: " + originalString);
System.out.println("MD5 Hash: " + hexString.toLowerCase());
```

Output:

```bash
Original String: Java study + Interview guide: javaguide.cn
MD5 Hash: fb246796f5b1b60d4d0268c817c608fa
```

### SHA

The SHA (Secure Hash Algorithm) series consists of a group of cryptographic hash algorithms designed to map data of any length to a fixed-length hash value. The SHA series algorithms were designed by the National Security Agency (NSA) in 1993 and currently include three versions: SHA-1, SHA-2, and SHA-3.

The SHA-1 algorithm maps data of any length to a 160-bit hash value. However, SHA-1 has some significant deficiencies, such as low security and susceptibility to collision attacks and length extension attacks. Therefore, the SHA-1 algorithm is no longer recommended for use. The SHA-2 family (e.g., SHA-256, SHA-384, SHA-512) and the SHA-3 series are alternatives to SHA-1, offering higher security and longer hash values.

The SHA-2 family was improved based on the SHA-1 algorithm, utilizing more complex operations and multiple rounds, making it more challenging for attackers to find collisions through pre-computation or coincidence.

To seek a more secure and advanced cryptographic hash algorithm, the National Institute of Standards and Technology (NIST) publicly solicited candidate algorithms for SHA-3 in 2007. NIST received a total of 64 algorithm proposals, which underwent multiple rounds of evaluation and screening, ultimately announcing that the Keccak algorithm won in 2012, becoming the SHA-3 standard algorithm (SHA-3 is not directly related to the SHA-2 algorithm). The Keccak algorithm utilizes a substantially different design approach from MD and SHA-1/2, specifically a sponge structure, making traditional attack methods inapplicable to SHA-3 attacks (capable of resisting all known attack vectors, including collision attacks, length extension attacks, differential attacks, etc.).

Since the SHA-2 algorithm has not shown significant security vulnerabilities and exhibits higher efficiency in software, most people still prefer using the SHA-2 algorithm.

Compared to the MD5 algorithm, the two main reasons why SHA-2 is stronger are:

- Longer hash value length: For example, the SHA-256 algorithm's hash value length is 256 bits, while the MD5 algorithm's hash value length is 128 bits, thereby increasing the difficulty for attackers in conducting brute-force or rainbow table attacks.
- Stronger collision resistance: The SHA algorithm employs more complex operations and more rounds, making it difficult for attackers to find collisions through pre-computation or coincidence. There are currently no known examples of two different data producing the same SHA-256 hash.

Of course, SHA-2 is not absolutely secure and also carries risks of brute-force attacks or rainbow table attacks; thus, in practical applications, salting is still essential.

Java provides support for the SHA algorithm series, including SHA-1, SHA-256, SHA-384, and SHA-512.

SHA-256 code example (without salt):

```java
String originalString = "Java study + Interview guide: javaguide.cn";
// Create a SHA-256 digest object
MessageDigest messageDigest = MessageDigest.getInstance("SHA-256");
messageDigest.update(originalString.getBytes());
// Calculate hash value
byte[] result = messageDigest.digest();
// Convert hash value to hexadecimal string
String hexString = new HexBinaryAdapter().marshal(result);
System.out.println("Original String: " + originalString);
System.out.println("SHA-256 Hash: " + hexString.toLowerCase());
```

Output:

```bash
Original String: Java study + Interview guide: javaguide.cn
SHA-256 Hash: 184eb7e1d7fb002444098c9bde3403c6f6722c93ecfac242c0e35cd9ed3b41cd
```

### Bcrypt

Bcrypt is a password hash algorithm based on the Blowfish encryption algorithm, specifically designed for password encryption, offering high security.

Bcrypt effectively prevents rainbow table attacks and brute-force attacks, ensuring password security through two mechanisms: salt (a randomly generated string used to mix with the password, increasing its complexity and uniqueness) and cost (a numeric parameter controlling the number of iterations in the Bcrypt algorithm, increasing the time and resource consumption for password hashing).

The complexity of encryption can be adjusted according to actual circumstances, allowing different cost and salt values to meet varied security requirements, providing high flexibility.

The Spring Security framework of Java applications supports various password encoders, among which the `BCryptPasswordEncoder` is recommended for encrypting and storing user passwords.

```java
@Bean
public PasswordEncoder passwordEncoder(){
    return new BCryptPasswordEncoder();
}
```

## Symmetric Encryption

Symmetric encryption algorithms refer to those that use the same key for encryption and decryption, also known as shared key encryption algorithms.

![Symmetric Encryption](https://oss.javaguide.cn/github/javaguide/system-design/security/encryption-algorithms/symmetric-encryption.png)

Common symmetric encryption algorithms include DES, 3DES, AES, etc.

### DES and 3DES

DES (Data Encryption Standard) uses a 64-bit key (with an effective key length of 56 bits and 8 bits for parity) and a 64-bit plaintext for encryption.

Although DES can only encrypt 64 bits at a time, you can divide plaintext into blocks of 64 bits to encrypt data of any length. If the plaintext length is not a multiple of 64 bits, padding must be performed; common padding modes include PKCS5Padding, PKCS7Padding, and NOPADDING.

The basic idea of the DES encryption algorithm is to divide the 64-bit plaintext into two halves, then undergo multiple rounds of transformations on each half, and finally merge them into a 64-bit ciphertext. These transformations include permutation, XOR, selection, shift operations, etc., with each round using a sub-key generated from the same 56-bit master key. The DES encryption algorithm undergoes 16 rounds of transformation before a final inverse permutation yields the final ciphertext.

![DES (Data Encryption Standard)](https://oss.javaguide.cn/github/javaguide/system-design/security/des-steps.jpg)

This is a classic symmetric encryption algorithm, but it has obvious weaknesses; the 56-bit key security is insufficient and has been demonstrated to be crackable in a short period.

In order to improve the security of the DES algorithm, variations or alternatives were proposed, such as 3DES (Triple DES).

3DES (Triple DES) is an encryption algorithm transitioning from DES to AES. It uses 2 or 3 56-bit keys to encrypt data three times. 3DES effectively applies the symmetric encryption algorithm three times to each data block.

To ensure compatibility with standard DES, 3DES does not directly employ the method of encrypt-encrypt-encrypt; instead, it uses encrypt-decrypt-encrypt. When all three keys are the same, the first two steps cancel each other out, effectively resulting in a single encryption, ensuring compatibility with standard DES. 3DES is more secure than DES but has a slower processing speed.

### AES

AES (Advanced Encryption Standard) is a more advanced symmetric key encryption algorithm that uses keys of 128-bit, 192-bit, or 256-bit lengths for encrypting or decrypting data, with longer keys providing higher security.

AES is also a block cipher, where the block length must be 128 bits, meaning each block consists of 16 bytes. AES has various modes of operation, such as ECB, CBC, OFB, CFB, CTR, XTS, OCB, GCM (currently the most widely used mode). Different modes involve different parameters and encryption processes, but the core still relies on the AES algorithm.

Similar to DES, for plaintext that is not a multiple of 128 bits, padding is necessary; common padding modes include PKCS5Padding, PKCS7Padding, and NOPADDING. However, AES-GCM is a stream encryption algorithm capable of encrypting plaintext of any length, making its corresponding padding mode NoPadding, which means no padding is needed.

AES operates faster than 3DES and is also more secure.

![AES (Advanced Encryption Standard)](https://oss.javaguide.cn/github/javaguide/system-design/security/aes-steps.jpg)

Comparison of DES and AES algorithms (image from: [RSA vs. AES Encryption: Key Differences Explained](https://cheapsslweb.com/blog/rsa-vs-aes-encryption)):

![Comparison of DES and AES](https://oss.javaguide.cn/github/javaguide/system-design/security/des-vs-aes.png)

Java implementation of AES algorithm code example:

```java
private static final String AES_ALGORITHM = "AES";
// AES secret key
private static final String AES_SECRET_KEY = "4128D9CDAC7E2F82951CBAF7FDFE675B";
// AES encryption mode is GCM, padding is NoPadding
// AES-GCM is a stream cipher algorithm, thus its corresponding padding mode is NoPadding, meaning no padding is necessary.
private static final String AES_TRANSFORMATION = "AES/GCM/NoPadding";
// Encryptor
private static Cipher encryptionCipher;
// Decryptor
private static Cipher decryptionCipher;

/**
 * Initialize certain configurations
 */
public static void init() throws Exception {
    // Convert the AES key into a SecretKeySpec object
    SecretKeySpec secretKeySpec = new SecretKeySpec(AES_SECRET_KEY.getBytes(), AES_ALGORITHM);
    // Get the appropriate encryptor instance for the specified AES encryption mode and padding, and initialize it
    encryptionCipher = Cipher.getInstance(AES_TRANSFORMATION);
    encryptionCipher.init(Cipher.ENCRYPT_MODE, secretKeySpec);
    // Get the appropriate decryptor instance for the specified AES encryption mode and padding, and initialize it
    decryptionCipher = Cipher.getInstance(AES_TRANSFORMATION);
    decryptionCipher.init(Cipher.DECRYPT_MODE, secretKeySpec, new GCMParameterSpec(128, encryptionCipher.getIV()));
}

/**
 * Encrypt
 */
public static String encrypt(String data) throws Exception {
    byte[] dataInBytes = data.getBytes();
    // Encrypt data
    byte[] encryptedBytes = encryptionCipher.doFinal(dataInBytes);
    return Base64.getEncoder().encodeToString(encryptedBytes);
}

/**
 * Decrypt
 */
public static String decrypt(String encryptedData) throws Exception {
    byte[] dataInBytes = Base64.getDecoder().decode(encryptedData);
    // Decrypt data
    byte[] decryptedBytes = decryptionCipher.doFinal(dataInBytes);
    return new String(decryptedBytes, StandardCharsets.UTF_8);
}

public static void main(String[] args) throws Exception {
    String originalString = "Java study + Interview guide: javaguide.cn";
    init();
    String encryptedData = encrypt(originalString);
    String decryptedData = decrypt(encryptedData);
    System.out.println("Original String: " + originalString);
    System.out.println("AES Encrypted Data : " + encryptedData);
    System.out.println("AES Decrypted Data : " + decryptedData);
}
```

Output:

```bash
Original String: Java study + Interview guide: javaguide.cn
AES Encrypted Data : E1qTkK91suBqToag7WCyoFP9uK5hR1nSfM6p+oBlYj71bFiIVnk5TsQRT+zpjv8stha7oyKi3jQ=
AES Decrypted Data : Java study + Interview guide: javaguide.cn
```

## Asymmetric Encryption

Asymmetric encryption algorithms use different keys for encryption and decryption, also known as public key encryption algorithms. These two keys are distinct: one is called the public key, and the other is called the private key. The public key can be shared with anyone, while the private key must be kept confidential.

If data is encrypted using the public key, it can only be decrypted with the corresponding private key (encryption); if data is encrypted using the private key, it can only be decrypted using the corresponding public key (signature). This enables secure data transmission and identity verification.

![Asymmetric Encryption](https://oss.javaguide.cn/github/javaguide/system-design/security/encryption-algorithms/asymmetric-encryption.png)

Common asymmetric encryption algorithms include RSA, DSA, ECC, etc.

### RSA

RSA (Rivest–Shamir–Adleman algorithm) is an asymmetric encryption algorithm based on the difficulty of large number factorization. It requires selecting two large prime numbers as part of the private key, then computing their product as part of the public key (finding two large prime numbers is relatively simple, but factoring their product is extremely difficult). For a detailed introduction to the principles of RSA, you can refer to this article: [Do You Really Understand the RSA Encryption Algorithm? - Xiaofuge](https://www.cnblogs.com/xiaofuge/p/16954187.html).

The security of the RSA algorithm depends on the difficulty of large number factorization; it is recommended to use a key length of 2048 bits or more since keys of 512 bits and 768 bits have already been successfully factored.

The strengths of RSA lie in its simplicity and usability for both data encryption and digital signatures; however, its weaknesses include slower operational speed, making it unsuitable for encrypting large amounts of data.

RSA is currently the most widely used asymmetric encryption algorithm, utilized in protocols like SSL/TLS and SSH.

![HTTPS Certificate Signing Algorithm with RSA and SHA-256](https://oss.javaguide.cn/github/javaguide/system-design/security/encryption-algorithms/https-rsa-sha-256.png)

Java implementation of RSA algorithm code example:

```java
private static final String RSA_ALGORITHM = "RSA";

/**
 * Generate RSA key pair
 */
public static KeyPair generateKeyPair() throws NoSuchAlgorithmException {
    KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance(RSA_ALGORITHM);
    // Key size of 2048 bits
    keyPairGenerator.initialize(2048);
    return keyPairGenerator.generateKeyPair();
}

/**
 * Encrypt data using the public key
 */
public static String encrypt(String data, PublicKey publicKey) throws Exception {
    Cipher cipher = Cipher.getInstance(RSA_ALGORITHM);
    cipher.init(Cipher.ENCRYPT_MODE, publicKey);
    byte[] encryptedData = cipher.doFinal(data.getBytes(StandardCharsets.UTF_8));
    return Base64.getEncoder().encodeToString(encryptedData);
}

/**
 * Decrypt data using the private key
 */
public static String decrypt(String encryptedData, PrivateKey privateKey) throws Exception {
    byte[] decodedData = Base64.getDecoder().decode(encryptedData);
    Cipher cipher = Cipher.getInstance(RSA_ALGORITHM);
    cipher.init(Cipher.DECRYPT_MODE, privateKey);
    byte[] decryptedData = cipher.doFinal(decodedData);
    return new String(decryptedData, StandardCharsets.UTF_8);
}

public static void main(String[] args) throws Exception {
    KeyPair keyPair = generateKeyPair();
    PublicKey publicKey = keyPair.getPublic();
    PrivateKey privateKey = keyPair.getPrivate();
    String originalString = "Java study + Interview guide: javaguide.cn";
    String encryptedData = encrypt(originalString, publicKey);
    String decryptedData = decrypt(encryptedData, privateKey);
    System.out.println("Original String: " + originalString);
    System.out.println("RSA Encrypted Data : " + encryptedData);
    System.out.println("RSA Decrypted Data : " + decryptedData);
}
```

Output:

```bash
Original String: Java study + Interview guide: javaguide.cn
RSA Encrypted Data : T9ey/CEPUAhZm4UJjuVNIg8RPd1fQ32S9w6+rvOKxmuMumkJY2daFfWuCn8A73Mk5bL6TigOJI0GHfKOt/W2x968qLM3pBGCcPX17n4pR43f32IIIz9iPdgF/INOqDxP5ZAtCDvTiuzcSgDHXqiBSK5TDjtj7xoGjfudYAXICa8pWitnqDgJYoo2J0F8mKzxoi8D8eLE455MEx8ZT1s7FUD/z7/H8CfShLRbO9zq/zFI06TXn123ufg+F4lDaq/5jaIxGVEUB/NFeX4N6OZCFHtAV32mw71BYUadzI9TgvkkUr1rSKmQ0icNhnRdKedJokGUh8g9QQ768KERu92Ibg==
RSA Decrypted Data : Java study + Interview guide: javaguide.cn
```

### DSA

DSA (Digital Signature Algorithm) is an asymmetric encryption algorithm based on the difficulty of discrete logarithms, requiring the selection of a prime number q and a multiple p of q as part of the private key. It then computes a primitive root modulo p, g, and an integer modulo q, y, as part of the public key. The security of the DSA algorithm relies on the difficulty of discrete logarithms; it is currently recommended to use key lengths of 2048 bits or more as 1024-bit DSA public keys have been successfully broken.

The strengths of DSA include faster digital signature generation, making it suitable for creating digital certificates; the weaknesses include its unsuitability for data encryption and the requirement of using random numbers during the signing process.

The DSA algorithm signing process is as follows:

1. Use a message digest algorithm to hash the data to be sent, generating a message digest, which is a short, unique, irreversible representation of the data.
1. The sender encrypts the message digest with their DSA private key, creating a digital signature, which serves as proof of data origin and integrity.
1. The original data and digital signature are transmitted over the internet to the receiver.
1. The receiver decrypts the digital signature using the sender's public key to retrieve the message digest. Additionally, the receiver hashes the received original data to create another message digest. The receiver compares both message digests; if they match, the data has remained unaltered; otherwise, it indicates a loss of security and confidentiality.

![DSA Algorithm Signing Process](https://oss.javaguide.cn/github/javaguide/system-design/security/encryption-algorithms/dsa-algorithm-signing-process.png)

## Conclusion

This article introduced three types of encryption algorithms: hash algorithms, symmetric encryption algorithms, and asymmetric encryption algorithms.

- Hash algorithms use mathematical methods to generate a fixed-length unique identifier for data, verifying its integrity and consistency. Common hash algorithms include MD, SHA, MAC, etc.
- Symmetric encryption algorithms use the same key for encryption and decryption to protect data's security and confidentiality. Common symmetric encryption algorithms include DES, 3DES, AES, etc.
- Asymmetric encryption algorithms use different keys for encryption and decryption to enable secure data transmission and identity verification. Common asymmetric encryption algorithms include RSA, DSA, ECC, etc.

## References

- Deep Understanding of Perfect Hashing - Tencent Technology Engineering: <https://mp.weixin.qq.com/s/M8Wcj8sZ7UF1CMr887Puog>
- Practical Cryptography for Developers (Part II) - Hash Functions: <https://thiscute.world/posts/practical-cryptography-basics-2-hash/>
- The Wonderful Security Journey of DSA Algorithm: <https://zhuanlan.zhihu.com/p/347025157>
- Introduction to AES-GCM Encryption: <https://juejin.cn/post/6844904122676690951>
- Java AES 256 GCM Encryption and Decryption Example | JCE Unlimited Strength: <https://www.javainterviewpoint.com/java-aes-256-gcm-encryption-and-decryption/>

<!-- @include: @article-footer.snippet.md -->
