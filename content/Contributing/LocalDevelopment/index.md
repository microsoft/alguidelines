+++
chapter = true
pre = "<b><i class='fas fa-clone'></i> </b>"
title = "Local Container Development"
weight = 100
+++

# Local Container Development

- [Abstract](#abstract)
- [Requirements](#requirements)
- [How to get started](#how-to-get-started)
- [Run the container](#run-the-container)
- [Update on changes](#update-on-changes)

## Abstract

Nowadays it's common practice to have an environment up-and-running at your local machine which is capable of giving you a representation of the deployment environment.

This allows us a better reviewing of our own code answering the question "How it would look like after the deployment?"

## Requirements

- Git
- Docker Desktop

## How to get started

First of all we need to have the themes/relearn properly loaded. Most likely you have already cloned the repository but without the `--recursive` option. In this case use `git submodule update --init --recursive` from within your cloned repository folder.

If you are wondering how to clone repository with all submodules by default the `git clone --recursive` argument will serve your needs.

Last but not least you need to change your Docker Desktop to **"Switch to Linux containers..."** in order to [Run the container](#run-the-container).

## Run the container

By executing the following command, you will have your local alguidelines hugo server up-and-running:

```docker run --rm -it -p 1313:1313 -v ${PWD}:/src klakegg/hugo:latest "server --bind 0.0.0.0"```

After everything is successful you will be able to see your local alguidelines hugo at http://localhost:1313.

Note: you have to execute this command from within your cloned alguidlines repo.

## Update on changes

1. In the terminal **Press Ctrl+c to stop**.

2. Press **Arrow-Up Key** or run the command as in [Run the container](#run-the-container).

3. **Reload** the browser tab
