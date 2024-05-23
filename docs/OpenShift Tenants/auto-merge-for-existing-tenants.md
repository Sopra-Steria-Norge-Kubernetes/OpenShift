# Auto-merging existing tenants

## Introduction

To make things easier for our developers, we've created an auto-merge workflow to approve and merge tenant changes that don't need manual checks. This helps speed up the process and lets developers focus more on important work instead of getting stuck in routine tasks. 

Auto-merging happens only when all status checks pass. This way, we ensure that only well-tested and verified changes go through, keeping the system stable and reliable. Auto-merging is restricted to directories where Sopra Steria has **CODEOWNER rights**, specifically in the `/wave-*/` directories. This restriction is enforced by GitHub's branch protection rules, ensuring that only changes meeting all criteria and passing all checks are automatically merged. This approach guarantees that our automated system maintains the high standards we expect for our codebase.

## Allowed auto-merge fields


Any change outside these fields will result in a manual approval

| **Category**      | **Field**              | **Allowed Actions**                    | **Restrictions**              |
|-------------------|--------------------|----------------------------------------|-------------------------------|
| labels[]          | <Key: value>       | - Can add<br>- Can remove<br>- Can modify existing |                              |
| limits[]          | Cpu                | - Can decrease<br>- Can change unit (Gi - Mi) | Cannot increase<br>Cannot remove unit |
|                   | Memory             | - Can decrease<br>- Can change unit (Gi - Mi) | Cannot increase<br>Cannot remove unit |
| requests[]        | Cpu                | - Can decrease<br>- Can change unit (Gi - Mi) | Cannot increase<br>Cannot remove unit |
|                   | Memory             | - Can decrease<br>- Can change unit (Gi - Mi) | Cannot increase<br>Cannot remove unit |
| environments[]    | externalURLs[]     | - Can add<br>- Can remove<br>- Can modify existing |                              |
|                   | externalIPs[]      | - Can add<br>- Can remove<br>- Can modify existing |                              |
| backup[]          | take_backup        | - Can modify existing                   |                              |
|                   | Schedules[]        | - Can add<br>- Can remove<br>- Can modify existing |                              |



## Required Status Checks to Pass Before Merging
To ensure that no changes outside of monitored fields are merged, specific status checks must pass before any changes can be merged. This process includes several key steps:

**Validate tenant files:** 

- Validates the syntax and performs a series of checks to ensure that the tenant order adheres to specified standards and requirements.

**Check Auto-merge / check-files-in-pr:** 

- This check verifies the presence of files in the pull request (PR) and uploads temporary files for comparison against existing files in the main branch.

**Check Auto-merge / check-if-files-can-auto-merge:**

- This step downloads the temporary files, performs a comparison, and validates the changes to ensure they can be safely auto-merged.

These status checks are crucial in maintaining the quality and stability of our system by preventing unverified changes from being merged.
