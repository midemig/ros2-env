# ros2-env
A zsh plugin to manage ROS 2 environment and workspaces

# What is this and why do I need it?
If you are working with ROS, you probably have more than one workspace (different projects, robots, maybe even different versions of the same package). This means having to manually source the current workspace we are working on, which is no bueno. Moreover, when using `colcon` to build the workspace, you need to manually `cd` to the workspace's dir and run the command there.

Over the years working with ROS 1 and 2, I have seen (and created) multiple solutions, "hacks", aliases, functions and tears, usually included in the shell configuration to tackle these problems. This plugin is a cured compilation of all that.

The `ros2-env` plugin allows you to define a list of workspaces, and to assign a name to each one of them. Then you can "activate" one of the workspaces, sourcing it and making it the target for the colcon utility functions that are also provided by this plugin.

**Note:** Right now only one workspace can be active at the same time. If you require multiple "chained" workspaces sourced, feel free to reach out. Issues and PRs are welcome!

The active workspace is stored in the environment variable `$ROSWS_ACTIVE_WS`, which can be changed with the `rosws` command described below.

# Disclaimer
This workspace management part of the `ros2-env` plugin is heavily inspired by [wd](https://github.com/mfaerevaag/wd). Most of the functions, completions and even the tests have been extracted from this repository. Check it out if you still don't know about that amazing plugin!


# Colcon build utility

Build the active workspace with the `cb` (colcon build) command. No matter where you are.

## Usage (`cb`)

* Compile the current active workspace:

```zsh
cb
```

* Compile any workspace by name:

```zsh
cb foo
cb bar
```

**Note:** If you build any listed workspace with `cb ws_name`, the just-built workspace will be sourced and set as the active one:

```zsh
# Active workspace is 'foo'
cb bar
# Now 'bar' is sourced and 'foo' is not.
```

# Cleaning workspaces
If sometimes you feel the need to clear all the compilation files that are generated in the `build`, `install` and `log` subdirectories, this comes in handy (sometimes we just need a compilation break):

## Usage (`colcon_clean`)

* Clean the compilation files from the current active workspace:

```zsh
colcon_clean
```

* Clean any workspace by name:

```zsh
colcon_clean foo
colcon_clean bar
```


# Workspace management
Similar to `wd`, this plugin provides a `rosws` command that allows you to change the active workspace, and to manage the list of registered workspaces. Only one workspace can be active at the same time.

## Usage (`rosws`)

* Add current working directory to list of workspaces:

```zsh
rosws add foo
```

If a workspace with the same name exists, use `rosws add foo --force` to overwrite it.

**Note:** The workspace cannot contain colons (either the name or the path). This will conflict in how `rosws` stores the workspaces.

You can omit the workspace name to automatically use the current directory's name instead.

* You can make `foo` the active workspace with:

```zsh
rosws foo
```

* Remove workspace:

```zsh
rosws rm foo
```


* List all registered workspaces (stored in `~/.config/ros2-env/workspaces` by default):

```zsh
rosws list
```

* Show information of given workspace:

```zsh
rosws show
```

* Show path (pwd) of given workspace:

```zsh
rosws path foo
```

* Remove workspaces pointing to non-existent directories.

```zsh
rosws clean
```

Use `rosws clean --force` to not be prompted with confirmation.

* Print usage info:

```zsh
rosws help
```

The usage will be printed also if you call `rosws` with no command

* Print the running version of `rosws`:

```zsh
rosws version
```

# Configuration

The configuration file where workspaces are registered is stored by default in `~/.config/ros2-env/workspaces`. It is possible to modify this by setting the environment variable `$ROSWS_CONFIG`.

## Colcon parameters
It is also possible to control the arguments passed to colcon when using the `cb` command, by setting the `$CB_EXTRA_ARGS` environment variable. For example:

```zsh
export CB_EXTRA_ARGS="--symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release"
```

By default, this variable only includes the `--symlink-install` option.

**Note:** In the future, `cb` will allow adding extra arguments that will be passed to colcon, to avoid setting the environment variable.

## ROS 2 distro
TODO.

# Automatic workspace switching
**Note: This is still WIP and is not implemented.**

When changing the working directory (with cd, wd, etc.), if the current directory is in the list of workspaces, automatically set it as the active workspace.

This behavior can be enabled/disabled by setting the environment variable `$ROSWS_AUTO` to 1/0.
