# Example

A demo of mani.

![example output of mani](../res/output.gif)

The demo is based on the following mani config.

`mani.yaml`
```yaml
projects:
  - name: example
    path: .
    description: A mani example

  - name: pinto
    path: frontend/pinto
    url: https://github.com/alajmo/pinto.git
    description: A vim theme editor
    tags: [frontend, node]

  - name: dashgrid
    path: frontend/dashgrid
    url: https://github.com/alajmo/dashgrid.git
    description: A highly customizable drag-and-drop grid
    tags: [lib, node]

  - name: template-generator
    url: https://github.com/alajmo/template-generator.git
    description: A simple bash script used to manage boilerplates
    tags: [cli, bash]

tasks:
  - name: git-status
    description: show working tree status
    command: git status

  - name: git-fetch
    description: fetch remote updates
    command: git fetch

  - name: git-prune
    description: remove local branches which have been deleted on remote
    env:
      remote: origin
    command: git remote prune $remote

  - name: git-switch
    description: switch branch
    env:
      branch: main
    command: git checkout $branch

  - name: git-create
    description: create branch
    command: git checkout -b $branch

  - name: git-delete
    description: delete branch
    command: git branch -D $branch

  - name: npm-install
    description: run npm install in node repos
    tags: [node]
    command: npm ci

  - name: npm-setup
    description: run npm install and build in node repos
    tags: [node]
    command: |
      npm ci
      npm build

  - name: git-daily
    description: show branch, local and remote diffs, last commit and date
    commands:
      - name: branch
        command: git rev-parse --abbrev-ref HEAD

      - name: status
        command: git status

      - name: local diff
        command: git diff --name-only | wc -l

      - name: remote diff
        command: |
          current_branch=$(git rev-parse --abbrev-ref HEAD)
          git diff "$current_branch" "origin/$current_branch" --name-only 2> /dev/null | wc -l

      - name: last commit
        command: git log -1 --pretty=%B

      - name: commit date
        command: git log -1 --format="%cd (%cr)" -n 1 --date=format:"%d  %b %y" | sed 's/ //'
```

Given the above `mani.yaml` we can run commands like:

```sh
# Initialize mani, any sub-directory that has a `.git` inside it will be included
mani init

# Sync repositories (will clone any repository that is not cloned yet)
$ mani sync

# List all projects
$ mani list projects

# Describe all tasks
mani describe tasks

# Get latest changes
$ mani run status --all-projects --output table

# Run custom ls command for projects with tag bash
$ mani exec 'ls' -t bash
```