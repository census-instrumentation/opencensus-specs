# Building on Kokoro

Kokoro is build infrastructure based on jenkins. This document describes how to configure Kokoro
builds for continuous integration as well as for pull requests.

There are fours steps to have builds running on Kokoro.
1. Job definition
2. Build Configuration
3. Build Script
4. Automatic Trigger


## Job Definition
The job definition resides in Kokoro infrastructure. Refer to Kokoro
documentation for details on job definition.

### Example
Here is a sample presubmit job defition for linux platform.
* File Name: linux.cfg
* File Content:
```
type: PRESUBMIT_GITHUB

cluster: GCP_UBUNTU
pool: "dynamic"

scm {
  github_scm {
    owner: "census-instrumentation"
    repository: "opencensus-java"
    name: "opencensus-java"
    presubmit_branch_regex: ".*"
    build_config_dir: "buildscripts/kokoro"
    commit_status_context: "Linux"
    commit_status_url: LOGGING_URL
    as_if_merged: true
    use_webhook: true
  }
}
```

## Build Configuration
The purpose of the build configuration is to tell kokoro how to build your
project. It includes (but not limited to ) reference to buildscript location
(build_file), build timeout value (timout_mins), etc.
For detail description please refer to Kokoro documentation.

The build configuration file resides in the directory specified by the field
'build_config_dir' in job definition (see above). The name of the build
configuration file should be same as the job definition.

### Example
File Name: linux.cfg
File Content:

```
# Config file for internal CI

# Location of the continuous shell script in repository.
build_file: "opencensus-java/buildscripts/kokoro/linux.sh"
timeout_mins: 60
```

## Build Script
The buildscript is the script that runs to perform various build tasks.
It is very specific to each project. 

## Automatic Trigger
In order to trigger an automatic build when pull request is created/updated
(presubmit job) OR a commit is made (continuous integration job) Kokoro requires
a webhook. Please add kokoro-team as admin of the repo and execute a command to
create a webhook. Please refer to Kokoro documentation for this command.

