# Quay

Quay is a secure and scalable container registry for storing, building, and distributing container images. It integrates seamlessly with CI/CD pipelines and supports automated builds, vulnerability scanning, and fine-grained access control.

This guide walks you through the essentials of using Quay.


## Quay Structure

![](../img/quay/quaystructure.png)

At a high level, Quay is organized into:

- **Repositories** – Where container images are stored.
    
- **Organizations** – Groups of repositories for easier management.
    
- **Teams** – Collections of users with assigned permissions.
    

There are several ways to configure Quay to suit your needs. We see two main options you can move forward with:

1. Organize Quay so that each team or project has its own organization, with a single team inside that organization.

2. Use one organization and create several teams within it.

There is no right or wrong to doing this, but it’s important to have a consistent setup.


## Organization per Team/Project

### Pros

* **Clear isolation**: Each team/project has its own namespace, making permissions and resources easier to manage.
* **Simplified auditing**: Easier to track which users have access to which repositories.
* **Reduced risk of accidental access**: Teams cannot interfere with other teams’ repositories.

### Cons

* **More overhead**: Managing multiple organizations can be administratively heavier.
* **Complexity in cross-team collaboration**: Sharing images between projects may require additional configuration.
* **Increased maintenance**: Updates, quotas, and settings must be repeated per organization.

---

## One Organization, Several Teams

### Pros

* **Simplified management**: Only one organization to maintain
* **Easier collaboration**: Teams within the same organization can share repositories and resources more easily.
* **Reduced duplication**: Common repositories or images can be centrally managed.
* **Scalability**: Adding new teams doesn’t require creating a new organization.

### Cons

* **Potential for permission errors**: Misconfigured team permissions could allow access to repositories unintentionally.
* **Less isolation**: Teams share the same organization namespace, which may increase the risk of accidental changes.



## Tailoring your Quay Registry

### Getting Started

### 1. Log in to Quay

- Navigate to your Quay instance.
    
- Log in with your credentials.
    

After login, you’ll land on the **dashboard**.

The Quay web interface gives you access to:

- **Repositories** – Your own and shared ones.
    
- **Organizations** – Repository groups.
    
- **User Settings** – Manage tokens, linked accounts, and notifications.
    
- **Security** – Vulnerability reports for your images.
    

---

## Creating Organizations & Teams

### 1. Create an Organization

1. In the top navigation, click **New Organizations**.
    ![](../img/quay/createorg1.png)
2. Enter a name and description.
    ![](../img/quay/createorg2.png)
4. Click **Create**.
    

### 2. Create Teams

1. Inside your organization, go to **Teams**.
    
2. Click **Create Team**. ![alt text](../img/quay/createteam1.png)
    ![alt text](../img/quay/createteam2.png)
3. Name your team (e.g., `devs`, `ci-cd`, `admins`).

4. When assigning users this can be done in 2 ways. ![[../../Pasted image 20250922103317.png]]
	You could add them manually by assigning team members by email or username. Like you see in the lower right corner. The only prerequsite is that the user you want to add, has to have logged in once, so the user is created. 
    
4. Set their role:
    
    - **Admin** – Full control (settings, repositories, teams).
        
    - **Write** – Can push images.
        
    - **Read** – Can only pull images.
        

### Best Practices

- Apply **least-privilege access** with teams (e.g., give developers read access).



## Working with Repositories

### 1. Create a Repository

1. From the organization, create **New Repository**
![](../img/quay/orgoverview.png)
    
2. Enter a name and set visibility:
    
    - **Public** – Anyone can pull images.
        
    - **Private** – Restricted to authorized users/teams.
        
3. Click **Create Repository**.

### Best Practice
**Keep Repositories Private** – Unless images are meant for public use, prefer private repositories for security
