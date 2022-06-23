# Project Application and Funding Service

The Project Application and Funding Service (PAFS) is used by regional management authorities to apply for funding for flood and coastal risk management projects.

# Repos

The service uses the following repos

(Pafs-user)[https://github.com/DEFRA/pafs-user]
(Pafs-admin)[https://github.com/DEFRA/pafs-admin]
(Pafs_core)[https://github.com/DEFRA/pafs_core]

Pafs-user is the front office application for users to submit thheir project proposals
Pafs-admin is the back office application to administer user accounts
Pafs_core is the common set of code used by both of the applications. Most code changes will be made here.

# Updating the repos

`pafs_core` has the `main` branch which is for work ready for release to production and the `develop` branch for work in progress.
`pafs-admin` and `pafs-user` have the ```main``` branch which uses the `main` branch from `pafs_core` and the `development` branch which uses the `develop` branch from `pafs_core`.

## Adding features

Create a branch from `develop` on `pafs_core` with a name of `feature/<JIRA ticket number>-<description of feature>`. When it's ready, create a PR to merge it into develop.

Once the work has been merged into develop, update the version of `pafs_core` used on the `pafs-user` and `pafs-admin` `development` branches and deploy them to the development environment for QA.

When the work has been accepted by the business, create a PR from `develop` to `main` on `pafs_core` and, once merged, update the version of `pafs_core` used on the `main` branches.
