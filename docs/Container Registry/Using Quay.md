# Using Quay

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
