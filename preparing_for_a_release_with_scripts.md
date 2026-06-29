# Preparing for a release (with helper scripts)

> **Note:** for now these scripts exist only in the waste exemptions (WEX) repos, so this process currently applies to WEX only.

All our managed Ruby services use the same release helper scripts to automate the mechanical part of a release — creating the release branch, updating `Gemfile.lock` and the `CHANGELOG`, and tagging:

- `bin/prepare-release` — creates the release branch and commits the `CHANGELOG` (and an updated `Gemfile.lock` if you update dependencies).
- `bin/complete-release` — after the release PR is merged, tags the release.

The scripts never push branches, open pull requests or push tags — those steps stay manual.

This is the script-assisted version of [Preparing for a release](preparing_for_a_release.md); both describe the same release.

## How a service is structured

Each service is built from three repos: a shared **engine** (a core gem) and two applications — a **front office** and a **back office** — that depend on it. The scripts treat the engine and the applications slightly differently:

- **Engine** — `bin/prepare-release` only. It updates the CHANGELOG on a `chore/changelog-*` branch. The engine is not tagged, as it is not deployed directly.
- **Applications** — `bin/prepare-release` (creates a `release/vX.Y.Z` branch and prompts for the version), then `bin/complete-release` (tags the merged release).

## Prerequisites

- Up-to-date local clones of the service's repos. Run `git checkout main && git pull` in each before you start.
- A [GitHub personal access token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) in the `CHANGELOG_GITHUB_TOKEN` environment variable. The changelog generator needs this or it will hit a request limit.
- A working Ruby / Bundler toolchain — the scripts run `bundle`, RuboCop and RSpec.

If you plan to update gem dependencies, run `bin/prepare-release` from the same environment where the tests run (the Vagrant VM or Docker container), as the dependency update also runs RuboCop and RSpec. You may also need to configure git (`user.name` and `user.email`) inside that environment.

## Determine the version number

We use semantic versioning to differentiate between patch, minor and major releases. The applications are tagged `vX.Y.Z`; the engine is not tagged, as it is not deployed directly.

When you run `bin/prepare-release` for an application, it suggests the next patch version from the latest `v*` tag (eg `v1.30.1`). Accept the suggestion for a patch release, or type a different version for a minor or major release.

## 1. Release the engine

From the engine repo, on an up-to-date `main`, run:

`bin/prepare-release`

> The script will:
>
> 1. Verify you are on `main` and pull the latest changes.
> 1. Check that `CHANGELOG_GITHUB_TOKEN` is set.
> 1. Create a `chore/changelog-DD_MM_YYYY` branch.
> 1. Optionally run `bundle update`, RuboCop and RSpec — if you choose to update dependencies.
> 1. If you updated dependencies, commit `Gemfile.lock` (with a message listing the gem version changes).
> 1. Generate the changelog via `bundle exec rake changelog`.
> 1. Commit `CHANGELOG.md`.
> 1. Print next steps.
>
> The script does **not** push the branch or create the pull request.

If the script stops part-way — for example, a RuboCop or RSpec failure — fix the issue and run `bin/prepare-release` again. It resumes from where it left off, skipping the main-branch check on the existing `chore/changelog-*` branch.

Then, manually:

`git push -u origin chore/changelog-DD_MM_YYYY`

Open a PR for the branch, get it reviewed, and merge it into `main` in the usual way. The engine is not tagged, so there is nothing more to do for it.

## 2. Release the front office and back office

Do this for each application — the front office and the back office.

From the app repo, on an up-to-date `main`, run:

`bin/prepare-release`

> The script will:
>
> 1. Verify you are on `main` and pull the latest changes.
> 1. Check that `CHANGELOG_GITHUB_TOKEN` is set.
> 1. Prompt for the next version number (suggests an incremented patch, eg `v1.30.1`).
> 1. Create a `release/vX.Y.Z` branch.
> 1. Optionally run `bundle update`, RuboCop and RSpec — if you choose to update dependencies.
> 1. If you updated dependencies, commit `Gemfile.lock` (with a message listing the gem version changes).
> 1. Generate the changelog via `bundle exec rake changelog`.
> 1. Commit `CHANGELOG.md`.
> 1. Print next steps.
>
> The script does **not** push the branch or create the pull request.

If the script stops part-way — for example, a RuboCop or RSpec failure — fix the issue and run `bin/prepare-release` again. It resumes from where it left off, skipping the main-branch check on the existing `release/v*` branch.

Then, manually:

`git push -u origin release/vX.Y.Z`

Open a PR for the branch, get it reviewed, and merge it into `main`.

Once the release PR has been merged, run from the release branch:

`bin/complete-release`

> The script will:
>
> 1. Verify you are on a `release/vX.Y.Z` branch and extract the version.
> 1. Verify the release branch has been merged into `main` (supports regular, squash and rebase merges).
> 1. Switch to `main` and pull the latest changes.
> 1. Create an annotated git tag for the version (`git tag -a vX.Y.Z -m "Version vX.Y.Z"`). It skips this if the tag already exists.
> 1. Print next steps.
>
> The script does **not** push the tag.

Push it manually:

`git push origin vX.Y.Z`

Tag the version before you submit the RFC, but _don't_ create a release in GitHub until after shipping is complete.

### Removing a version tag

If you need to cancel a release and no longer need the version tag, delete it with the following commands:

`git push --delete origin vX.Y.Z`

`git tag --delete vX.Y.Z`

This makes sure the release list only contains real releases.

---

The remaining steps deploy the tagged release and are the same as in the [manual process](preparing_for_a_release.md#publish-the-release); they are reproduced here so this runbook is self-contained.

<!-- Deployment steps below mirror preparing_for_a_release.md — keep the process in sync. -->

## Publish the Release
To publish the release:

- Go to the repository's code page.
- Click on "Releases" on the right-hand side of the page.
- View the latest release and copy the description from it.
- Click the option to "Draft a new release".
- Select the tag for the release.
- Fill in/paste and edit the description with the RFC number, today's date, and ensure the CHANGELOG link is correct.
- Click "Publish".
- Edit the description for the Jira ticket for the release and add a tick mark after the step to publish the release on GitHub.
- Please note that these instructions assume familiarity with the repository's code page, GitHub's release functionality, and Jira ticket management.


## Update the Jenkins deploy job

Find the PRE-PROD job to deploy the app.

In the `Properties content` box, update `DEPLOY_BRANCH` to the new tag (eg `v1.1.7`). Then save your changes.

## Contact Web Ops

Before submitting an RFC, check with Web Ops and make sure they can deploy on that date.

Post in the service's channel on the Web Ops Slack and say when you'd like to deploy.

Once you have confirmed the date, you can submit an RFC.

## Submit an RFC

You must submit an RFC, or "request for change", using MyIT.

Select the following options to begin an RFC:

"Normal Change" > "Initiate RFC" > "New" (button on top)

### Emergency, expedited and normal RFCs

There are three types of changes. This is based on how much notice you are giving for the change.

- Emergency - release within 3 working days
- Expedited - release in 4-10 working days
- Normal - release in 2 weeks' time or later

You can't change the type once you've selected it.

### Filling in the form

The easiest thing to do is to look up an earlier RFC for the same service's Configuration Item and copy from that.

However, if you are filling in a new form, be sure to fill in the fields as follows:

- Category - "software"
- Environment - "production"
- Configuration item - the service's Configuration Item (copy from an earlier RFC for the same service).
- Short descriptions - "Deploy service changes" (plus "fix data issues" if release includes a data fix)
- Description – copy the opening section from an earlier RFC. For each repo involved, add the name of repo, a link to the CHANGELOG, then copy and paste from changelog for each repo affected. You may need to tidy up formatting. If including data fixes, describe each one in a bullet point.

Fill in all the fields in the "Planning" tab.

Put in a planned start and end date. We usually start at 10 and end at 10:10.

Hit the "Save for later" button regularly. This saves your work, but also checks if there are scheduling conflicts.

Only click the "Validate" button when the RFC is ready, as this will not only validate the RFC, but submit it for approval. "Save for later" before submitting to make sure there is no scheduling clash.

## Set up a calendar invite

Create a calendar event for when the release is scheduled.

Invite:

- the head of Web Ops
- the person in Web Ops who will be handling deployment
- developers and testers in the delivery team (as optional attendees)

In the body of the event, include the RFC number and which jobs should be run as part of the deployment.

If the release includes a data fix as part of a new Jenkins job, then add a README to the Tools repo in GitLab. This should include details about the deployment process, including which jobs to run. Check the repo for previous release note READMEs and follow the same format.

Once the README has been merged into main, copy its contents to the calendar invite. Include a link to the README on GitLab.

## Once the RFC is approved

Confirm with WebOps that it's been approved and everyone is still happy to proceed.

Do not ship anything unless the RFC has been approved.

## Release day

Communication on release day usually takes place in the service's channel on the Web Ops Slack.

Web Ops will begin the release at the agreed time. They will usually say when they are starting the release and once it is completed.

When all the jobs have run, someone from the delivery team (usually QA) will run a smoke test to make sure the release has shipped and that everything is working as expected, then confirm with Web Ops.

## After a successful release

Update the RFC to say that the release was a success.

For any repos included in the release (excluding the engine), create a new release on GitHub using the version tag.
