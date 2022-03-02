---
Title: Devcontainer with VS Code
---

## Use a local devcontainer

If you don't want any local setup (apart from Docker Desktop), but still run your own Hugo instance, you can make use of the preconfigured devcontainer. If you want to learn more about the concept, visit [https://code.visualstudio.com/docs/remote/containers](https://code.visualstudio.com/docs/remote/containers). To use it, you need to take the following steps:

1. Start [Docker Desktop](https://www.docker.com/products/docker-desktop) and [switch to Linux containers](https://docs.docker.com/desktop/windows/#switch-between-windows-and-linux-containers) by right-clicking on the Docker logo in the system tray and selecting "Switch to Linux containers...". If you only see "Switch to Windows containers...", then you are already switched to Linux containers. If anything goes wrong, check the extended installation documentation [here](https://code.visualstudio.com/docs/remote/containers#_installation)
2. Install the [Remote development extension pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack) in Visual Studio Code
3. Run the action "Remote containers: Clone Repository in Container Volume" and select the fork you created. If you haven't done that before, go through the [docs](https://alguidelines.dev/docs/contributing/forkandpr/#step-1-fork).
4. Wait for a bit. When you do this for the first time, it can take a couple of minutes. Next time it will be faster...
5. After a while, you will have VS Code with the cloned repository and the terminal should show something like "Done. Press any key to close the terminal."
6. Run the action "Tasks: Run task" and select "Start local Hugo Server" from the list. If you don't see that entry, you might have to reload your VS Code window and try again
7. After Hugo has generated the site, you will get a notification that offers you to "Open a browser". Click on that and you will see your local instance of the AL guidelines! Again, on the first try it will be a bit slow and sluggish, but the second one should be fast.
8. Now you can make changes and just save them. If you open the terminal, you will see a message that tells you that a change was detected and the site was rebuilt. After that, the change should automatically appear in your browser

Here is a walkthrough of the full process:

<video width=100% controls>
    <source src="alguidelines walkthrough.mp4" type="video/mp4">
    Your browser does not support the video tag.  
</video>

## Use GitHub Codespaces

What is also great about this, is that you can also use [GitHub Codespaces](https://github.com/features/codespaces) with that setup. In that case, steps 1-5 become two clicks... Here is another full walkthrough:

<video width=100% controls>
    <source src="alguidelines codespace.mp4" type="video/mp4">
    Your browser does not support the video tag.  
</video>
