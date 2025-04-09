I don't...
Title: Spring Detail
Category: Frame
Tag:

- Spring.
  I don't...

A few days ago, I promised my readers **Spring Service** that the analytical summary had finally arrived. This part is important, whether for work or for interviews, but there are fewer better reference materials available online.

What's the matter?

**The matter is a logical set of operations that are either implemented or not implemented.**

I am sure that you will all be able to carry on with this, and I will address this in the context of our daily real development.

Each of our system's business methods may include several atomic database operations, such as two of the `savePerson()` methods below. These atomic database operations are dependent, and they are either implemented or not.

```Java
public void savePerson() {
    personDao.save();
    // (a) Persons Detail Dao.save;
    // I'm sorry
}
```

In addition, particular attention needs to be paid to the following: **Whether or not the database engine is effective is critical. For example, the commonly used MySQL database defaults to the `innodb` engine for support services. But if the database engine is turned into `myisam`, then the program will no longer support the business!**

The classics of affairs are often cited as examples of transfers. If Ming was to transfer $1,000 to Red, it would involve two key operations:

> 1. Reduction of Ming's balance by $1,000.
> 1. Increase the balance of Little Red by $1,000.

In the event of a sudden error between the two operations, such as a collapse of the banking system or a network failure, which resulted in a reduction in the balance of Ming and no increase in the balance of Red, it would be inappropriate. The thing is to make sure that both of these critical operations succeed or fail.

[Domestic chart](https://oss.javaguide.cn/github/javaguide/mysql/%E4%BA%8B%25E%255%8A%A1%E7%A4%BA%E6%E4%8F%255%E5%9B%BE.png)

```Java
public class OrdersService {
    // I'm sorry.
    public void setOrdersDao(AccountDao accountDao) {
        this.accountDao = accountDao;
        // I'm sorry
    }

    @Transactional(propagation = Propagation.REQUIRED)
    public void transfer() {
        // Small red account plus 1000
        accountDao.addMoney(1000, xiaohong);
        // Simulations of sudden anomalies, such as possible power outages in banks, etc.
        // If there is no configuration management, there will be 1,000 more small red accounts and little money in small Ming accounts
        int i = 10 / 0; // Small Ming's account is 1,000 less
        accountDao.reduceMoney(1000, xiaming);
        // I'm sorry
    }
}
```

In addition, the ACID characteristics of database services are the basis of the services, which are briefly described below.

Do you understand?

1. **Atomic** (`Atomicity`): Matters are the smallest implementing units and division is not permitted. The atomic nature of the matter ensures that the action is either fully completed or completely ineffective;
1. **Consistency** (`Consistency`): Before, after, and after execution, consistent data, for example, in transfer operations, the total amount of the money transferer and payee should remain the same regardless of whether the transaction is successful or not;
1. **Isolation** (`Isolation`): When sent to and visited the database, one user's affairs are not interfered with by other firms and the database is independent of the one from the other;
1. **Durability** (`Durability`): After a matter has been submitted, it is a lasting change to the data in the database and should not affect it in any way, even if the database fails.

🌈 It would be useful to add here: \*\*Consistency can only be guaranteed if the durability, atomicity, and isolation of the matter are ensured. That means A, I, D is a means, C is an end! I'm sure you've been misled by the ACID concept for a long time! Zhou Ji Minh's Software Architecture Course\](https://time.geekbang.org/opencourse/intro/100064201) was only clear (thank you!!!).

[AID->C]

In addition, the author of DDIA, in his book ([https://book.douban.com/subject/30329536](https://book.douban.com/subject/30329536)), said:

> Atomicity, isolation, and durability are protocols of the database, whereas consistency (in the ACID sense) is a problem of the application.

Translation means: Atomicity, isolation, and durability are attributes of the database, while consistency (in the sense of ACID) is an attribute of the application
