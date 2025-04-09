I don't...
title: Data cold thermal separation details\
Category: High performance\
Head:

- Meta.
- Name: keywords\
  Center: Cold thermal separation of data, cold data migration, cold data storage
- Meta.
- Name: description\
  CT: Data cold thermal separation refers to the classification of data into cold and thermal data based on the frequency of access and operational importance of the data, which are generally stored in low-cost, low-performance media and high-performance storage media for thermal data.\
  I don't...

# What's the cold-hot separation of data?

Data cold thermal separation refers to the classification of data into cold and thermal data based on the frequency of access and operational importance of data, which are generally stored in low-cost, low-performance media and high-performance storage media for thermal data.

## Cold and thermal

Thermal data refer to data that are frequently visited and modified and that require rapid access, while cold data refer to data that are not frequently visited and that are of low value to current projects but require long-term preservation.

How do you distinguish between cold and hot data? There are two common methods of distinction:

1. **Specific time dimension**: Data over a certain period of time are considered hot data, while data outside that period are considered cold data, depending on the time of creation, updating, expiry, etc. For example, the order system can use the order data from one year ago as cold data and the order data from the current year as hot data. This approach applies to scenarios where data access frequency and time are more relevant.
1. **Access frequency distinction**: Data accessed frequently are considered thermal, while data accessed infrequently are considered cold. For example, content systems can use articles with very low views as cold data and articles with higher views as hot data. This approach requires a high frequency of access to record data, which is more costly and suitable for scenarios where the frequency of access and the data themselves are more relevant.

Data from a few years ago were not necessarily cold; for example, some high-quality articles were still widely visited a few years after their publication, while most of the newly published articles by ordinary users were largely inaccessible.

The two methods of distinguishing cold and thermal data have their pros and cons and can be used in combination in actual projects.

# The cold, the cold, the separated mind

The idea of cold-hot separation is simple: to sort data and store it separately. The idea of cold-hot separation can be applied to many fields and scenarios rather than just data storage, for example:

- The mail system allows for the more recent and important mail to be placed in the inbox and the more long-standing and less important mail to be archived.
- In everyday life, usual items can be placed in a visible position, while unconventional items can be placed in storage or the attic.
- The library can place the most popular and commonly borrowed books in a single and visible area, leaving less borrowed books unobserved.
- ...

# The advantages and disadvantages of the cold-hot separation of data

- Advantages: Thermal data availability is optimized (the vast majority of users' operational experience will be better), cost-saving (the different storage needs for cold and thermal data, the choice of the corresponding database type and hardware configuration, e.g., thermal data on SSD and cold data on HDD).
- Shortcomings: System complexity and increased risk (requirement for separation of cold-hot data and increased risk of data error) and statistical inefficiency (requirement for cold data may be needed for statistical purposes).

# How does cold data migrate?

Cold data migration program:

1. Business-level code realization: When there is a writing operation for data, the logic triggers cold thermal separation, determining whether the data are cold or thermal. Cold data enters the cold storage, and thermal data enters the thermal storage. Such a program would affect performance, and the judgment logic of cold and hot data would not be well established and would require modifications to the business layer code, which would not normally be used.
1. Mission movement: Regular scanning of the database, using XXL-job or other distributed task control platforms, can be used to find data that meet the cold data conditions, which are then copied in bulk to the freezer and removed from the heater. The code changes in this method are very few and are well suited to distinguish between cold and hot data by time.
1. Change log for listening to database binlog: Extract data meeting cold data conditions from binlog and copy it to the freezer and remove it from the heater. This method allows for no code changes but is not suitable for distinguishing the scenes of cold and hot data by time dimension.

If your company has a DBA, you can also ask the DBA to migrate cold data manually to a cooling tank at a time. The program described above is then accompanied by the migration of subsequent cold data.

# How do you store cold data?

Storage requirements for cold data are mainly high-capacity, low-cost, high-reliability, and access speed can be appropriately sacrificed.

Cold data storage program:
