# favorite-branching-strategy

## Possible problems and solutions

**Problem:**

Pushed an unconventional commit to main branch by editing the squash and merge commit message generated from the PR title (or by any other stupid decision)

**Solution:**

Source: https://stackoverflow.com/q/8981194/20003689

If it is the most recent commit, you can simply do this:

    git commit --amend

This brings up the editor with the last commit message and lets you edit the message. (You can use `-m` if you want to wipe out the old message and use a new one.)

And then when you push, do this:

    git push --force-with-lease <repository> <branch>

Or you can use "+":

    git push <repository> +<branch>

Or you can use `--force`:

    git push --force <repository> <branch>

Be careful when using these commands.

- If someone else pushed changes to the same branch, you probably want to avoid destroying those changes. The `--force-with-lease` option is the safest, because it will abort if there are any upstream changes (

- If you don't specify the branch explicitly, Git will use the default push settings. If your default push setting is "matching", then you may destroy changes on several branches at the same time.

Pulling / fetching afterwards: anyone who already pulled will now get an error message, and they will need to update (assuming they aren't making any changes themselves) by doing something like this:

    git fetch origin
    git reset --hard origin/master # Loses local commits

Be careful when using `reset --hard`. If you have changes to the branch, those changes will be destroyed.

A note about modifying history: the destroyed data is really just the old commit message, but `--force` doesn't know that, and will happily delete other data too. So think of `--force` as "I want to destroy data, and I know for sure what data is being destroyed." But when the destroyed data is committed, you can often recover old commits from the reflog—the data is actually _orphaned_ instead of destroyed (although orphaned commits are periodically deleted).

If you don't think you're destroying data, then stay away from `--force`... [bad things might happen][1].

This is why `--force-with-lease` is somewhat safer.
