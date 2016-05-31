<style>
h2:not(:target) a{
    font-size:1rem;
    text-align:left;
    border:0;
    background-color:transparent;
}
h2:not(:target) a:before{
    content:"\025B8";
    padding-right:1em;
}
h2 + section{
    display:none;
}
h2:target + section{
    display:block;
}
</style>

# Common Git Errors

These are the errors or unexpected behaviors you're most likely to run into when using Git.

## Your `push` is rejected because you "failed to push some refs"
<section>
#### How it looks

```
$ git push origin master
To git@github.com:wdi-student/my-repo.git
 ! [rejected]        master -> master (non-fast-forward)
 error: failed to push some refs to 'git@github.com:wdi-student/my-repo.git'
 hint: Updates were rejected because the tip of your current branch is behind
 hint: its remote counterpart. Integrate the remote changes (e.g.
 hint: 'git pull ...') before pushing again.
 hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

#### Explanation

A Git repository is a series of "snapshots" of your files in the same way that a cartoon is a series of snapshots of drawings.

This error says you were unable to push some commits ("refs") to Github because you're missing some commits that Github has. In other words, someone has added more frames to the cartoon on Github, and you don't have those frames. Presumably you don't want to overwrite those frames, so Github rejects your push.

#### Resolution

Notice the "hint" saying to `git pull ...`? Just run:

```
$ git pull origin master
```

This will pull down the commits you're missing. Then, you should be able to push yours up to Github.
</section>

## Your `push` is rejected because of denied permission
<section>
#### How it looks

```
$ git push origin master
ERROR: Permission to ga-wdi-exercises/homeworkaroo.git denied to wdi-student.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

#### Explanation

You're trying to push to a repo that you don't own. Note above it says **ga-wdi-exercises/homeworkaroo.git**. That means you're trying to push to the instructors' repo.

#### Resolution

If you haven't already, fork the repo. Then add your fork as the remote called `origin`, push to it, and make a pull request if desired:

```
$ git remote remove origin
$ git remote add origin git@github.com:wdi-stduent/homeworkaroo.git
$ git push origin my-branch-name # Usually `git push origin master`
# On the Github page for YOUR FORK, click the green "New pull request" button
# Click the green "Create pull request" button
# Click the new green "Create pull request" button
```
</section>

## Merge conflicts
<section>
#### How it looks

```
$ git merge branch-two
Auto-merging some-file.html
CONFLICT (add/add): Merge conflict in some-file.html
Automatic merge failed; fix conflicts and then commit the result.
~/Desktop/wdi-student (branch-one *+|MERGING) $
```

...or...

```
$ git pull origin master
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:wdi-student/my-repo
 * branch            master     -> FETCH_HEAD
17a9271..c80c46f  master     -> wdi-student/my-repo
Auto-merging some-file.html
CONFLICT (add/add): Merge conflict in some-file.html
Automatic merge failed; fix conflicts and then commit the result.
~/Desktop/wdi-student (master *+|MERGING) $
```

#### Explanation

You and another person both tried to commit different changes affecting the same parts of the same files at the same time. Git doesn't know which commit is "right", so it's asking you to tell it.

#### Resolution

Notice it specifies the file(s) that have the merge conflicts. In this case, it's just one file: `some-file.html`. If you open that file you'll see at least one section like this:

```
<<<<<<< HEAD
This is what John wrote.
=======
This is what Jane wrote.
>>>>>>> abc123456789
```

This is showing you the change you made and the change the other person made.

Simply delete the lines you don't want. (Presumably you don't want the `<<<<<<<` lines, so delete those too!) Then, `add` and `commit` as normal.
</section>

## You can't pull because you're "up-to-date", but you know changes have been made
<section>
#### How it looks

```
$ git pull origin master
From github.com:wdi-student/my-repo
 * branch            master     -> FETCH_HEAD
Already up-to-date.
```

### Possibility 1

#### Explanation

You're pulling commits from the wrong branch.

#### Resolution

Run this:

```
$ git pull origin THE-BRANCH-NAME
```

Similarly, if you get this error when trying to push, run:

```
$ git push origin THE-BRANCH-NAME
```

### Possibility 2

#### Explanation

This happens when someone has rebased or reset what's on the remote repository. Until you're *really* comfortable with Git you probably shouldn't be doing either of those. Basically, it changes a commit that has already been made.

Because the commit still has the same ID (or "SHA") that it did before, the Git on your computer doesn't recognize it as a new commit. As far as it's concerned, you have the same commits the remote repository does.

#### Resolution

Run this:

```
$ git reset --hard origin/master
```

This is saying, "Drop everything I have and make it look exactly like whatever's in the `master` branch of the remote repository." Note that **this will overwrite any changes you've made that aren't on Github**.
</section>

## Your head is detached
<section>
#### How it looks

```
$ git checkout 17a9271
Note: checking out '17a9271'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 17a9271... foo
```

#### Explanation

This actually isn't an error at all -- it just looks weird! Git's telling you that you're not on a branch right now. When you check out a previous commit, even if that commit appears to be on the same branch you're currently in, you still get put into a "branchless" state.

Git can only make commits on a branch. It's giving you this warning to tell you that if you make any commits and then checkout to another branch, they're going to be overwritten and you'll lose your commits.

#### Resolution

To tell Git to make a new branch from the commit you're currently on, do exactly what it tells you to do in the warning message:

```
$ git checkout -b NEW-BRANCH-NAME
```
</section>

## Not a git repository
<section>
#### How it looks

```
$ git pretty-much-anything
fatal: Not a git repository (or any of the parent directories): .git
```

#### Explanation

There's no `.git` folder, i.e. you haven't made your folder into a Git repository.

#### Resolution

Inside the folder:

```
$ git init
```
</section>

## "Origin" is not a remote repository
<section>
#### How it looks

```
$ git push origin master
fatal: 'origin' does not appear to be a git repository
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

#### Explanation

Either you misspelled `origin`, or your local repository doesn't "know" about the repository on Github.

#### Resolution

Inside the folder:

```
$ git remote add origin git@github.com:ga-wdi-exercises/homeworkaroo.git
```
</section>

## `git remote -v` shows the wrong repo
<section>
#### How it looks

```
$ git remote -v
origin git@github.com/this-is-the-wrong-organization/homeworkaroo.git (fetch)
origin git@github.com/this-is-the-wrong-organization/homeworkaroo.git (push)
```

#### Explanation

Your local repository thinks you want to push to the wrong Github repository.

#### Resolution

```
$ git remote remove origin
$ git remote add origin git@github.com:wdi-student/homeworkaroo.git
$ git push origin master
```
</section>

## `git remote -v` doesn't show anything
<section>
#### How it looks

```
$ git remote -v
...
```

#### Explanation

See #7.
</section>

# Something else?

Let the instructors know!

