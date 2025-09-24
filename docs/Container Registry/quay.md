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


There are several ways to configure Quay to suit your need: 

1. Every organization is created as a team with a single team inside that organization.

2. Use one organization and create several teams within it.



## Organization per Team/Project

### Pros

* **Full isolation between teams**: Each team or project has its own organization. Other teams cannot see each other unless explicit access is given.
* **Reduced risk of accidental access**: Teams cannot interfere with other teams’ repositories.

### Cons

* **More overhead**: Managing multiple organizations can be administratively heavier.
* **Increased maintenance**: If changes is applied to an organization 


## One Organization, Several Teams

### Pros

* **Simplified management**: Only one organization to maintain
* **Easier collaboration**: Teams within the same organization can share repositories and resources more easily.

### Cons

* **Potential for permission errors**: Misconfigured team permissions could allow access to repositories unintentionally.
* **Less isolation**: Teams share the same organization namespace. You will be able to see each others team name, but will not be able to see content of that team and repository unless acssess is given.

---