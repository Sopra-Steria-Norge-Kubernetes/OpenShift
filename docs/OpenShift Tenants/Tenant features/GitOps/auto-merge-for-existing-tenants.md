# Auto-merging existing tenants

## Introduction

To make things easier for our developers, we've created an auto-merge workflow to approve and merge tenant changes that don't need manual checks. This helps speed up the process and lets developers focus more on important work instead of getting stuck in routine tasks. 

Auto-merging happens only when all status checks pass. This way, we ensure that only well-tested and verified changes go through, keeping the system stable and reliable. Auto-merging is restricted to directories where Sopra Steria has **CODEOWNER rights**, specifically in the `/wave-*/` directories. This restriction is enforced by GitHub's branch protection rules, ensuring that only changes meeting all criteria and passing all checks are automatically merged. This approach guarantees that our automated system maintains the high standards we expect for our codebase.

## Allowed auto-merge fields

Any change outside these fields will result in a manual approval.

| **Category**      | **Field**                                   | **Allowed Actions**                     | **Restrictions**               |
|-------------------|---------------------------------------------|-----------------------------------------|--------------------------------|
| labels[]          | <Key: value>                               | Add<br>Remove<br>Change              |                                |
| limits[]          | Cpu                                         | Decrease<br>Change unit<br>(Gi - Mi)  | Increase<br>Remove unit |
|                   | Memory                                      | Decrease<br>Change unit<br>(Gi - Mi)  | Increase<br>Remove unit |
| requests[]        | Cpu                                         | Decrease<br>Change unit<br>(Gi - Mi)  | Increase<br>Remove unit |
|                   | Memory                                      | Decrease<br>Change unit<br>(Gi - Mi)  | Increase<br>Remove unit |
| environments[]    | externalURLs[]                             | Add<br>Remove<br>Change               |                                |
|                   | externalIPs[]                              | Add<br>Remove<br>Change               |                                |
|                   | name                                        | Add                       | Change<br>Remove |
|                   | custom_auto_defined_<br>targetRevision         | Add<br>Remove<br>Change               |                                |
| backup[]          | take_backup                                | Change                              |                                |
|                   | Schedules[]                                | Add<br>Remove<br>Change               |                                |
| argocd           | enable_auto_defined_apps<br>enable_user_defined_apps<br>enable_user_defined_apps_legacy                                | Add<br>remove<br>change              |                                |
| slack_alert_integration             | All values                                 | Add<br>Remove<br>Change               |                                |
| monitoring        | All values                                 | Add<br>Remove<br>Change               |                                |
| gitops            | All values                                 | Add<br>Remove<br>Change               | Blank fields               |

## Required Status Checks to Pass Before Merging
To ensure that no changes outside of monitored fields are merged, specific status checks must pass before any changes can be merged. This process includes several key steps:

**Validate tenant files:** 

- Validates the syntax and performs a series of checks to ensure that the tenant order adheres to specified standards and requirements.

**Check Auto-merge / check-files-in-pr:** 

- This check verifies the presence of files in the pull request (PR) and uploads temporary files for comparison against existing files in the main branch.

**Check Auto-merge / check-if-files-can-auto-merge:**

- This step downloads the temporary files, performs a comparison, and validates the changes to ensure they can be safely auto-merged.

These status checks are crucial in maintaining the quality and stability of our system by preventing unverified changes from being merged.
