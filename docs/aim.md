# Aim of the project

This explains litteraly all the possible wanted features for this project. Some are mandatories, others would be "nice to have".

Undock is the tool that will be triggered by your CI pipeline, through command line or by a command on the Slack channel in order to package your code and bring it alive on the desired environment.

## Engine

It first consists of a engine that will parse a `.undock.yml` file at the root of your repository to read configurations. The user will interact with the engine using a command line interface. The engine will have two purposes detailed in the two main following sections.

### Build a package

Generally speaking, this step is about preparing your source code locally to fit to the wanted environment and to be ready to be deployed afterwards. Different strategies will be developed for this phase.

#### Default strategy

The engine will clone the latest version of your repository and checkout onto the desired branch or tag. This operation will be done on the same machine as the engine. Different features belong to this strategy:

 - Delete files and folders that are shared among releases
 - Delete unwanted files and folders (for instance the `.git` folder)
 - Use a subfolder as root of the package
 - Rename and apply files that are specific to the wanted environment (for instance `config.yml__INTE__` to `config.xml`)
 - Generate a release note/changelog
 - Define if the package has to be compressed

#### Docker strategy

This one has to be defined but the idea is to be able to build and push one or multiple docker image to a registry.

For all strategies, hooks will be created in order to let the user scripts additional steps while the package is building.

### Send the package

As the package has been created, it now needs to be deployed to one or multiple environments. Different strategies are again possible.

#### Default strategy

The package will be sent through SSH or using Rsync using parallel goroutines if multiple environments are targeted. Undock will handle user/password authentications and SSH keys. The package will be synchronized in a release subdirectory of the configured path. The release will then be activated by symlink. It will be possible to rollback easily. Here is a not exaustive list of features of this deploy strategy:

 - Delete old releases
 - Link shared files and folders to the new deployed release
 - Hooks to trigger action before/after the package has been deployed

#### Docker strategy 

If the docker strategy has been used for building the package, this deploy strategy will update containers with new brand new generated images.

## Web UI

A second part of the project will be to implement a UI around the engine. This interface will be installable anywhere on any servers. It will be possible to load up multiple projects into a clear and ergonomic interface. You will run manually deployments for any projects using the UI. Permissions and roles will be setup to give read/execute access. A REST API will be implemented in order to let the world trigger deployments. For instance, Jenkins will be able to trigger a deployment if the tests have passed.