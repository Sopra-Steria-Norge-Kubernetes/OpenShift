# Auto-Merging Existing of Teams & Tenants

## Introduction

We've created two seperate auto-merge workflow to approve and merge **team** and **tenant** changes that don't need manual checks. This helps speed up the process and lets developers focus more on important work instead of getting stuck in routine tasks. 

Auto-merging happens only when all status checks pass. This way, we ensure that only well-tested and verified changes go through, keeping the system stable and reliable. Auto-merging is restricted to directories where Sopra Steria has **CODEOWNER rights**, specifically in the `/wave-*/` directories and the `/team-definitions/` directory. This restriction is enforced by GitHub's branch protection rules, ensuring that only changes meeting all criteria and passing all checks are automatically merged. This approach guarantees that our automated system maintains the high standards we expect for our codebase.

## Team - What Requires Manual Approval

The table below contains which changes in a team values file that would trigger a manual approval.

!!! Note
    - When deleting an existing team or creating a brand new team from scratch, it will trigger for manual approval

    - If the team values file contains fields that does not exist in the helm chart or a required field is not specified, it will trigger for manual approval

| **Category**      | **Field**                                   | **Allowed Actions**                     | **Restrictions**               |
|-------------------|---------------------------------------------|-----------------------------------------|--------------------------------|
| requests[]        | Cpu                                         | Decrease<br>Change unit<br>(Gi - Mi)  | Increase<br>Remove unit          |
|                   | Memory                                      | Decrease<br>Change unit<br>(Gi - Mi)  | Increase<br>Remove unit          |


## Tenant - What Requires Manual Approval

The table below contains which changes in a tenant values file that would trigger a manual approval.

!!! Note
    - When deleting an existing tenant or creating a brand new tenant from scratch, it will trigger for manual approval

    - If the tenant values file contains fields that does not exist in the helm chart or a required field is not specified, it will trigger for manual approval

| **Category**      | **Field**                                   | **Allowed Actions**                     | **Restrictions**               |
|-------------------|---------------------------------------------|-----------------------------------------|--------------------------------|
| limits[]          | Cpu                                         | Decrease<br>Change unit<br>(Gi - Mi)    | Increase<br>Remove unit        |
|                   | Memory                                      | Decrease<br>Change unit<br>(Gi - Mi)    | Increase<br>Remove unit        |
| requests[]        | Cpu                                         | Decrease<br>Change unit<br>(Gi - Mi)    | Increase<br>Remove unit        |
|                   | Memory                                      | Decrease<br>Change unit<br>(Gi - Mi)    | Increase<br>Remove unit        |
| environments[]    | name                                        | Add                                     | Change<br>Remove               |

## Required Status Checks to Pass Before Merging
To ensure that no changes outside of monitored fields are merged, specific status checks must pass before any changes can be merged. This process includes several key steps:

**Validate team/tenant files:** 

- Validates the syntax and performs a series of checks to ensure that the team or tenant orders adheres to specified standards and requirements.

**Check Auto-merge / check-files-in-pr:** 

- This check verifies the presence of files in the pull request (PR) and uploads temporary files for comparison against existing files in the main branch.

**Check Auto-merge / check-if-files-can-auto-merge:**

- This step downloads the temporary files, performs a comparison, and validates the changes to ensure they can be safely auto-merged.



