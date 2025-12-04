# Git Tips and Tricks

This guide covers essential git configurations, workflow optimizations, and best practices to help you work more efficiently.

---

## Configuration

Essential git configurations to set up your environment for optimal workflow.

### Rebase vs Merge

> **💡 Tip:** Configure `git pull` to use rebase instead of merge in your global git config to maintain a cleaner commit history.

```ini
[pull]
    rebase = true
```

### Using Aliases

Aliases help you optimize your workflow and reduce repetitive typing. You can configure them at a user level (`~/.gitconfig`) or at a project level (`.git/config`).

#### Git Aliases

> **💡 Tip:** Use aliases to optimize your workflow. This can be done at a user level (`~/.gitconfig`) or at a project level (`.git/config`).

**Example:** Filter commits by author

```ini
[alias]
   my = "log --grep=thibaud.szymczak@datadoghq.com"
```

This alias lets you view only your changes with `git my`.

**Example:** Creating a branch with your namespace

To pass arguments to an alias, set it as a bash function:

```ini
[alias]
  create = "!f() { git checkout -b thibaud.szymczak/\"$1\";}; f"
```

Usage:
```bash
git create feature-1
```

This will create a branch called `thibaud.szymczak/feature-1`.

#### Shell Aliases

Shell aliases are more flexible and work best when you want to chain commands. [ZSH already provides many out of the box](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/git).

Enable ZSH git aliases with:

```bash
# .zshrc
plugins=(git)
```

**Example shell aliases:**

```bash
# Shorthand for git command
alias g="git"

# Remove any existing gco alias to avoid conflicts
unalias gco

# Checkout using ngit-cli tool
alias gco="ngit-cli checkout"

# Stage all changes and commit with a message
alias gcam="git add . && git commit -m"

# Pull from origin preprod branch
alias gpop="git pull origin preprod"

# Checkout preprod branch and pull latest changes
alias gcop="git checkout preprod && gpop"

# Quick work-in-progress commit bypassing pre-commit hooks
alias gcwip="git add . && git commit -m 'wip' --no-verify"

# Add all changes and amend to last commit without editing message
alias gane="git add . && git commit --amend --no-edit"

# Pull from origin for the current branch
alias gpb="git pull origin \$(git rev-parse --abbrev-ref HEAD)"
```

> **💡 Tip:** Making aliases is now easier than ever with AI.

---

**Quick Reference:**
- `git pull` with rebase: Add `[pull] rebase = true` to `.gitconfig`
- Create aliases in `~/.gitconfig` or `.zshrc`
- Use `!f() { ... }; f` syntax for git aliases with arguments

---

## Workflow Optimization

Tools and techniques to streamline your daily git operations.

### Choosing the Right Tool

#### When to Use the Terminal

Using the terminal has multiple advantages:
- Use it everywhere - you are not dependent on a specific environment/IDE
- Most flexible way of using git - you are not limited by UI restrictions
- Extremely customizable (see sections below)
- Best way to learn how git works

#### When to Use an IDE

Sometimes the terminal is not the best tool for the job. These are tasks where an IDE (like WebStorm) excels:
- Git conflict resolutions
- Checking diffs/changes
- Squashing commits
- Git blame
- "Dropping commits" (removing commits from history)

### Going Further - Build Your Own CLI Tool

Make git work the way you want!

[This is mine](https://github.com/thibaudszy/ngit-cli) - ngit-cli

![ngit-cli branch overview](images/ngit-cli-branches.png)

### Use "-" for Previous Reference

Works similarly to `-` in the `cd` command. In git, this is a reference to the previous ref.

**Examples:**
 - `git cherry-pick` - cherry-pick commits from the previously checked out branch onto the current branch
- `git rebase -` - rebase the current branch on the previous ref. Generally used after switching branches

### Speed Up Your Git Pulls

Add this line to your list of cronjobs to automatically fetch the latest changes:

```bash
*/15 * * * * cd ~/dd/web-ui && git fetch origin preprod
```

This performs a fetch of preprod every 15 minutes, so when you pull, the changes are already locally cached.

---

## Best Practices

Strategies for avoiding common issues and maintaining a smooth workflow.

### Avoiding and Dealing with Conflicts (in web-ui)

#### Avoiding Conflicts

- Keep your branches up to date (use the `gcop` and `gpop` aliases)
- When working on stacked PRs, use a tool like graphite to sync the changes, or do it manually but don't forget
- Avoid long-lived branches and big PRs. Prefer smaller changes and hide WIP features behind feature flags

#### Dealing with Conflicts

> ⚠️ **Note:** Conflicts in auto-generated files like `pnp.cjs` and `yarn.lock` should be resolved by accepting the changes from `preprod` (or the shared branch) and re-running `yarn` locally.

- Conflicts in auto-generated files generally don't matter
- Jetbrains IDEs have a good UI to resolve conflicts

---

## Other useful concepts/commands
 - learn how to navigate branches with the HEAD pointer
 - `git cherry pick` - cherry pick a commit onto your current branch
 - `git bisect` - very useful to find when a bug started
 - `git revert` - undo a change while preserving history
 - `git log` - we all know it but it is good to learn how to customize the output. For example: `git log -1 --name-only` to view the files affected by the last commit

## Resources

Additional learning materials to deepen your git knowledge.

> **Note:** These are only the resources that I have used and have helped me. They only cover a small portion of git features (with the exception of the official docs).

### Documentation
- [git docs](https://git-scm.com/docs) - duh
- [Atlassian git docs](https://www.atlassian.com/git/tutorials) - less thorough than the official docs but made by people not allergic to CSS

### Interactive Learning
- [Oh my git](https://ohmygit.org/) - Git game to learn git interactively

### Videos
- [So you think you know git](https://www.youtube.com/watch?v=aolI_Rz0ZqY&pp=ygUSeW91IGRvbid0IGtub3cgZ2l0) - talk by the cofounder of github on advances and new(ish) git features
- [So you think you know git - part 2](https://www.youtube.com/watch?v=Md44rcw13k4)
- [13 Advanced (but useful) Git Techniques and Shortcuts](https://www.youtube.com/watch?v=ecK3EnyGD8o) - description is in the title of the video
