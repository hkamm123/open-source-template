# Setup guide

This guide takes you from a fresh clone to {{PROJECT_NAME}} running on your machine.

<!--
One or two sentences on what the pieces are (for example "a React UI built
with Vite and a Node API"), so a newcomer knows what they're about to run.

Write each section as numbered steps, each with the exact command and a
checkpoint that shows it worked ("You should see ..."). List a section that
isn't written yet in Sections with "(not written yet)" and the issue that
tracks it, instead of leaving it out.
-->

## Sections

1. [Before you start](#before-you-start)
2. [Get the code](#get-the-code)
3. [Run the project locally](#run-the-project-locally)
4. [Run the tests](#run-the-tests)
5. [When things go wrong](#when-things-go-wrong)

## Before you start

You need:

- [Git](https://git-scm.com/downloads)
- {{TOOLS_AND_VERSIONS}}

<!-- Give the supported versions and the command to check each one, for example "Run `node --version` to check." -->

## Get the code

1. Clone the repository and move into it:

   ```sh
   git clone https://github.com/{{OWNER}}/{{REPO}}.git
   cd {{REPO}}
   ```

   If you plan to open a pull request, [fork the repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo) first and clone your fork instead (see [the pull request process](../CONTRIBUTING.md#the-pull-request-process)).

The commands in each section below start from this folder, the root of the repository.

## Run the project locally

1. Install the dependencies:

   ```sh
   {{INSTALL_COMMAND}}
   ```

2. Start the project:

   ```sh
   {{RUN_COMMAND}}
   ```

3. Check that it's up. <!-- The checkpoint: a URL to open or a command to run, and exactly what it shows. -->

## Run the tests

Run these before you open a pull request. The [definition of done](git-standards.md#definition-of-done) requires all of them to pass.

```sh
{{CHECK_COMMANDS}}
```

<!-- One line per command (tests, lint, type check), with a sentence on what each checks. -->

## When things go wrong

<!--
One bullet per problem people actually hit, written as the symptom they see
in bold, then the cause and the fix. For example:

- **`npm install` fails.** Check that you're in the right folder and that
  `node --version` shows a supported version (see Before you start).

Add an entry whenever someone asks for help with setup.
-->
