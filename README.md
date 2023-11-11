# Git Tips and Tricks for Effective Developers

## Git daily scenarios with tips

Disclaimer. Here I consider usage of standard [feature branch workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow). This flow is widely used in modern microservice architecture and CI/CD pipelines.

### Feature branch workflow

Create a new branch, work on it for a while, push, pass tests and builds, finally create a Pull Request.

```
# Create new branch and checkout it
git checkout -b JIR-123-new-feature

# Change your code, do commit the changes
git add file_1.py
git add file_2.py
git commit -m 'JIR-123 changed file_1 and file_2'

# This will create a new branch on the server
git push -u origin HEAD

# Your build is failed! Fix it
git add file_1.py

# This approach helps you not to spam commits like 'fix', 'fix', 'fix'
# Caution. Use only in your personal branch!
git commit --amend --no-edit
# Force push, rewrite the history
git push -f

# In a shared branch just create new commit
git commit -m 'JIR-123 fixed file_1'

# At this point of time you may want to create a Pull Request
```

### Pull Request review

Go through multiple iterations of code review. Commit the changes. Squash 'fix' commits after you finally get your approve.

```
# Checkout your branch
git checkout JIR-123-new-feature

# Fix after first iteration
git add file_1.py
git commit -m 'JIR-123 changed file_1.py'
git push

# Fix after second iteration
git add file_1.py
git commit -m 'JIR-123 changed file_1.py'
git push

# Finnaly, your PR is approved
# Reduce number of commits using "squash" for better readability
# Option 1. Leave one commit in the branch
git rebase -i master
# Option 2. Squash only 'fix' commits
git rebase -i HEAD~2
# Force push, rewrite the history
git push -f

# Your reviewer approves again. You're allowed to merge
```

[Freecodecamp.org: Tutorial on squashing](https://www.freecodecamp.org/news/git-squash-commits/)

Note. Some Git hosting services provide an option to merge with squash in UI.

<details>
<summary>Why you shoudn't use git commit amend / push -f during code review</summary>
<p>When you rewrite the history, the reviewer comments unlink, and it becomes quite annoying to check that you fixed the code in a way it was intended</p>
</details>

### Merge conflicts

Disclamer. Actually, developers are devided on two camps in the question of conflict solving. The first one prefers rebases. The second one prefers merges. This guide encourages using rebases, since they produce linear history which is much easier to read and maintain.

[Atlassian blog: Merge or rebase?](https://www.atlassian.com/blog/git/git-team-workflows-merge-or-rebase)

Regular rebases allows you not to get too far behind of master or your main branch. Yes, you will have to solve conflicts more frequently but in rather small portions. Keeping your branch too far away from master may lead to a very painful merge at the end.

```
# When you come to work, in your feature branch
git checkout JIR-123-new-feature
# Keep you uncommited changes to the temporary storage
git stash
# Do rebase
# Caution. Use only in your personal branch!
git rebase master

# You will probably need to solve conflicts

# If something went wrong
git rebase --abort

# If you fixed the conflicts
git add file_1.py
git rebase --continue

# If you want to return evertything back
git checkout origin/JIR-123-new-feature
git branch -f JIR-123-new-feature

# If rebase is ok

# Return uncommited changes
git stash pop
# Force push, rewrite the history
git push -f

# After rebase you will have zero conflicts with master
```