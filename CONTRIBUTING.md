# Contributing to Tekton

Thank you for contributing your time and expertise to Tekton. This
document describes the contribution guidelines for the project.

**Note:** Before you start contributing, you must read and abide by our **[Code of Conduct](./code-of-conduct.md)**.


## Contributing to Tekton code

To set up your environment and begin working on our code, see [Developing for Tekton](./DEVELOPMENT.md).

[The `community` repo](https://github.com/tektoncd/community) contains information on the following:

- [Development standards](https://github.com/tektoncd/community/blob/main/standards.md), including:
  - [Writing high quality code](https://github.com/tektoncd/community/blob/main/standards.md#coding-standards)
  - [Adopting good development principles](https://github.com/tektoncd/community/blob/main/standards.md#principles)
  - [Writing useful commit messages](https://github.com/tektoncd/community/blob/main/standards.md#commit-messages)
- [Contacting other contributors](https://github.com/tektoncd/community/blob/main/contact.md)
- [Tekton development processes](https://github.com/tektoncd/community/tree/main/process#readme), including:
  - [Finding things to work on](https://github.com/tektoncd/community/tree/main/process#finding-something-to-work-on)
  - [Proposing new features](https://github.com/tektoncd/community/tree/main/process#proposing-features)
  - [Performing code reviews](https://github.com/tektoncd/community/tree/main/process#reviews)
  - [Becoming a community member and maintainer](https://github.com/tektoncd/community/blob/main/process/contributor-ladder.md)
- [Making changes to the Tekton API](api_compatibility_policy.md#approving-api-changes)
- [Understanding the Tekton automation infrastructure](https://github.com/tektoncd/plumbing)

Additionally, please read the following resources specific to Tekton Pipelines:

- [Tekton Pipelines GitHub project](https://github.com/orgs/tektoncd/projects/3)
- [Tekton Pipelines roadmap](roadmap.md)
- [Tekton Pipelines API compatibility policy](api_compatibility_policy.md)

For support in contributing to specific areas, contact the relevant [Tekton Pipelines Topical Owner(s)](topical-ownership.md). 

## Merge Queue

This repository uses [GitHub's merge queue](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue) feature to ensure that all pull requests are tested together before merging to the main branch. This helps maintain a stable main branch by preventing race conditions where multiple PRs pass individually but conflict when merged together.

Once your PR is approved and all required checks pass, a maintainer will add it to the merge queue. The PR will then be automatically tested in queue order and merged if all checks continue to pass.

## Contributing to Tekton documentation

If you want to contribute to Tekton documentation, see the
[Tekton Documentation Contributor's Guide](https://github.com/tektoncd/website/blob/main/content/en/docs/Contribute/_index.md).

This guide describes:
- The contribution process for documentation
- Our standards for writing high quality content
- Our formatting conventions

It also includes a primer for getting started with writing documentation and improving your writing skills.