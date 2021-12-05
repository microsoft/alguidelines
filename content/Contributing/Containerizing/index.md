+++
chapter = true
pre = "<b><i class='fas fa-clone'></i> </b>"
title = "Containerizing"
weight = 100
+++

# Containerizing

## Abstract

Nowadays it's common practice to have an environment up-and-running at your local machine which is capable of giving you a representation of the deployment environment.

This allows us a better reviewing of our own code answering the question "How it would look like after the deployment?"

## How to get started

First of all we need to have the themes/relearn properly loaded. Most likely you have already cloned the repository. In this case use:

`git submodule update --init --recursive`

If you are wondering how to clone the submodules by default the `--recursive` will serve your needs.

## Run the container

By executing the following command, you will have your local alguidelines hugo server up-and-running:

```docker run --rm -it -p 1313:1313 -v ${PWD}:/src klakegg/hugo:latest "server --buildDrafts --watch --bind 0.0.0.0"```

## FAQ

#### 1. I get the following Error when I try to run the container "Error building site: "/src/content/Contributing/ForkAndPR/index.md:14:1": failed to extract shortcode: template for shortcode "notice" not found"

> Cloning the themes/relearn project solves this error: `git submodule update --init --recursive`.

#### 2. I made some changes in the documentation but they are not shown to me in my local hugo environment.

> Consider building the image and running your local docker container again.
