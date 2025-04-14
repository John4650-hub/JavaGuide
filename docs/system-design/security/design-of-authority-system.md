---
title: Detailed Design of Permission System
category: System Design
tag:
  - Security
head:
  -   - meta
      - name: keywords
        content: Permission System Design, RBAC, ABAC
  -   - meta
      - name: description
        content: Role-Based Access Control (RBAC) refers to the authorization of related permissions to users based on their roles, achieving flexible access control, which is simpler, more efficient, and scalable compared to directly granting permissions to users.
---

<!-- @include: @article-header.snippet.md -->

> Author: Zhuanzhuan Technical Team
>
> Original: <https://mp.weixin.qq.com/s/ONMuELjdHYa0yQceTj01Iw>

## Problems and Current Status of Old Permission System

In the past, Zhuanzhuan did not have a unified permission management system. Permission management was developed by each business independently or used the permission system of other businesses. The lack of uniformity in permission management led to several issues:

1. Each business reinventing the wheel, leading to high maintenance costs.
1. Each system only solving partial scene problems; the solutions are not general enough, resulting in a lack of reliable permission management solutions during the selection of new projects.
1. A lack of unified log management and approval processes, making it very difficult to trace authorization information.

Based on the above issues, at the end of last year, the company initiated the construction of a unified permission system aimed at developing a flexible, easy-to-use, and secure permission management system for various businesses.

## Design Methods of Industry Permission Systems

Currently, there are two mainstream permission models in the industry, which are introduced below:

- **Role-Based Access Control (RBAC)**
- **Attribute-Based Access Control (ABAC)**

### RBAC Model

**Role-Based Access Control (RBAC)** refers to the authorization of related permissions to users based on their roles, achieving flexible access control that is simpler, more efficient, and scalable compared to directly granting permissions to users.

A user can have multiple roles, and each role can be assigned multiple permissions, thus constructing a “User-Role-Permission” authorization model. In this model, there is a many-to-many relationship between users and roles, and between roles and permissions.

This can be illustrated with a diagram:

![RBAC Permission Model Diagram](https://oss.javaguide.cn/github/javaguide/system-design/security/design-of-authority-system/rbac.png)

When using the `RBAC model`, different roles are assigned to users based on their actual situations, common responsibilities, and needs. This relationship of `User -> Role -> Permission` allows us to manage user permissions collectively through the roles assigned to them, without needing to manage individual user permissions separately.

For example, in a simple scenario (the permission system of GitLab), the user system has three roles: `Admin`, `Maintainer`, and `Operator`. These roles have different permissions; for instance, only `Admin` has the permissions to create and delete repositories, while others do not. If a user is granted the `Admin` role, they then have the **create repository** and **delete repository** permissions.

Through the `RBAC model`, when multiple users have the same permissions, we only need to create the roles with those permissions and assign different roles to different users. Subsequently, permissions can be modified simply by changing the permissions of the roles, automatically updating all users within that role.

### ABAC Model

**Attribute-Based Access Control (ABAC)** is a more flexible authorization model than the `RBAC model`. Its principle is to dynamically determine whether an operation can be allowed based on various attributes. This model is widely used in cloud systems, such as AWS and Alibaba Cloud.

Consider the following scenarios for permission control:

1. Authorizing a person with edit permission for a specific book.
1. Allowing a user to access a document when the document's department matches the user's department.
1. Allowing a user to edit a document if they are the owner and the document's status is in draft.
1. Prohibiting access to System B for Department A employees before 9 AM.
1. Prohibiting administrative access to System A from locations other than Shanghai.
1. Granting a user operational rights to orders created before 2022-06-07.

It can be seen that these scenarios are difficult to implement using the `RBAC model`. The `RBAC model` primarily describes what actions a user can take, but does not impose conditions or restrictions on the actions and associated data. This is the strength of the `ABAC model`, which calculates a user's permissions dynamically based on attributes of the user, the data being accessed, and various environmental factors.

#### Principles of the ABAC Model

In the `ABAC model`, whether an operation is allowed is dynamically calculated based on the object, resource, operation, and environmental information.

- **Object**: The object is the user requesting access to the resource. User attributes include ID, personal resources, roles, departments, and organizational membership.
- **Resource**: The resource is the asset or object the user wants to access, such as files, data, servers, or APIs.
- **Operation**: The operation is the action the user attempts to perform on the resource. Common operations include "read," "write," "edit," "copy," and "delete."
- **Environment**: The environment refers to the context of each access request. Environmental attributes include access time and location, the device of the object, communication protocols, and encryption strength.

During the execution of decision statements in the `ABAC model`, the decision engine dynamically calculates the result based on defined decision statements, along with factors like objects, resources, operations, and the environment. Whenever an access request occurs, the `ABAC model` decision system analyzes whether attribute values match established policies. If there are matching policies, the access request will be approved.

## Design Philosophy of the New Permission System

Considering Zhuanzhuan's business situation, the `RBAC model` meets most of Zhuanzhuan's business scenarios, and the development cost is far lower than that of the `ABAC model` permission system. Therefore, the new permission system chooses to implement the `RBAC model`. For business systems that cannot be met, we choose to temporarily not support them, ensuring a quick implementation of the new permission system, allowing businesses to use it faster.

The standard `RBAC model` strictly adheres to the `User -> Role -> Permission` chain, meaning a user's permissions are entirely controlled by the roles they have. However, this leads to a drawback where adding permissions to a user requires the creation of a new role, which reduces operational efficiency. Therefore, we have added the capability to directly assign permissions to users based on the `RBAC model`, meaning users can both be assigned roles and have permissions added directly. The final permissions for users are a combination of the roles held and the permissions directly assigned.

**Permission Model of the New Permission System**: User's final permissions = Permissions granted by the roles the user holds + Independently configured permissions by the user, taking the union of the two.

The new permission system solution is illustrated in the diagram below:

![New Permission System Solution](https://oss.javaguide.cn/github/javaguide/system-design/security/design-of-authority-system/new-authority-system-design.png)

- First, all users in the group (including external users) are uniformly managed through **unified login and registration** functionality, which is integrated with the company's organizational structure information module, ensuring consistency of information for the same personnel across all systems. This also provides feasibility for future permission management based on the organizational structure.
- Second, since the new permission system needs to serve all businesses in the group, it must support multi-system permission management. Before managing permissions, users need to select the corresponding system and configure the **menu permissions** and **data permissions** for that system, establishing the various permission points of the system. _PS: The specific description of menu permissions and data permissions will be detailed later._
- Finally, different roles under the system are created, and permission points are configured for each role. For instance, the store manager role has operational permissions and data view permissions for their store. After configuring this role, simply granting this role to the store manager will provide them with the corresponding permissions.

Once the above configuration is completed, user permissions management can proceed. There are two ways to assign permissions to users:

1. First select the user, then add permissions. This method allows any role or menu/data permission points to be added.
1. First select the role, then associate the user. This method allows only roles to be added to users, without the capability to add menu/data permission points individually.

The specific design proposals for these two methods will be explained in detail later.

### Permission Management of the Permission System Itself

For the permission system, it is essential to design its own permission management, which involves managing "who can enter the permission system and who can manage permissions for other systems." The users of the permission system itself are categorized into three types:

1. **Super Administrator**: Holds all operational permissions of the permission system, can perform any operations on the system, and manage the applications connected to it.
1. **Permission Operation User**: A user who has the super administrator role for at least one connected application system. The operations allowed for this user are limited to the permissions of the application systems they own. Permission operation users are identities that need not be assigned but are automatically obtained based on rules.
1. **Ordinary Users**: Ordinary users can be considered an identity type excluding the above two categories; the rest are ordinary users. They can only request access to systems and view the permission application page.

### Definition of Permission Types

In the new permission system, we categorize permissions into two major types:

- **Menu Function Permissions**: Includes the navigation directory of the system, menu access permissions, and button and API operation permissions.
- **Data Permissions**: Includes defining the scope of data query permissions, commonly referred to as "organization" or "site" in different systems. In the new permission system, these are collectively referred to as "organization" for managing data permissions.

### Classification of Default Roles

Each system is designed with three default roles to satisfy basic permission management needs, as follows:

- **Super Administrator**: This role has all permissions in the system, can modify role permissions and other configurations, and can grant permissions to other users.
- **System Administrator**: This role has the ability to grant permissions to other users and modify system role permissions but does not possess any permissions itself.
- **Authorization Administrator**: This role can grant permissions to other users but cannot exceed the permissions already held by themselves.

> For example: Authorization Administrator A can grant permissions to User B, but the permissions granted cannot exceed those held by User A.

By making this distinction, we can separate **possessing permissions** from **having authorization capabilities**, thereby accommodating all scenarios of permission control.

## Core Module Design of the New Permission System

The overall design philosophy and modules of the new permission system have been introduced. The new system is widely used internally at Zhuanzhuan, and permission management has become much more convenient compared to before. A flexible and comprehensive design for permission systems, being a foundational system for every company, can significantly enhance future business development efficiency.

Upcoming two articles:

- [Design and Implementation of Zhuanzhuan Unified Permission System (Backend Implementation)](https://mp.weixin.qq.com/s/hFTDckfxhSnoM_McP18Vkg)
- [Design and Implementation of Zhuanzhuan Unified Permission System (Frontend Implementation)](https://mp.weixin.qq.com/s/a_P4JAwxgunhfmJvpBnWYA)

## References

- Choosing the Right Permission Model: <https://docs.authing.cn/v2/guides/access-control/choose-the-right-access-control-model.html>

<!-- @include: @article-footer.snippet.md -->
