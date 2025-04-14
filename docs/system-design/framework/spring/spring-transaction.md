---
title: Spring Transaction Detailed Explanation
category: Framework
tag:
  - Spring
---

The **Spring Transaction** analysis summary that I promised to readers some time ago is finally here. This part is quite important, whether for work or interviews, but there are relatively few good reference materials available online.

## What is a Transaction?

**A transaction is a logical group of operations that either all execute or none execute.**

I believe everyone can recite this sentence by now. Below, I will discuss it in the context of our daily development.

Each business method in our system may include multiple atomic database operations. For example, the `savePerson()` method below contains two atomic database operations. These atomic database operations are dependent; they either all execute or none execute.

```java
  public void savePerson() {
    personDao.save(person);
    personDetailDao.save(personDetail);
  }
```

Additionally, it is crucial to note: **Whether a transaction can take effect depends on whether the database engine supports transactions. For example, the commonly used MySQL database defaults to the transaction-supporting `innodb` engine. However, if the database engine is changed to `myisam`, then the program will no longer support transactions!**

The classic example often cited for transactions is money transfers. Suppose Xiao Ming wants to transfer 1000 yuan to Xiao Hong. This transfer involves two key operations:

> 1. Decrease Xiao Ming's balance by 1000 yuan.
> 1. Increase Xiao Hong's balance by 1000 yuan.

If an error suddenly occurs between these two operations, such as a bank system crash or network failure, resulting in Xiao Ming's balance being decreased while Xiao Hong's balance does not increase, that would be incorrect. A transaction ensures that these two key operations either both succeed or both fail.

![Transaction Diagram](https://oss.javaguide.cn/github/javaguide/mysql/%E4%BA%8B%E5%8A%A1%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

```java
public class OrdersService {
  private AccountDao accountDao;

  public void setOrdersDao(AccountDao accountDao) {
    this.accountDao = accountDao;
  }

  @Transactional(propagation = Propagation.REQUIRED,
                isolation = Isolation.DEFAULT, readOnly = false, timeout = -1)
  public void accountMoney() {
    // Xiao Hong's account increases by 1000
    accountDao.addMoney(1000, xiaohong);
    // Simulate a sudden exception, such as a power outage in the bank
    // If transaction management is not configured, Xiao Hong's account will increase by 1000 while Xiao Ming's account does not decrease
    int i = 10 / 0;
    // Xiao Wang's account decreases by 1000
    accountDao.reduceMoney(1000, xiaoming);
  }
}
```

Moreover, the four ACID properties of database transactions are fundamental to transactions. Let's briefly understand them.

## Do You Understand the Properties of Transactions (ACID)?

1. **Atomicity**: A transaction is the smallest unit of execution and cannot be divided. The atomicity of a transaction ensures that actions are either all completed or have no effect at all.
1. **Consistency**: Before and after executing a transaction, the data remains consistent. For example, in a transfer business, regardless of whether the transaction is successful, the total amount of the transferor and the payee should remain unchanged.
1. **Isolation**: When accessing the database concurrently, one user's transaction should not be interfered with by other transactions. Each concurrent transaction operates independently in the database.
1. **Durability**: Once a transaction is committed, the changes it makes to the data in the database are permanent, even in the event of a database failure.

🌈 Here, I want to add one more point: **Only by ensuring the durability, atomicity, and isolation of transactions can consistency be guaranteed. In other words, A, I, and D are means, while C is the goal!** I believe many of you, like me, have been misled by the concept of ACID for a long time! I only clarified this after watching Teacher Zhou Zhiming's public course [“Zhou Zhiming's Software Architecture Course”](https://time.geekbang.org/opencourse/intro/100064201) (read good books more!!!).

![AID->C](https://oss.javaguide.cn/github/javaguide/mysql/AID-%3EC.png)

Additionally, the author of [“Designing Data-Intensive Applications”](https://book.douban.com/subject/30329536/) states in this book:

> Atomicity, isolation, and durability are properties of the database, whereas consistency (in the ACID sense) is a property of the application. The application may rely on the database’s atomicity
