---
title: Detailed Explanation of Data Hot and Cold Separation
category: High Performance
head:
  -   - meta
      - name: keywords
        content: Data hot and cold separation, cold data migration, cold data storage
  -   - meta
      - name: description
        content: Data hot and cold separation refers to dividing data into cold and hot data based on access frequency and business importance. Cold data is generally stored in low-cost, low-performance media, while hot data is stored in high-performance media.
---

## What is Data Hot and Cold Separation?

Data hot and cold separation refers to dividing data into cold and hot data based on access frequency and business importance. Cold data is generally stored in low-cost, low-performance media, while hot data is stored in high-performance media.

### Cold Data and Hot Data

Hot data refers to data that is frequently accessed and modified and requires quick access, while cold data refers to data that is infrequently accessed, has lower current project value, but needs to be stored for the long term.

How do we distinguish between hot and cold data? There are two common methods of distinction:

1. **Time Dimension Distinction**: Data is classified as hot or cold based on its creation time, update time, expiration time, etc. Data within a certain time frame is considered hot, while data beyond that time frame is considered cold. For example, an order system might classify order data from one year ago as cold data and order data within the last year as hot data. This method is suitable for scenarios where access frequency is strongly correlated with time.
1. **Access Frequency Distinction**: Data that is accessed frequently is considered hot, while data that is accessed infrequently is considered cold. For example, a content system might classify articles with very low views as cold data and articles with higher views as hot data. This method requires tracking data access frequency, which can be costly, and is suitable for scenarios where access frequency is strongly correlated with the data itself.

Data from a few years ago is not necessarily all cold data; for instance, some high-quality articles may still receive many visits years after publication, while most newly published articles by ordinary users may hardly be accessed.

Both methods of distinguishing hot and cold data have their pros and cons, and in actual projects, they can be used in combination.

### The Concept of Hot and Cold Separation

The concept of hot and cold separation is quite simple: classify data and then store it separately. This concept can be applied in many fields and scenarios, not just data storage, such as:

- In an email system, important recent emails can be placed in the inbox, while less important older emails can be archived.
- In daily life, frequently used items can be placed in prominent locations, while less frequently used items can be stored in a storage room or attic.
- In a library, the most popular and frequently borrowed books can be placed in a prominent area, while less borrowed books can be placed in less noticeable locations.
- ……

### Advantages and Disadvantages of Data Hot and Cold Separation

- Advantages: The query performance of hot data is optimized (the user experience for most operations will be better), and costs are saved (different storage requirements for hot and cold data allow for the selection of appropriate database types and hardware configurations, such as placing hot data on SSDs and cold data on HDDs).
- Disadvantages: Increased system complexity and risk (the need to separate hot and cold data increases the risk of data errors), and lower statistical efficiency (cold storage data may be needed for statistics).

## How to Migrate Cold Data?

Cold data migration solutions:

1. **Business Layer Code Implementation**: When there is a write operation on the data, trigger the logic for hot and cold separation to determine whether the data is cold or hot. Cold data goes to cold storage, while hot data goes to hot storage. This solution can impact performance, and the logic for determining hot and cold data can be difficult to establish, plus it requires modifying business layer code, so it is generally not used.
1. **Task Scheduling**: You can use xxl-job or other distributed task scheduling platforms to periodically scan the database, identify data that meets the cold data criteria, and then batch copy it to cold storage while deleting it from hot storage. This method requires very little code modification and is very suitable for scenarios where cold and hot data are distinguished by time.
1. **Listening to Database Change Logs (binlog)**: Extract data that meets the cold data criteria from the binlog, then copy it to cold storage and delete it from hot storage. This method does not require code modification but is not suitable for scenarios where hot and cold data are distinguished by time.

If your company has a DBA, you can also have the DBA perform manual migration of cold data to cold storage in one go. Then, you can use the solutions mentioned above to implement subsequent cold data migration work.

## How to Store Cold Data?

The storage requirements for cold data mainly include large capacity, low cost, high
