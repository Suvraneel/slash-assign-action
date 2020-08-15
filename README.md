<h3 align="center">✋💪</h3>
<h3 align="center">[WIP] `/assign` action</h3>

<p align="center"><a href="https://github.com/JasonEtco/slash-assign-action"><img alt="GitHub Actions status" src="https://github.com/JasonEtco/slash-assign-action/workflows/CI/badge.svg"></a> <a href="https://codecov.io/gh/JasonEtco/slash-assign-action/"><img src="https://badgen.now.sh/codecov/c/github/JasonEtco/slash-assign-action" alt="Codecov"></a></p>

---

A GitHub Action that listens for a `/assign` "command" (an issue comment that starts with `/assign`) and assigns the commenter to the issue. It can also unassign issues that have been assigned for a configured amount of time.

## Usage

```yaml
name: Slash assign

on:
  schedule:
    cron: 0 0 * * *
  issue_comment:
    types: [created]

jobs:
  assign:
    if: ${{ github.event_name == issue_comment }} && ${{ startsWith(github.event.comment.body, "/assign") }}
    runs-on: ubuntu-latest
    steps:
      - name: Assign the user
        uses: JasonEtco/slash-assign-action@v1
        with:
          required_label: good-first-issue
          assigned_label: assigned-to-contributor

  unassign:
    if: ${{ github.event_name == schedule }}
    runs-on: ubuntu-latest
    steps:
      - name: Unassign stale issues
        uses: JasonEtco/slash-assign-action@v1
        with:
          assigned_label: assigned-to-contributor
```

## Options

#### `assigned_label`

A label that is added to issues when they're assigned, to track which issues were assigned by this action. Default: `slash-assigned`

#### `required_label`

If set, the issue must have this label to be assigned.

#### `days_until_warning`

The span of time (in days) between a user assigning themselves to the action commenting saying it will become unassigned. Default: `14`

#### `days_until_unassign`

The span of time (in days) between a warning (see `days_until_warning`) and the issue being unassigned automatically. Default: `7`

#### `stale_assignment_label`

The label applied when the assignment is stale (>= `days_until_warning`). Default: `stale-assignment`.

#### `pin_label`

A label that prevents the user from being unassigned, typically for issues that are expected to take a long time. Default: `pinned`.

#### `assigned_comment`

The comment posted after a user has assigned themselves to an issue. This is a Mustache template that supports the following variables:

* `inputs` (the inputs given to the action)
* `comment` (an object of the comment that was created)
* `totalDays` (`days_until_warning` + `days_until_unassign`)
* `env` (`process.env`, anything you pass to the action via `env`)

Default:

```
This issue [has been assigned]({{ comment.html_url }}) to {{ comment.user.login }}!

It will become unassigned if it isn't closed within {{ totalDays }} days. A maintainer can also add the **{{ inputs.pin_label }}** label to prevent it from being unassigned.
```

#### `warning_comment`

The comment posted to warn a user that the issue will become unassigned. This is a Mustache template that supports the following variables: 

* `user` (the user that was assigned)
* `inputs` (the inputs given to the action)</li> <li>
* `env` (`process.env`, anything you pass to the action via `env`)

Default:

```
@{{ assignee.login }}, this issue hasn't had any activity in {{ inputs.days_until_warning }} days. It will become unassigned in {{ inputs.days_until_unassign }} days to make room for someone else to contribute.
```
