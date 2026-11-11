# Spotify and CI

## Background

We started this session with a look at the
[Prompting for Attorneys](https://huggingface.co/spaces/arthrod/prompting_for_attorneys)
app on Hugging Face. For a stronger data science example of a Marimo app, have a look at this
[movies dashboard](https://marimo.app/gh/marimo-team/gallery-examples/main?entrypoint=notebooks%2Fdashboard%2Fmovies.py&mode=read&show-code=false).

Behind every good app, there is a GitHub repository, and so we explored the
[GitHub repo](https://github.com/cicero-im/prompting) behind this one. We noticed
these directories:

* `.devcontainer`, which is a configuration space for preparing Codespaces
* `.github/workflows`, which is a configuration space for *GitHub Actions*, a form of *Continuous Integration*
* `docker`, which holds a *Dockerfile* - the configuration for *containerising* an application

![CI and Virtualisation configs](assets/ci-virt-dirs.png)

This repository has all three too. Its Dockerfile is in the project root.

## Spotify EDA

This repository contains a Marimo notebook from a series on Polars, from
[the Marimo team](https://github.com/marimo-team/learn/tree/main/polars). The notebook
uses some Spotify tracks data to demonstrate Polars functionality and take the learner
through some exploratory data analysis (EDA) techniques.

You can run this notebook like an app, like this:

```bash
uv run marimo run notebooks/spotify_eda.py
```

`marimo run` shows the notebook's outputs without the code.

**Try it out! Press `Ctrl+C` in the terminal to stop the app when you're done.**

## Dev Container Configuration

Devcontainers allow you to define a consistent development environment.
Codespaces will always look for and use a devcontainer config, but you
can also use devcontainers in your local VS Code.

This repo contains a `devcontainer.json` that:

* installs `uv` with the *postCreateCommand*
* installs the VS Code `Python` extension with *customizations*
* calls the *postStartCommand* to `sync` the environment, so you don't have to

The *customizations* also change some VS Code settings. One of them switches GitHub Copilot off for now.

**Have a look at `.devcontainer/devcontainer.json` and find each of these**

## GitHub Action Configuration

GitHub Actions are a way to run continuous integration (CI) workflows,
or automated processes triggered by updates to your repository code.
These processes run on virtual GitHub servers called runners.

This repo contains a workflow configuration in
`.github/workflows/python_app.yaml` that runs on pushes to the `main` branch.

On a repository without any GitHub Actions configured, some templates are
provided to help you get started. We've used the `Python application` template,
which sets up a workflow to:
* Check out the repository code
* Set up Python
* Install dependencies
* Run tests and checks

Unfortunately, the workflow is incomplete and doesn't use very modern tooling. For
example it uses `pip` to install dependencies, rather than `uv`.

### Installing `uv` on the Runner

To use `uv` in the workflow, we know we first need to install it. For most common
CI tasks, a GitHub Action workflow already exists. Installing `uv` is one of these
common tasks.

**Search the [GitHub Actions Marketplace](https://github.com/marketplace?type=actions) for the official "uv" workflow and use the examples within to install `uv` on the runner**

### Exercise

Next, have a look at the rest of the workflow file and complete the TODOs that are
listed within it.

**Copilot is switched off in this Codespace for now. Have a go yourself first!**

<details>
<summary>Hint</summary>

Have a look back at the README in your Intro to Marimo Notebooks repository from the last
session. Keep the `--check` on the `ruff format` and `isort` commands. Without it they change
the files on the runner and the steps always pass.

</details>

### Triggering GitHub Actions

First, commit and push any changes to the workflow configuration to GitHub. This
should trigger the workflow run, which you can monitor from the repository page
on GitHub, under the `Actions` tab. Click the latest run and then the `build` job
to see the output of each step.

Are there any issues? If so, you would come back to the Codespace to fix them and
push again until the workflow runs successfully.

Let's now make some changes to the notebook itself.

### Switching on Copilot

For the next exercises, **feel encouraged to use GitHub Copilot to guide the way**. To switch it on:

* Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on Mac) to open the command palette
* Start typing **Preferences: Open Settings (UI)** and select it
* Choose the **Remote [Codespaces]** tab
* Search for `chat.disableAIFeatures` and untick it

If Copilot doesn't appear, run **Developer: Reload Window** from the command palette.

### Exercises

* Add type hints to the utility function `get_extremes` at the bottom of `notebooks/spotify_eda.py`.
    - You can add type hints to arguments, and to the return type.
* Add a test for `get_extremes` in `tests/test_utility.py`.
    - You may overwrite the existing placeholder test function.

<details>
<summary>Hint</summary>

Have a look back at the test in your Intro to Marimo Notebooks repository from the last session.

</details>

When you're done, commit and push to see your tests run in CI.

## Docker Configuration

Docker is a platform for developing, sharing, and running applications. It
uses containerisation technology to package an application and its dependencies.

It aims to solve the problem of "it works on my machine" by providing a consistent
environment from development to production. Many cloud platforms support Docker
containers natively, so they are a great way to package applications for deployment.

This repo contains a Dockerfile in the project root that builds a Docker image
for the app. **Have a look!** We:

* choose a base image
* install `uv`
* copy over all the important project files
* `sync` the environment
* expose a port
* define a command to run the app

### Triggering a Docker Build

We can use continuous integration to build and push Docker images too.
First we have to enable the appropriate permissions on our repository, via
Settings:

* Open your repository on GitHub and go to **Settings**
* Under **Actions**, choose **General**
* Under **Workflow permissions**, select **Read and write permissions** and click **Save**

![Changing GH Action permissions](assets/action-permissions.png)

Now let's add a new GitHub Action to do this. Building Docker images is another
common task, so we might go back to the
[GitHub Actions Marketplace](https://github.com/marketplace?type=actions) for a
suitable "Docker" workflow.

We can see the *Build and push Docker images* action and the examples within
to create a new workflow for ourselves.

**Drag the `container.yaml` file from the `complete/` directory into `.github/workflows/` in the file explorer or create your own from scratch**

Once dragged in, commit and push the changes to GitHub to trigger the workflow.
When it finishes, your image appears under **Packages** on your repository's page on GitHub.
We'll look at using this Docker image in the next session.

Note that we added these permissions:

```yaml
permissions:
  contents: read
  packages: write
```


# The End!
