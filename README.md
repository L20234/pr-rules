# pr-rules
Validates a syntax of the pull request title against specified regexes.

## Using the action
### Inputs
* github_token - GitHub authentication token, can be passed in as '${{ secrets.GITHUB_TOKEN }}'.
    * required: true
* title_regex - Semicolon separated list of Regex patterns to validate the pull request title against

    * required: true
    * NOTE: Do not use " or ' characters to wrap the string, pass as is (see example)
### Usage
#### Example:
~~~
name: "Pull Request Linter"
 
on:
  pull_request:
    types: ['opened', 'edited', 'reopened', 'synchronize']
 
jobs:
  execute:
    name: pr-rules-job
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - name: pr-linter-step
        uses: L20234/pr-rules@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          title_regex: ^\[([A-Z0-9]{1,6}-[0-9]{1,6}|BF|MERGE)\] ([a-zA-Z0-9 \!\?\'\"\=\.\,\(\)\_\-\+\>\<\/\@\:\$\%\&\*\#])+$
~~~
#### This would enforce following:
* Valid:
~~~
[M-1] valid Example of a feature PR title 1234=-+_.,!?'"()
[IQ4-101] Yet Another valid Example of a PR of a feature

[BF] Valid BugFix title
[HF] Valid HotFix title 1234=-+_.,!?'"()
~~~
* Invalid:
~~~
[IQ4-101]Invalid PR title because there is no space between a ticket and the beginning of the description
[I4-1]:Invalid because ':' is not a valid character
[IA-11] Title @#$%^&*{};\/~:` these are the characters that are not allowed
~~~
## Additional features
### Auto-labeler
* This Feature will auto add a label to the Pull Request on the multi-project repos. If branch contains a project prefix in its name, auto-labeler will workout project prefix from the branch name and use that as a label.
* Label properties like color and description can be changed later manually if required.
* Enabled by default for all repos
* Can be explicitly disabled by supplying workflow argument enable_labeler: false to a step 'run-pr-linter'

### File type checker
* This feature checks for files added in the PR and if binaries found, raise an error. This makes it so PR with binaries must be approved by an Administrator.
* Enabled by default for all repos
* Can be explicitly disabled by supplying workflow argument enable_file_checker: false to a step 'run-pr-linter'
