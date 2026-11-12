# Setting up a project from this template

GitHub copies this repo's files into a new repository, but not its labels, settings, rulesets or project board. This checklist covers those too. Work through it once, then delete this file.

## 1. Create the repository

Click **Use this template** on GitHub, or:

```sh
gh repo create OWNER/REPO --template hkamm123/open-source-template --public --clone
cd REPO
```

## 2. Replace the placeholders

Every placeholder looks like `{{NAME}}`. These four appear in many files, so replace them in one go (edit the values first):

```sh
grep -rlF '{{' --exclude-dir=.git --exclude=TEMPLATE_SETUP.md . | xargs sed -i \
  -e 's/{{PROJECT_NAME}}/My Project/g' \
  -e 's/{{OWNER}}/my-github-user-or-org/g' \
  -e 's/{{REPO}}/my-repo/g' \
  -e 's/{{CONTACT_EMAIL}}/maintainer@example.com/g'
```

On macOS, use `sed -i ''` instead of `sed -i`.

Then find the rest and fill them in by hand:

```sh
grep -rnF '{{' --exclude-dir=.git --exclude=TEMPLATE_SETUP.md .
```

| Placeholder | What goes there | Where |
|---|---|---|
| `{{PROJECT_NAME}}` | The project's display name | Most files |
| `{{OWNER}}`, `{{REPO}}` | The GitHub owner and repository name, as in `github.com/OWNER/REPO` | Most files |
| `{{CONTACT_EMAIL}}` | A private address for security and Code of Conduct reports | Code of Conduct, issue forms, contributing guide, communication norms |
| `{{PROJECT_BOARD_URL}}` | The project board's URL (see step 5) | README, contributing guide, roadmap |
| `{{LICENSE_NAME}}` | The license's name, e.g. "MIT" or "AGPL v3.0" | README, contributing guide |
| `{{ONE_SENTENCE_DESCRIPTION}}`, `{{STATUS}}` | What the project is, and where it stands | README |
| `{{TOOLS_AND_VERSIONS}}`, `{{INSTALL_COMMAND}}`, `{{RUN_COMMAND}}` | What a contributor needs installed, and how to install and run the project | README, setup guide |
| `{{CHECK_COMMANDS}}` | The test, lint and type-check commands: one per line in the setup guide's code block, and as a sentence in the git standards, e.g. `` `npm test`, `npm run lint`, and `npm run typecheck` `` | Setup guide, git standards |
| `{{AUTHORS}}`, `{{REVIEWERS}}`, `{{DATE}}`, `{{MONTH_YEAR}}` | Design doc and roadmap details | `docs/design.md`, `docs/roadmap.md` |

The files also have `<!-- comments -->` with guidance. They don't show on GitHub; delete them as you fill each section in.

## 3. Add a license

Pick one at [choosealicense.com](https://choosealicense.com/), then download its text (this example uses MIT):

```sh
gh api /licenses/mit --jq .body > LICENSE
```

Fill in the year and name if the license has them, and say why you chose it in the README's License section.

## 4. Create the labels

The issue forms and the [contributing guide](CONTRIBUTING.md#labels) use these labels on top of GitHub's defaults:

```sh
gh label create "needs triage" --color 4895f4 --description "A maintainer hasn't looked at it yet" --force
gh label create task           --color 24e741 --description "A segment of planned work" --force
gh label create epic           --color f24634 --description "A large section of work that includes multiple sub-issues" --force
gh label create current        --color eb1978 --description "This is the current priority" --force
gh label create next           --color ebe278 --description "What we'll prioritize after the current issues are done" --force
```

Add project-specific labels (for example, one that marks a milestone's thinnest slice) to the table in `CONTRIBUTING.md` too.

## 5. Create the project board

1. Create the board and its Size field:

   ```sh
   gh auth refresh -s project   # only needed once
   gh project create --owner OWNER --title "PROJECT NAME"
   gh project field-create NUMBER --owner OWNER --name Size --data-type SINGLE_SELECT --single-select-options "XS,S,M,L,XL"
   ```

   `NUMBER` is the project number that `gh project create` prints.

2. On GitHub, open the board and:
   - Change the Status field's options to **Backlog, Ready, In Progress, In Review, Done**.
   - Switch the view to **Board**, grouped by Status.
   - Under **Workflows**, turn on "Auto-add to project" (for this repo), and make sure "Item closed" and "Pull request merged" set Status to Done.
   - Link the board to the repository (**Settings → Manage access**, or the repo's **Projects** tab).

3. Put the board's URL in place of `{{PROJECT_BOARD_URL}}`.

## 6. Match the repository settings to the git standards

The [git standards](docs/git-standards.md) say PRs are squash-merged by a maintainer, the PR title becomes the commit title, and every PR needs a review.

```sh
gh repo edit OWNER/REPO \
  --enable-squash-merge --enable-merge-commit=false --enable-rebase-merge=false \
  --delete-branch-on-merge
gh api -X PATCH repos/OWNER/REPO \
  -f squash_merge_commit_title=PR_TITLE -f squash_merge_commit_message=PR_BODY
```

Protect `main` with a ruleset that requires a pull request with one approval. Repository admins can bypass it, for the rare emergencies the git standards allow:

```sh
gh api -X POST repos/OWNER/REPO/rulesets --input - <<'EOF'
{
  "name": "Protect main",
  "target": "branch",
  "enforcement": "active",
  "bypass_actors": [
    { "actor_id": 5, "actor_type": "RepositoryRole", "bypass_mode": "always" }
  ],
  "conditions": { "ref_name": { "include": ["~DEFAULT_BRANCH"], "exclude": [] } },
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 1,
        "dismiss_stale_reviews_on_push": false,
        "require_code_owner_review": false,
        "require_last_push_approval": false,
        "required_review_thread_resolution": false,
        "allowed_merge_methods": ["squash"]
      }
    }
  ]
}
EOF
```

On a free personal account, rulesets only apply to public repositories.

## 7. Write the project docs

| File | What to do |
|---|---|
| `README.md` | Describe the project, fill in the Quickstart, and delete the template note at the top. |
| `docs/setup.md` | Write the steps from a fresh clone to a running project, with a checkpoint after each. |
| `docs/design.md` | Fill in each section, or delete it if the project is small enough not to need a design doc (and remove its links from the task form and the git standards). |
| `docs/roadmap.md`, `docs/known-issues.md` | Leave the "None yet." lines until there's something to list. |
| `.github/ISSUE_TEMPLATE/*.yml` | Follow the `#` comments: add project-specific questions to the bug report, and list the project's kinds of users in the feature request. |

Add other docs (a data model, an MVP plan) to `docs/` as the project needs them, and add each one to the definition of done's docs list in `docs/git-standards.md`.

## 8. Finish

1. Delete this file:

   ```sh
   git rm TEMPLATE_SETUP.md
   ```

2. Check that no placeholders are left. This should print nothing:

   ```sh
   grep -rnF '{{' --exclude-dir=.git .
   ```

3. Commit and push.
