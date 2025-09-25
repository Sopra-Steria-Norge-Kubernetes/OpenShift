# Quay

## What is quay?
Quay is a secure and scalable container registry for storing and managing container images. 

## Quay Structure
There are severral components in quay

![](../img/quay/quaystructure.png)

* **Repositories** – Locations where container images are stored.
* **Organizations** – Groups of repositories that make management easier.
* **Teams** – Collections of users with specific permissions within an organization.
* **Robot Accounts** – Automated accounts used for CI/CD pipelines or other automated processes


There are mainly 2 ways to configure Quay to suit your needs: 

##### 1. Every organization is created as a team with a single team inside that organization.

The advantage of this approach is full isolation between teams: each team or project has its own organization, and other teams cannot see each other unless access is given. Since this setup is the most enclosed it reduces the risk of accidental access, since teams cannot se each other. 

##### 2. Have only one organization and create several teams within it.

Having a single organization simplifies management, since there is only one organization to maintain. It also makes collaboration easier, as teams within the same organization can share repositories and resources easier.
