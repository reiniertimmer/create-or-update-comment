# Action to create or update a comment

It is generally pretty straightforward to push a message to a GitHub issue or pull request.

However, in CI systems like GitHub Actions, on re-runs, this may actually lead to a number of duplicate messages. For example, if your CI
posts a message on every run, and you need a number of re-runs, this will effectively mean that your PR will have a large number of similar
messages.

This action can take care of that.

## What does this action do

On the first run, this action will create a new message, just like you would using an action like `peter-evans/create-or-update-comment`.
(Actually, this is a composite action that is implemented to use that particular action under the hood.)

On subsequent runs however, the action will not create a new message, but it will update the original message with new content.
So, you will end up with only one message for a certain subject.

## How does it work

This action works by appending some invisible marker at the start of a comment, e.g.

```
<!-- marker -->
This is the actual message content.
```

The HTML comment tags are not shown in the message, and you only see the actual message content.

On a subsequent run, the action will use `peter-evants/find-comment` to find a message that starts with your configured marker. If found,
that comment will be updated. If not found, then a new message will be created

## Usage

| Input  | Required | Default | Description |
| ------ | -------- | ------- | ----------- |
| token  | no | `${{ github.token }}` | The token used to post comments |
| repository  | no  | `${{ github.repository }}` | The repository to post comments (defaults to current repository) |
| issue-number | **yes** | - | The issue or pull request number to post on |
| body | **yes** | - | The message body to post |
| marker | **yes** | - | The marker message to track your message |

Example usage:

```yaml
  - uses: reiniertimmer/create-or-update-comment@master
    with:
      marker: marker
      issue-number: ${{ github.event.pull_request.number }}
      body: This is a tracked message
```
