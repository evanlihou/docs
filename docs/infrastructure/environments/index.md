---
title: Environments
description: Environments are how you group your deployment targets so you can promote your software through different phases, for instance, into Development, then Test, and finally into Production.
position: 10
---

!include <environments>

## Edit your environments

To edit individual environments, click the ... overflow menu for that environment. From here, it is possible to edit the environment, description, change the [guided failure mode](#guided-failure-mode), enable or disable [dynamic infrastructure](#dynamic-infrastructure), or delete the environment.

## Environment permissions

You can control who has access to view, edit, and deploy to environments by assigning users to Teams and assigning roles to those teams. For more information, see the section on [managing users and teams](/docs/security/users-and-teams/index.md).

## Manage your environments

If you're working with a large number of environments and deployment targets, the **Environments** page makes it easy to sort, filter, and view your environments and the deployment targets that belong to each environment.

## Sort your environments

Click the ... overflow menu on the environments sections to reveal the **reorder** menu and access a drag and drop pane to sort your environments.

The order that environments are shown in the environments tab also affects:

- The order that they are shown in the Dashboard.
- The order that they are listed when choosing which environment to deploy a release to.

It's a good idea to put your least production-like environments first, and the most production-like environments last.

## Use advanced filters

You can use advanced filters to search your environments by clicking on **SHOW ADVANCED FILTERS** from the environment page.

This will let you search by:

- Name
- Deployment target
- Environment
- Target Roles
- Health Status
- Communication style

## Learn more

Learn how to add and manage your [deployment targets](/docs/infrastructure/deployment-targets/index.md).
