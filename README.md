# Template: Deploy anonystat on Deno Deploy

This template repo manages a deployment of
[anonystat](https://github.com/h4l/anonystat) on Deno Deploy.

To use it, press GitHub's "Use this template" button from the repo's main page,
then select "Create a new repository".

## Set up

After creating your copy of this repo, set it up following these steps:

1. Create a Deno Deploy project to deploy into
   - Go to https://dash.deno.com/
   - Press the "New Project" button
   - Select your copy of the repo to link it with Deno Deploy
   - When prompted to build step options, select the "**Just link the repo, I’ll
     set up GitHub Actions myself**" checkbox
2. Configure anonystat in Deno Deploy

   - Edit [anonystat-config.jsonc](anonystat-config.jsonc)
   - Run `deno task config` to validate your config file and print it as
     environment variables:

     ```console
     $ deno task config
     ANONYSTAT_USER_ID_SCRAMBLING_SECRET=00000000-0000-0000-0000-000000000000
     ANONYSTAT_DATA_STREAM_IN_MEASUREMENT_ID=foo
     ANONYSTAT_DATA_STREAM_IN_API_SECRET=secret123
     ANONYSTAT_DATA_STREAM_OUT_MEASUREMENT_ID=example-id
     ANONYSTAT_DATA_STREAM_OUT_API_SECRET=example-secret
     ```

   - Set these environment variables under your project's settings at
     https://dash.deno.com/

3. Edit [.github/workflows/deploy.yml](.github/workflows/deploy.yml)
   - Inside the `with:` properties of the `uses: denoland/deployctl@v1`. Set the
     `project: "<YOUR PROJECT NAME HERE>"` entry to the name or ID of the Deno
     Deploy project you created in step 1.

4. Push your changes to your copy of this repo. The deploy Action should run
   successfully and create a new deployment in your Deno Deploy project.

## Ongoing Use

After setting up the repo, it serves two purposes:

- It provides a record of your configuration (except perhaps secrets, which you
  can set locally in `.env` / `.envrc` and load using the direnv tool, or
  however you prefer).
- It provides a record of the version of anonystat you have deployed, and a way
  to update & redeploy it.

You update anonystat by bumping the the git revision of the `./anonystat`
submodule. By default GitHub's dependabot will make PRs to do this automatically
because there's a config file at
[.github/workflows/dependabot.yml](.github/workflows/dependabot.yml).

## Note: Deploys don't change configuration

Although `anonystat-config.jsonc` is in this repo, it's not deployed by the
`deploy.yml` Action — that just deploys code changes. This is because:

- Deploying secrets as environment variables should be more secure than a file
  alongside code
- We assume configuration won't change frequently enough that manually updating
  it be a burden
- The deployctl GitHub Action doesn't support setting environment variables when
  deploying.
  - the deployctl command line program can set environment variables, but that
    doesn't support OIDC auth, so it requires extra steps to configure it to
    work in a GitHub Actions CI job.

You can configure from a deployed file by using the following env var options:

```
ANONYSTAT_CONFIG_SOURCE=file
ANONYSTAT_CONFIG_FILE=anonystat-config.jsonc
```

And modify the `build` task to create a config file in the `anonystat/dist` dir.
