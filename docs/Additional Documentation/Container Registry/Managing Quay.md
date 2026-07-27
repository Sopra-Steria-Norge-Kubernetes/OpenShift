# Managing Quay

This guide covers how to create and manage organizations, teams, repositories, and robot accounts in Quay.

## Creating Organizations & Teams

### 1. Create an Organization

1. In the top navigation, click **New Organization**.
    ![](../../img/quay/createorg1.png)
2. Enter a name and description.
    ![](../../img/quay/createorg2.png)
3. Click **Create**.

### 2. Create a Team

1. Open your organization and go to **Teams and memberships**.

2. Click **Create Team**.
   ![](../../img/quay/createteam1.png)
   ![](../../img/quay/createteam2.png)

3. Give the team a name, for example `developer-team-1`, `ci-cd`, or `admins`.

4. Review the details and click **Finish**.

### Adding Team Members

There are two ways to add members to a team:

- **Manually** – Add members by email or username. The user must have logged in to Quay at least once before they can be added.
- **Via Azure** – Connect an Entra ID (Azure) group using its Object ID, and members are synced automatically.

#### Add members manually

1. Open your team from the organization's **Teams and memberships** page.
2. Add each member by their email or username.
3. Set a role for each member (see [Team Roles](#team-roles) below).

#### Add members through Azure

1. Open your team from the organization's **Teams and memberships** page.
    ![](../../img/quay/newuiorg.png)

2. Select the team you want to sync.
    ![](../../img/quay/newuiteamview.png)

3. Click **Enable Team Sync** and enter the Object ID of the group from Entra ID.

Once someone in the Entra ID group logs into Quay, they will only see the organization and team they belong to.

#### Team Roles

Each team has a role that defines what its members can do:

- **Admin** – Full administrative access to the organization, including the ability to create teams, add members, and set permissions.
- **Member** – Inherits all permissions set for the team.
- **Creator** – All member permissions, plus the ability to create new repositories.

To set a team's role:

1. On the Red Hat Quay landing page, click the name of your organization.
2. In the navigation pane, click **Teams and Membership**.
3. Select the **TEAM ROLE** drop-down menu, and choose the role you want to assign.

### Best Practices

- Follow the principle of **least privilege**. For example, give developers read access unless they need more.

## Working with Repositories

### 1. Create a Repository

1. From the organization, click **New Repository**.
    ![](../../img/quay/orgoverview.png)

2. Enter a name and set the visibility:
    - **Public** – Anyone can pull the images.
    - **Private** – Only authorized users and teams can access it.

3. Click **Create Repository**.

### 2. Add Teams or Members to a Repository

You manage repository access and permissions from the **Teams and membership** page of your organization.

1. Open your organization and go to **Teams and membership**.
2. Click the kebab menu next to a team, and choose one of the following:
    - **Manage Team Members** – View all members, robot accounts, and pending invitations. Click **Add new member** to add someone to the team.
    - **Set repository permissions** – Set the team's access to a repository.
    - **Delete** – Remove the team.

When setting repository permissions, choose one of the following levels:

- **None** – No access to the repository.
- **Read** – Can view and pull from the repository.
- **Write** – Can pull from and push to the repository.
- **Admin** – Full access to pull and push, plus administrative tasks for the repository.

### Best Practices

- **Keep repositories private.** Unless the images are meant for public use, private repositories are the safer default.

## Robot Accounts

Robot accounts are non-human credentials used to push and pull images from automated systems such as CI/CD pipelines. Each robot account belongs to an organization and can be given its own repository permissions.

### Create a Robot Account

1. In the top navigation, click **Organizations** and select your organization.
2. Open the **Robot accounts** tab and click **Create robot account**.
3. Enter a name, for example `robot1`. The full name combines your organization or username with the robot name, for example `quayadmin+robot1`.
4. (Optional) Add the robot to a team or repository, and adjust its permissions.
5. On the **Review and finish** page, review the details and click **Review and finish**.

### Retrieve Credentials from a Robot Account

When you need the robot's credentials, for example to authenticate a CI/CD pipeline or to log in from your local machine, click the robot account's name to view its credentials. You can retrieve them in the format you need:

- **Robot Account** – View the robot's token. Use **Regenerate token now** to issue a new one.
- **Kubernetes Secret** – Download the credentials as a Kubernetes pull secret (YAML).
- **Podman** – Copy a full `podman login` command with the credentials.
- **Docker Configuration** – Copy a full `docker login` command with the credentials.
