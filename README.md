# Example of launching binder with a custom JupyterLab layout

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/ian-r-rose/binder-workspace-demo/master?urlpath=lab)

We would like to be able to launch JupyterLab from a binder deployment with a specific layout.
For instance, the repository authors may want to open a particular file or notebook,
or they may want to display a custom widget, or pull up some helpful documentation.
That way, when the user or student opens the binder, they immediately are
shown the most useful information.

JupyterLab provides functionality to store layouts as JSON files on disk which allow for state restoration.
These layout files (called workspaces) can be specified via URL, and can be used to specify demo layouts.
This repository is a demonstration of how to use that functionality.

## Making a workspace

The easiest way to make a workspace is to launch JupyterLab and arrange the application in the layout you prefer.
Once you have your layout, open a terminal and enter
```bash
mkdir binder
jupyter lab workspaces export > binder/workspace.json

```
This will store your workspace in a new file called `workspace.json`.
You will [distribute](binder/workspace.json) this file along with your binder repository.

## Configuring binder to use the workspace

Just putting the workspace file in your repository is not enough.
At binder launch time, you must import that workspace, and then load JupyterLab with it.

There is a corresponding `import` command for exactly this use case.

In our binder [`postBuild`](binder/postBuild) script, we enter the following:

```bash
#!/bin/bash

# Import the workspace into JupyterLab
jupyter lab workspaces import binder/workspace.json
```

The first line specifies that we are using `bash` to execute the script.
The second performs the import of the workspace file.

In some cases you may want to edit a workspace to insert values that are
not available at the time of the image build.
If that is the case, you can also perform the workspace import in a binder `start` script.

## Custom settings

Like workspaces, JupyterLab settings are stored on the server in JSON files.
You can also distribute these with your repository.
The default location for setting files is in `~/.jupyter/lab/user-settings`.
For instance, this repository includes a [setting file](.jupyter/lab/user-settings/@jupyterlab/apputils-extension/themes.jupyterlab-settings)
that sets a dark theme for the application:

```json
{
    "theme": "JupyterLab Dark"
}
```
In this case, all that is required is to place the setting files in the right directory.

## Deployment

At this point, you just need to push your repository to the remote,
and open binder with a default URL pointing to JupyterLab.

This demo used the default workspace (which is shown when you navigate to `/lab`),
but it is also possible to import to named workspaces, which may be useful
if your repository wants to use several different layouts.
