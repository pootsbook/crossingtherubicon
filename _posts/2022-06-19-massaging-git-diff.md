---
layout: post
title: "Massaging git diff"
---

Recently I’ve been working in an Elixir Phoenix application. It’s a legacy application (first commit: December 2015) which has seen it move through both Elixir and Phoenix versions since Elixir 1.1.0 and Phoenix 1.0.4 all the way to Elixir 1.13.4 and Phoenix 1.6.10.

One of the features that was introduced around the half-way point was `mix format`. This Mix task makes use of a code formatter to automate code style into a consistent format. Runnig this in one go was seen as a violation of the Git history and so much of the formatting has been happening incrementally since that point onwards. There is now a `mix format --check-formatted` task in CI which doesn’t reformat the code, but reports on only on violations. This means there is a divergence between a freshly minted file that is formatted with `mix format` and a file that has been manually massaged into a format that doesn’t report any violations. (This is arguably a failing of the formatter in dictating one unambigous style—the conversation has just been moved from one bike-shed to another, rather than shedding it altogether.)

This potential mismatch, paired with the desire to constrain commits and pull requests to only the changes related to the goal of the pull request itself, means that I only want to run `mix format` on the files that I have changed. And just to be difficult, technically only on the changes that I have made, rather than the whole file. I also want to see the diff between my changes and my changes formatted by `mix format`.

I was looking for an easy way to do this on the command line (bash). The first challenge was returning only the file names of files I had changed. And to ensure a diff between the `mix format` result and my edits, my edits are added to the Git staging area. The status therefore looks something like this:

```
$ git status
On branch demonstrate-diff
Your branch is up to date with 'origin/demonstrate-diff'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:    lib/demo/helpers.ex
        modified:    lib/demo/schemas/book.ex
        deleted:     lib/demo/schemas/codex.ex

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   lib/demo/workers/export.ex
```

The idea is to pick out the necessary file names and then apply `mix format` to each file. We can use the `--name-only` option on `git diff` to return the names:

```
$ git diff --name-only
```

This however returns all the names:

```
lib/demo/helpers.ex
lib/demo/schemas/book.ex
lib/demo/schemas/codex.ex
lib/demo/workers/export.ex
```

and we only want those from the staging area, the first three. To do that we can use the option `--cached`:

```
$ git diff --name-only --cached
lib/demo/helpers.ex
lib/demo/schemas/book.ex
lib/demo/schemas/codex.ex
```

The problem with returning everything from the staging area is that it will include all operations, including deleted files, which won’t succeed if passed to `mix format`. We need to turn to the `--diff-filter` option. I’m really only looking for new files that have added (`A`) or files that have been modified (`M`):

```
$ git diff --name-only --cached --diff-filter=AM
lib/demo/helpers.ex
lib/demo/schemas/book.ex
```

Now that we have the file names we need to pass those to `mix format`. This would normally be done with `xargs` or with a `bash` for-loop, but I have recently been enjoying the simplicity of [`map`](https://github.com/soveran/map). Using `map`:

```
$ git diff --name-only --cached --diff-filter=AM | map f 'mix format "$f"'
```

And there we have it. `mix format` being only run for files that have been added or modified. Now I can compare the changes `mix format` has made with `git diff` and then selectively add those that are relevant to my own edits, ignoring those that aren’t, using `git add --interactive --patch`.

—*Sunday, 19th June 2022.*

<div class="references">
  <h3>References</h3>
  <ul>
    <li><a href="https://stackoverflow.com/questions/33610682/git-list-of-staged-files">Git list of staged files</a></li>
    <li><a href="https://yanneves.medium.com/how-to-git-diff-only-for-modified-files-c5bf43b8721e">How to git diff only for modified files</a></li>
    <li><a href="https://github.com/soveran/map"><code>map</code></a></li>
  </ul>
</div>
