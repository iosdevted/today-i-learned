# Git Commit Message Style Guide

To create a useful revision history, teams should first agree on a commit message convention to use. This also applies to personal projects.

## Message Structure

A commit messages consists of three distinct parts separated by a blank line: the title, an optional body and an optional footer. The layout looks like this:

```git
type: Subject

body

footer
```

The type is contained within the title and can be one of these types:

- feat: A new feature
- fix: A bug fix
- docs: Changes to documentation
- style: Formatting, missing semi colons, etc; no code change
- refactor: Refactoring production code
- test: Adding tests, refactoring test; no production code change
- chore: Updating build tasks, package manager configs, etc; no production code change

### Subject

Subjects should be no greater than 50 characters, should begin with `a capital letter` and do not end with a period.

Use an imperative tone to describe what a commit does, rather than what it did. For example, use change; not changed or changes.

### Body

Not all commits are complex enough to warrant a body, therefore it is optional and only used when a commit requires a bit of explanation and context. Use the body to explain the what and why of a commit, not the how.

When writing a body, the blank line between the title and the body is required and you should limit the length of each line to no more than 72 characters.

### Footer

The footer is optional and is used to reference issue tracker IDs.

## Examples

- Commit message with description and breaking change footer

```git
docs: Edit README.md to include New Features Use-Cases
```

```git
feat: Allow provided config object to extend other configs

BREAKING CHANGE: `extends` key in config file is now used for extending other config files
```

- Commit message with ! to draw attention to breaking change

```git
refactor!: Drop support for Node 6
```

- Commit message with both ! and BREAKING CHANGE footer

```git
refactor!: Drop support for Node 6

BREAKING CHANGE: Refactor to use JavaScript features not available in Node 6.
```

- Commit message with no body

```git
docs: Correct spelling of CHANGELOG
```

- Commit message with scope

```git
feat(lang): Add polish language
```

- Commit message with multi-paragraph body and multiple footers

```git
fix: Correct minor typos in code

see the issue for details

on typos fixed.

Reviewed-by: Z
Refs #133
```

## References

[Udacity Git Commit Message Style Guide](https://udacity.github.io/git-styleguide/)

[How to Write Good Commit Messages: A Practical Git Guide](https://www.freecodecamp.org/news/writing-good-commit-messages-a-practical-guide/)

[Write joyous git commit messages](https://joshuatauberer.medium.com/write-joyous-git-commit-messages-2f98891114c4)

[Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)