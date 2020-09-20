# wait-for-check-action

GitHub action that waits for a check and makes the check result accessible.

## Usage

```yaml
steps:
  - name: Wait for CI to succeed
    uses: jgautsch/wait-for-check-action@v0.0.1
    id: wait-for-ci
    with:
      token: ${{ secrets.GITHUB_TOKEN }}
      checkName: ci
      ref: ${{ github.event.pull_request.head.sha || github.sha }}

  - name: Do something with a passing build
    if: steps.wait-for-ci.conclusion == 'success'

  - name: Do something with a failing build
    if: steps.wait-for-ci.conclusion == 'failure'
```

## Inputs

This Action accepts the following configuration parameters via `with:`

- `token`

  **Required**

  The GitHub token to be used when making API requests. Typically, this would be set to `${{ secrets.GITHUB_TOKEN }}`

- `checkName`

  **Required**

  The name of the GitHub check to wait for. For example, `build` or `deploy`.

- `ref`

  **Default: `github.sha`**

  The Git ref of the commit you want to poll for a passing check.

  _TIP: You may want to use `github.pull_request.head.sha` when working with Pull Requests._

- `repo`

  **Default: `github.repo.repo`**

  The name of the Repository you want to poll for a passing check.

- `owner`

  **Default: `github.repo.owner`**

  The name of the Repo's owner you want to poll for a passing check.

- `timeoutSeconds`

  **Default: `600`**

  The number of seconds to wait for the check to complete. If the check does not complete within this amount of time, this Action will emit a `conclution` value of `timed_out`.

- `intervalSeconds`

  **Default: `10`**

  The number of seconds to wait before each poll of the GitHub API for checks on this commit.

## Outputs

This Action emits a single output named `conclusion`. Like the field of the same name in the CheckRunEvent API Response, it may be one of the following values:

- `success`
- `failure`
- `neutral`
- `timed_out`
- `action_required`

These correspond to the `conclusion` state of the Check you're waiting on. In addition, this action will emit a conclusion of `timed_out` if the Check specified didn't complete within `timeoutSeconds`.
