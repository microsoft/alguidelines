+++
chapter = true
pre = "<b><i class='fas fa-clone'></i> </b>"
title = "Guide to Fork & PR"
weight = 100
+++

## Abstract

AL Guidelines is a community project, and as such YOU are encouraged to submit corrections and new ideas. In order to get your content included, you must submit a pull request to the GitHub Repository (Located here: https://github.com/microsoft/alguidelines\). All Pull Requests are subject to approval by a minimum three admins.

If You are toying with an idea, but You aren't ready to create a document just yet, you are encouraged to create a project discussion thread here: https://github.com/microsoft/alguidelines/discussions

{{% notice warning %}}
If You haven't worked in collaboration with "external" GitHub repositories before, please familiarize yourself with that process by visiting:

https://docs.github.com/en/pull-requests/collaborating-with-pull-requests

{{% /notice %}}

## Steps
Now that You have decided that You are ready to contribute, these are the steps to take.
{{% notice info %}}
You can read more about this process here:

https://docs.github.com/en/get-started/quickstart/contributing-to-projects
{{% /notice %}}

### Step 1: Fork
In order to work on the repository, You must [Fork](https://docs.github.com/en/get-started/quickstart/fork-a-repo) the repository.

By forking the repository, You essentially create a copy into Your own account. 

Start by going to the GitHub Repository (), and press the Fork ![Fork Button](./fork_button.jpg "Fork")

Once You have successfully forked the repository, go to your own GitHub repository : ![Forked Repository Representation](./ForkedRepro.png "Forked repository")

You are now able to clone your own repository to your local pc and start editing using your favorite editor. [Visual Studio Code](https://code.visualstudio.com/) is perfectly fine for this task.

{{% notice info %}}
You can read more about forking here:

https://docs.github.com/en/get-started/quickstart/fork-a-repo
{{% /notice %}}

### Step 2: Branch
While not necessarily a must, it is always good practice to create a branch off of your forked repository. That will allow you to work on multiple contributions at the same time and won't have to wait for pull requests to be approved before you can continue on your next contribution.

{{% notice info %}}
You can read more about Branches here:
https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-branches
{{% /notice %}}
### Step 3: Pull Request
Once you are happy with your contibution, it's time to create a pull request to propose changes into the main project! This is the final step in producing a fork of someone else's project, and arguably the most important. If you've made a change that you feel would benefit the community as a whole, you should definitely consider contributing back.

To do so, head on over to the repository on GitHub where your project lives. For this example, it would be at `https://www.github.com/<your_username>/alguidelines`. You'll see a banner indicating that your branch is one commit ahead of microsoft:main. Click **Contribute** and then **Open a pull request.**

GitHub will bring you to a page that shows the differences between your fork and the microsoft/alguidelines repository. Click **Create pull request.**

GitHub will bring you to a page where you can enter a title and a description of your changes. _It's important to provide as much useful information and a rationale for why you're making this pull request in the first place._ The project owners needs to be able to determine whether your change is as useful to everyone as you think it is. Finally, click **Create pull request.**
