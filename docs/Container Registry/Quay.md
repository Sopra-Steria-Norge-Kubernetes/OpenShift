# Quay

## What is quay?
Quay is a secure and scalable container registry for storing and managing container images. 
For more details, see the [official Quay documentation](https://docs.redhat.com/en/documentation/red_hat_quay/3.15/html/use_red_hat_quay/index).


## Quay Structure
There are severral components in quay

![](../img/quay/quaystructure.png)

* **Repositories** – Locations where container images are stored.
* **Organizations** – Groups of repositories that make management easier.
* **Teams** – Collections of users with specific permissions within an organization.
* **Robot Accounts** – Automated accounts used for CI/CD pipelines or other automated processes



## Structuring Quay for Your Organization
 
There are two primary approaches to structuring Quay, depending on the desired balance between isolation and collaboration:


##### 1. One Organization per Team
 
Each team or project is assigned its own dedicated organization, typically with a single team inside it. This model provides complete isolation of repositories and organizations. Teams cannot see each other’s repositories, teams, or organizations.
 
2. One Central Organization with Multiple Teams
 
A single organization is created, and multiple teams are defined within it. In this model, all teams are visible within the organization, but each team can have repositories that are completely isolated from other teams. Additionally, shared repositories can be defined to enable collaboration across



If you want to configure Quay with organizations and teams, check out our [Managing Quay](./Managing%20Quay.md) guide.