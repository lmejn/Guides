# Moving a Branch to a New Repository

This is for if you want to take a single branch and move it a new git repository.

## Motivation

In my case, I started completely new code written in a different language in an existing repository.
I branched off the `main` branch, then deleted all previous code, and started fresh.
The old code still existed on the other branches (*e.g.* `main`, `develop`, *etc.*).

My thoughts at the time was that the new code was going to replace the old code, making use of the new language features and also adding various improvements.
However, as I kept working, its scope became more general, and I felt the need to move it to a repository of its own.

## Method

In these steps, we are moving the branch `<branch_to_move>` from the repository `<old_repo_url>`, discarding all commits before `<commit_hash>`.

1. Moving the branch into a new repository

    There are a few ways to move a branch into. I used a `git clone`, with the `--single-branch` option [[1]](https://stackoverflow.com/a/49293378):

    ```sh
    git clone --single-branch --branch <branch_to_move> <old_repo_url>
    ```

2. Removing the old remote from the new repository

    ```sh
    git remote remove origin
    ```
    
    While not strictly neccesary, this is important to make sure you don't accidentally push to the old repository.

3. Truncating git history

    When moving the single branch, it copies it's whole history, including commits before branching off from `main`.
    In this step, the git history before a given commit is deleted [[2]](https://passingcuriosity.com/2017/truncating-git-history/).
    In my case, this commit is the first commit after branching off of `main`, with commit hash `<commit_hash>`

    ```sh
    git checkout --orphan temp <commit_hash>
    git commit -m "Initial Commit"
    git rebase --onto temp <commit_hash> <branch_to_move>
    ```

4. Tidy up

    You may want to tidy up the new repository, which could consist of:
    * Removing tags
    * Renaming branches
    * Pruning unreachable commits (see `git prune`)
    * Pushing to a new remote

## References

1. [meallhour, Answer to *How do I move a Git branch out into its own repository?*](https://stackoverflow.com/a/49293378)
2. [Thomas Sutton, *Truncating git history*](https://passingcuriosity.com/2017/truncating-git-history/)
