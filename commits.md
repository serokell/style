<!--
 - SPDX-FileCopyrightText: 2021 Union (This guide originally created by Serokell <https://serokell.io/>)
 -
 - SPDX-License-Identifier: MPL-2.0
 -->

## Branches and pull requests

1. The `master` branch:
    1. The code in the `master` branch SHOULD always compile and pass all tests.
    2. The documentation in the `master` branch SHOULD always be up-to-date.

2. Issue branches:
    1. Naming scheme: `<username>/<issue_id>-<brief_description>`.
    2. Make sure the description is really brief but clear enough so that it is easy to understand what the issue is about without looking it up in the issue tracker (assuming that you are familiar with existing issues).

    Examples:
    - `worm2fed/#1-example-issue`
    - `rexolion/#1234-ui-for-inputs`

3. Pull requests:
    1. Each pull request SHOULD resolve exactly one issue.
    2. Try to avoid too big PRs. If your PR has more than 500 additions + deletions, please think twice whether it can be split into smaller PRs.
    3. PR title SHOULD adhere to the following scheme: `[ISSUE-ID] Brief description`.
    4. PR description MUST NOT be empty. If there is a PR template, PR description MUST adhere to this template. If there is no template, please describe the changes you've made and **why** you've made them. Also, provide links to related issues.
    5. You SHOULD request a review from at least one person you think would be most suitable as reviewers. You MAY request a review from more people if the changes are substantial – use your discretion.
    6. Keep in mind that review might be automatically requested from codeowners when you actually create a PR. Make sure to review list of reviewers after pressing the `Create pull request` button.

## Commit history

1. An ideal commit history should satisfy the following criteria:
    1. Each commit MUST adhere to the commit guidelines (#3).
    2. Each commit SHOULD be a minimal and accurate answer to exactly one identified and agreed problem.
    3. Each commit SHOULD NOT fix a problem introduced by an earlier commit in your pull request or revert an earlier commit in your PR.
    4. Each commit SHOULD be signed.
    5. Your pull request generally shouldn't contain changes unrelated to the issue it solves. However, if you modify some file and see an obvious small mistake there (e. g. a typo or wrong formatting), you MAY fix it in your PR.

2. Upon submitting a pull request, ensure the commit history satisfies the criteria listed in 2.1.

3. During the PR review:
    1. Make changes in separate commits, even if such commits do not comply with 2.1 or 3.
    2. You MUST NOT amend the previous commits.
    3. You SHOULD NOT use force-push unless you're pushing the same commits rebased on the newer version of the target branch.
    4. You SHOULD periodically synchronize your issue branch with the target branch.
    5. You SHOULD use rebase to synchronize the branches.
    6. You SHOULD NOT use "Update branch" or merge to synchronize the branches unless rebasing is prohibitively hard.

4. After passing the review but before merging:
    1. Rebase your changes on the target branch.
    2. Use the interactive rebase to prettify the commit history so that it adheres to 2.1.

5. Merging the changes:
    1. If you want to merge someone else's PR, you SHOULD make sure the author is fine with merging and does not want to do anything with the commit history.
    2. You MUST create a merge commit, i.e. you MUST NOT use neither fast-forward merge nor "squash and merge".

## Commit messages

1. The rules in this section apply to:
    1. Commits that a PR contains upon submission.
    2. Commits that are going to be merged into the target branch.

2. Each commit message MUST have a subject line (header) and a body, separated with a blank line.

3. Subject line:
    1. Prefix the subject line with IDs of all relevant issues: `[#1] Title` if they are accessible by all people with access to the repository.
    2. Start with an uppercase letter
    3. Try to limit the subject line to 50 characters; the hard limit is 72 characters
    4. Do not end the subject line with a period
    5. Use the imperative mood in the subject line

4. Message body:
    1. Start with `Problem:` and describe a problem that the commit solves
    2. Continue with `Solution:` and describe how the commit addresses the problem
    3. Add additional context if you wish

      Examples:
      - GOOD:
          ```
          [#453] Switch from avada-kedavra to expelliarmus

          Problem: `avada-kedavra` library that we use to kill unwanted
          process instances has a negative side-effect: it tears
          programmers' souls apart. There's currently no mitigation
          of this issue according to: (link to the developer issue
          tracker).

          Solution: Use a simpler `expelliarmus` library that blocks
          process instances from performing any effects. Garbage-collect
          blocked processes using the `kill` syscall.

          ```
      - BAD:
          - `[#453] Switch from avada-kedavra to expelliarmus`
          - `[#453] Change the killer lib`
          - `[#453] Get rid of avada-kedavra in favor of expelliarmus because the former has some negative side-effects that are too long to explain in the commit header`
          - ```
            [#453] Update killer
            Problem: we need a new lib
            Solution: switch to the new lib`
            ```
