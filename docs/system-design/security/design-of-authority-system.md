I don't...
title: Permission system design details
Category: System design
Tag:

- Clear.
  Head:
- Meta.
- Name: keywords
  Content: Permission System Design, RBAC, ABAC
- Meta.
- Name: description
  Content: Role-based access control (Role-Based Access Control, known as RBAC) refers to a flexible access control that is simpler, more efficient, and more scalable than the direct granting of user privileges by the user's role (Role).
  I don't...

@include: @article-header.snippet.md--

> Author: Switching Technology Team

Original language: <https://mp.weixin.qq.com/s/ONMUELjdHYAOYQceTj01Iw>

# Problem and status of the old permission system

In the past, there had not been a single system of delegation of authority, which had been developed by each operation on its own or in other operations, and the lack of uniformity in the management of authority had caused many problems:

1. Duplication of wheeling operations and high maintenance costs
1. Systems address only part of the scenery, the options are not common enough, and there are no reliable competency management options for new project types
1. Lack of a unified log management and approval process and difficulties in tracking delegation of authority information

On the basis of the above, at the end of last year, the company launched the development of a system for the transfer of unified authority, with the objective of developing a flexible, user-friendly, and secure system of delegated authority for use by its operations.

# The way the system of industry authority is designed

At present, there are two models of competence for the mainstream industry, as described below:

- **Role-based access control (RBAC)**
- **Attribute-based access control (ABAC)**

# RBAC Model

**Role-based access control (Role-Based Access Control, known as RBAC)** refers to flexible access control that is simpler, more efficient, and more scalable than the direct granting of user access by the user's role (Role).

A user can have a number of roles, and each role can be assigned a number of privileges, thus creating a “user-role-authority” authorization model. In this model, there are multiple-to-multiple relationships between users and roles, and roles and privileges.

A graph depicts the following:

[RBAC Permission Model Map](https://oss.javaguide.cn/github/javaguide/system-design/security/design-of-autocity-system/rbac.png)

When using the `RBAC model`, users are given different roles by analyzing their actual situation, based on common responsibilities and needs. This relationship between the `user -> role -> permissions` allows us to no longer separately manage individual user privileges, which users need from the assigned role.

In the case of a simple scene (GitLab's permission system), the user system has three roles: `Admin`, `Maintainer`, and `Operator`, which have different competencies, such as only `Admin` has the right to create a code repository, remove the code repository, and no one else. We gave a user the role of `Admin`, and he had both permissions **to create the code repository** and **to delete the code repository**.

Through the `RBAC Model`, when there are multiple users with the same privileges, we simply need to create a role with the same privileges and then assign different roles to different users, and then we need only to modify the role's privileges to automatically modify the privileges of all users within the role.

## ABAC Model

**Attribute-based access control (ABAC)** is a more flexible enabling model than the `RBAC model`, which is based on the dynamic determination of whether an operation can be allowed through a variety of attributes. This model is more used in cloud systems, such as AWS, Aliyun, etc.

Consider the control of the following scenarios:

1. Authorizing the editorial authority of a person for a specific book
1. Users have access to a document when it is owned by the same department as the user.
1. When the user is the owner of a document and the document is in draft form, the user can edit the document
1. Ban access to the B system by 9 a.m.
1. Prohibition of access to A systems as administrators in places other than Shanghai
1. User's permission to operate on orders created before 2022-06-07

The above scenario can be found to be difficult to achieve through the `RBAC model`, since the `RBAC model` simply describes what the user can do, but the conditions of the operation and the data of the operation, the `RBAC model` itself, do not have these limitations. But this is precisely the strength of the `ABAC Model`, whose idea is to dynamically calculate whether the user has the permission to operate on the basis of the user, the attributes of the data accessed, and environmental factors.

### The principles of the ABAC model

In the `ABAC model`, whether an operation is
