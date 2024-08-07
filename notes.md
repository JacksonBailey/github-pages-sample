The final step in the process hopefully. The solution seems to be to use the
actual jekyll-build-pages image. I make a new GitHub action and got the actual
command being ran. I then chopped it up to get this:

```
docker pull ghcr.io/actions/jekyll-build-pages:v1.0.12

docker run \
  --workdir /github/workspace \
  --rm \
  -e INPUT_SOURCE=./ \
  -e INPUT_DESTINATION=./_site \
  -e INPUT_FUTURE=false \
  -e INPUT_BUILD_REVISION=$(git rev-parse HEAD) \
  -e INPUT_VERBOSE=true \
  -e GITHUB_WORKSPACE=/github/workspace \
  -v "$HOME/Code/github-pages-sample":"/github/workspace" \
  ghcr.io/actions/jekyll-build-pages:v1.0.12
```

`GITHUB_WORKSPACE` needs to be set (it is set to the workdir) because some
commands in the entrypoint rely on it being there. The input and output
destinations are relative to it. If they aren't there it tries to do stuff from
root.

To debug, I ran this. The same command but with `-it --entrypoint bash`. You
will need to install vim. You can directly modify Ruby files and add lines like
`p some_variable` to print information. It gets newlines removed in the final
output but it's more useful than nothing. I could not figure out interactive
debugging.

```
docker run \
  --workdir /github/workspace \
  --rm \
  -e INPUT_SOURCE=./ \
  -e INPUT_DESTINATION=./_site \
  -e INPUT_FUTURE=false \
  -e INPUT_BUILD_REVISION=$(git rev-parse HEAD) \
  -e INPUT_VERBOSE=true \
  -e GITHUB_WORKSPACE=/github/workspace \
  -v "$HOME/Code/github-pages-sample":"/github/workspace" \
  -it --entrypoint bash \
  ghcr.io/actions/jekyll-build-pages:v1.0.12

apt-get install vim
```

For reference, this is the official pages-gem image but it does not seem to work
super well: `ghcr.io/github/pages-gem:v231`. Also, while the official process
uses the official gem, it does not use the official docker image of the official
gem.

The full, unedited command:

```
docker run \
  --name ghcrioactionsjekyllbuildpagesv1012_c7ec62 \
  --label 5f9cf7 \
  --workdir /github/workspace \
  --rm \
  -e "GITHUB_PAGES" \
  -e "INPUT_SOURCE" \
  -e "INPUT_DESTINATION" \
  -e "INPUT_FUTURE" \
  -e "INPUT_BUILD_REVISION" \
  -e "INPUT_VERBOSE" \
  -e "INPUT_TOKEN" \
  -e "HOME" \
  -e "GITHUB_JOB" \
  -e "GITHUB_REF" \
  -e "GITHUB_SHA" \
  -e "GITHUB_REPOSITORY" \
  -e "GITHUB_REPOSITORY_OWNER" \
  -e "GITHUB_REPOSITORY_OWNER_ID" \
  -e "GITHUB_RUN_ID" \
  -e "GITHUB_RUN_NUMBER" \
  -e "GITHUB_RETENTION_DAYS" \
  -e "GITHUB_RUN_ATTEMPT" \
  -e "GITHUB_REPOSITORY_ID" \
  -e "GITHUB_ACTOR_ID" \
  -e "GITHUB_ACTOR" \
  -e "GITHUB_TRIGGERING_ACTOR" \
  -e "GITHUB_WORKFLOW" \
  -e "GITHUB_HEAD_REF" \
  -e "GITHUB_BASE_REF" \
  -e "GITHUB_EVENT_NAME" \
  -e "GITHUB_SERVER_URL" \
  -e "GITHUB_API_URL" \
  -e "GITHUB_GRAPHQL_URL" \
  -e "GITHUB_REF_NAME" \
  -e "GITHUB_REF_PROTECTED" \
  -e "GITHUB_REF_TYPE" \
  -e "GITHUB_WORKFLOW_REF" \
  -e "GITHUB_WORKFLOW_SHA" \
  -e "GITHUB_WORKSPACE" \
  -e "GITHUB_ACTION" \
  -e "GITHUB_EVENT_PATH" \
  -e "GITHUB_ACTION_REPOSITORY" \
  -e "GITHUB_ACTION_REF" \
  -e "GITHUB_PATH" \
  -e "GITHUB_ENV" \
  -e "GITHUB_STEP_SUMMARY" \
  -e "GITHUB_STATE" \
  -e "GITHUB_OUTPUT" \
  -e "RUNNER_OS" \
  -e "RUNNER_ARCH" \
  -e "RUNNER_NAME" \
  -e "RUNNER_ENVIRONMENT" \
  -e "RUNNER_TOOL_CACHE" \
  -e "RUNNER_TEMP" \
  -e "RUNNER_WORKSPACE" \
  -e "ACTIONS_RUNTIME_URL" \
  -e "ACTIONS_RUNTIME_TOKEN" \
  -e "ACTIONS_CACHE_URL" \
  -e "ACTIONS_ID_TOKEN_REQUEST_URL" \
  -e "ACTIONS_ID_TOKEN_REQUEST_TOKEN" \
  -e "ACTIONS_RESULTS_URL" \
  -e GITHUB_ACTIONS=true \
  -e CI=true \
  -v "/var/run/docker.sock":"/var/run/docker.sock" \
  -v "/home/runner/work/_temp/_github_home":"/github/home" \
  -v "/home/runner/work/_temp/_github_workflow":"/github/workflow" \
  -v "/home/runner/work/_temp/_runner_file_commands":"/github/file_commands" \
  -v "/home/runner/work/github-pages-sample/github-pages-sample":"/github/workspace" \
  ghcr.io/actions/jekyll-build-pages:v1.0.12
```
