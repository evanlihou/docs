---
title: Jenkins Pipeline projects
description: Managing Octopus steps in a Jenkins pipeline project.
position: 20
---

This page lists the arguments you can supply to the Octopus Jenkins Pipelines commands to run against your Octopus Deploy server.

The Jenkins Pipeline support requires plugin version 3.0.0 or later and Jenkins version 2.190.1 or later.

## Pack {#pack}

Step name: **_octopusPack_**

_**octopusPack** allows you to create a package from files on disk during your pipeline execution_.

| Parameters      | Required | Description |
|-----------------|----------|-------------|
| `toolId` | Yes | The Octopus CLI tool to use. |
| `packageId` | Yes | The ID of the package. |
| `packageFormat` | Yes | The format of the package, `zip` or `nupkg`. |
| `sourcePath` | Yes      | Path containing files and directories to include in package. |
| `overwriteExisting` | No | Overwrite an existing package with the same name and version. Valid values are `true` or `false`. Defaults to `false`.|
| `includePaths` | No | New-line separated paths to include files. |
| `outputPath` | No | Path to write final package. Defaults to `.`. |
| `packageVersion` | No | Package version, defaults to a timestamp-based version. |
| `verboseLogging` | No | Turn on verbose logging. Valid values are `true` or `false`. |
| `additionalArgs` | No | Additional arguments to pass to the Octopus CLI [pack](/docs/octopus-rest-api/octopus-cli/pack.md) command. |

Example:
```powershell
octopusPack additionalArgs: '-author "My Company"', outputPath: './artifacts/', overwriteExisting: false, packageFormat: 'zip', packageId: 'OctoPetShop', packageVersion: '1.1.${BUILD_NUMBER}', sourcePath: './bin/Release/publish/', toolId: 'octocli', verboseLogging: false
```

## Push {#push}

Step name: **_octopusPushPackage_**

_**octopusPushPackage** allows you to push packages to the package repository in an Octopus Server_.

| Parameters      | Required | Description |
|-----------------|----------|-------------|
| `toolId` | Yes | The Octopus CLI tool to use. |
| `serverId` | Yes | The ID of the target server to push the package. |
| `spaceId` | Yes | The ID Space on the server to push the package. |
| `packagePaths` | Yes | The path to the package. |
| `overwriteMode` | Yes | Valid values are `FailIfExists`, `OverwriteExisting` or `IgnoreIfExists`. |
| `verboseLogging` | No | Turn on verbose logging. Valid values are `true` or `false`. |
| `additionalArgs` | No | Additional arguments to pass to the Octopus CLI [push](/docs/octopus-rest-api/octopus-cli/push.md) command. |

Example:
```powershell
octopusPushPackage overwriteMode: 'FailIfExists', packagePaths: './artifacts/OctoPetShop.1.1.${BUILD_NUMBER}.zip', serverId: 'octopus-server', spaceId: 'Spaces-1', toolId: 'octocli'
```

## Push package info {#build-information}

Step: **_octopusPushBuildInformation_**

_**octopusPushBuildInformation** allows you to push package information to an Octopus Server_.

| Parameters      | Required | Description |
|-----------------|----------|-------------|
| `toolId` | Yes | The Octopus CLI tool to use. |
| `serverId` | Yes | The ID of the target server to push the package. |
| `spaceId` | Yes | The ID Space on the server to push the package. |
| `packageId` | Yes | The ID of the packages to push the version information, multiple values can be provided seperated by `\n`. |
| `commentParser` | Yes | Valid values are `GitHub` and `Jira`.  |
| `overwriteMode` | Yes | Valid values are `FailIfExists`, `OverwriteExisting` or `IgnoreIfExists`. |
| `gitUrl` | No | The URL of the repository for the package(s). |
| `gitCommit` | No | The commit ID for the repository. Available via `git checkout`. |
| `verboseLogging` | No | Turn on verbose logging. Valid values are `true` or `false`. |
| `additionalArgs` | No | Additional arguments to pass to the Octopus CLI [build-information](/docs/octopus-rest-api/octopus-cli/build-information.md) command.|

Example:
```powershell
octopusPushBuildInformation toolId: 'octocli', serverId: 'octopus-server', spaceId: 'Spaces-1', commentParser: 'GitHub', overwriteMode: 'FailIfExists', packageId: 'OctoPetShopService', packageVersion: '1.2.${BUILD_NUMBER}', verboseLogging: false, additionalArgs: '--debug', gitUrl: 'https://github.com/OctopusSamples/OctoPetShop', gitCommit: '${GIT_COMMIT}'
```

Due to _limitations in Jenkins Pipelines_, you will need to pass the *Git URL* and *Git Commit* values to the `octopusPushBuildInformation`. 
Including these values will allow the build information to provide correct URL links to the source.

For a pipeline source from SCM, set the parameters to `gitUrl: '${GIT_URL}' gitCommit: '${GIT_COMMIT}'`, the `checkoutVars` script will not be required.
For a inline pipeline definition configure the step as:

```powershell
steps {
    script {
        def checkoutVars = checkout([$class: 'GitSCM', userRemoteConfigs: [[url: 'https://github.com/OctopusSamples/RandomQuotes-Java.git']]])
        env.GIT_URL = checkoutVars.GIT_URL
        env.GIT_COMMIT = checkoutVars.GIT_COMMIT
    }
    octopusPushBuildInformation commentParser: 'GitHub', overwriteMode: 'FailIfExists', packageId: 'randomquotes', packageVersion: "1.0.${BUILD_NUMBER}", serverId: "octopus-server", spaceId: "Spaces-2", toolId: 'Default', gitUrl: "${GIT_URL}", gitCommit: "${GIT_COMMIT}"
}
```

## Create release {#create-release}

Step: **_octopusCreateRelease_**

_**octopusCreateRelease** allows you to push packages to the package repository in an Octopus Server_.

| Parameters           | Required | Description |
|----------------------|----------|-------------|
| `toolId` | Yes | The Octopus CLI tool to use. |
| `serverId` | Yes | The ID of the target server to push the package. |
| `spaceId` | Yes | The ID Space on the server to push the package. |
| `project` | Yes | The ID of the project to create the release in. |
| `releaseVersion` | Yes | The version number for the release. |
| `channel` | No | The name of the target channel. Defaults to `Default` channel. |
| `packageConfigs` | No | Collection of package versions to set when creating the release. |
| `defaultPackageVersion` | No | The default version to use for packages associated with the release. |
| `deployThisRelease` | No | Deploy release after creation. Valid values are `true` or `false`. Defaults to `false`. |
| `waitForDeployment` | No | Wait for deployment to complete before continuing. Valid values are `true` or `false`. Defaults to `false`. |
| `cancelOnTimeout` | No | Cancel the deployment after the `waitForDeployment` time. Valid values are `true` or `false`. Defaults to `false`. |
| `tenant` | No | The tenant to deploy the release to. |
| `tenantTag` | No | The tenant tag to deploy the release to. |
| `deploymentTimeout` | No | How long to wait for deployment. Format is `HH:mm:ss`. Default is `00:10:00`. |
| `environment` | Conditional | The environment to deploy release to. Required if `deployThisRelease` is `true`. |
| `jenkinsUrlLinkback` | No | Include link to the Jenkins build that created the release. Valid values are `true` or `false`. Default is `false`. |
| `releaseNotes` | No | Include release notes in release. Valid values are `true` or `false`. Default is `false`. |
| `releaseNotesSource` | No | Valid values are `file` or `scm`. |
| `releaseNotesFile` | Conditional | The file path for release notes, required if `releaseNotesSource` is `file`.  |
| `verboseLogging` | No | Turn on verbose logging. Valid values are `true` or `false`. |
| `additionalArgs` | No | Additional arguments to pass to the Octopus CLI [create-release](/docs/octopus-rest-api/octopus-cli/create-release.md) command. |

Example:
```powershell
octopusCreateRelease serverId: 'octopus-server', spaceId: 'Spaces-1', project: 'Random Quotes', releaseVersion: '2.3.${BUILD_NUMBER}', toolId: 'octocli',     packageConfigs: [[packageName: 'Nuget.CommandLine', packageReferenceName: 'NugetCLI', packageVersion: '5.5.1']] deployThisRelease: true, cancelOnTimeout: false, deploymentTimeout: '00:15:00', environment: 'test', tenant: 'The Tenant', tenantTag: 'importance/high', jenkinsUrlLinkback: true, releaseNotes: true,  releaseNotesSource: 'scm'
```

## Deploy release {#deploy-release}

Step: **_octopusDeployRelease_**

_**octopusDeployRelease** allows you to push packages to the package repository in an Octopus Server_.

| Parameters           | Required | Description |
|----------------------|----------|-------------|
| `toolId` | Yes | The Octopus CLI tool to use. |
| `serverId` | Yes | The ID of the target server to push the package. |
| `spaceId` | Yes | The ID Space on the server to push the package. |
| `project` | Yes | The ID of the project to create the release in. |
| `environment` | Yes | Environment to deploy release. |
| `releaseVersion` | Yes | The version number for the release. |
| `cancelOnTimeout` | No | Cancel the deployment after the `waitForDeployment` time. Valid values are `true` or `false`. Defaults to `false`.  |
| `tenant` | No | The tenant to deploy the release to. |
| `tenantTag` | No | The tenant tag to deploy the release to. |
| `waitForDeployment` | No | Wait for deployment to complete before continuing. Valid values are `true` or `false`. |
| `deploymentTimeout` | No | How long to wait for deployment. Format is `HH:mm:ss`. Default is `00:10:00`. |
| `variables` | No | Set prompted variable values. Format is `key1=value1\nkey2=value2`. |
| `verboseLogging` | No | Turn on verbose logging. Valid values are `true` or `false`.|
| `additionalArgs` | No | Additional arguments to pass to the Octopus CLI [deploy-release](/docs/octopus-rest-api/octopus-cli/deploy-release.md) command.|

Example:
```powershell
octopusDeployRelease toolId: 'octocli', serverId: 'octopus-server', spaceId: 'Spaces-1', project: 'OctoPetShop', environment: 'test', releaseVersion: '1.2.${BUILD_NUMBER}', deploymentTimeout: '00:05:00', waitForDeployment: false, cancelOnTimeout: true
```