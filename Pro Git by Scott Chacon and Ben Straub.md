# Basics

1. `git clone [url]`  fetches all data of the whole history with all versions
(only some server-side hooks may be lost).

2. `git add` stages files in their fixed on the current moment state.
That means if you `add` some file and modify it after that,
it will appear (in outputs of `git status`) as both *to be comitted* and *not staged*.
Further commit will include only those updates were done before `git add`.

3. `git status` will output shorter status with `--short` (`-s`) flag.

4. Some examples of `.gitignore` rules:
- `*.[oa]`: ignores all files ending with `.o` or `.a`;
- `*~`: ignores all files ending with `~`;
- `#`: to add comments;
- `/` in the ends means exactly folder type;
- `!`: inverts the pattern;
- `?`: stands for one symbol;
- `[0-9]`: any symbol from the range;
- `a/**/z`: any nested folders between `a` and `z`;
- `/TODO`: ignores only the root `TODO` file (the same works for directories as well).

5. `.git/info/exclude` does the same as `.gitignore`,
but is not included into the repo.

6. `git diff` shows only unstaged changes!
It will be empty for files after `git add` executed for them.
To see changes between staged files and their latest committed versions,
use `git diff --staged`.
To see the contents of a deleted file when this update is staged,
use `git diff --cached -- [file]`

7. Simple removal `rm` doesn't stage the result of this operation.
To make the removal be staged for commit, use `git rm`.

8. `git rm` will not allow to remove a file if it has some changes already staged.
In this case use `git rm -f`.

9. If you want to stop watching a file in the repo (move it to untracked files),
but keep it in a working directory, use `git rm --cached [file]`.
It's helpful to remove files that should be .gitignored from the repo.

10. Remove multiple files from the repo using escaped `*`.
`git rm log/\*.log` removes all files with `.log` extenstion from `log` folder.

11. `git mv` is equivalent to the sequence:
```
mv [file]
git rm [file]
git add [file]
```
Two files: one added, one removed, - having less than 50% of differences 
(in terms of Git's indexes) are treated as renaming of the same file.
To avoid losing of the history, it is possible to do 2 commits when renaming is needed:
one - with the renaming itself and the other - with all required corresponding content updates.
It's very easy to miss such updates while resolving merge conflicts
when in one branch some file was updated and renamed (moved),
in another - only updated (or renamed in a different way):
usually Git writes something like *deleted by them* or *deleted by us* in such cases.
The rule of thumb can be to look for pairs of added and deleted files
and process them manually by doing 2 separate commits as described above.

12. `git log -p -2` also shows commits' diffs
and limits the output to the 2 latest commits.
Or you can output short diffs' stats with `git log --stat`.

13. `git log --pretty=oneline` supports various formats: 
`short`, `full`, `fuller`.
It also supports format: `git log --pretty=format:"%h - %an, %ar : %s"`.

14. There is a difference between **author** and **committer**.
**Author** - is someone who created a file.
**Committer** - is someone who edited and saved some updates to this file.

15. `--format=oneline` is very convenient in pair with `--graph`
(`git log --graph --format=oneline`) to add commits and branches visualisations.

16. `git log` has limiting parameters `--since` (`--after`) and `--until` (`--before`),
for example: `git log --since=2.weeks`.
The date here supports various formats.

17. Very convenient filter to include only commits where the provided string
was added or removed: `git log -Sfunction_name`.

18. Another very convenient filter - is inclusion of some path (folder or file)
to output related commits only: `git log -- README`.

19. To view logs only for some specific branch or commit:
`git log [branch_or_commit]`.

20. `git commit --amend` to update the latest commit.

21. `git reset HEAD [file]` to unstage a file
and `git checkout -- [file]` to discard changes in working dir for a file.

22. Everything was once committed can be most likely restored
(even if the target commit was modified or removed somehow).

23. `git fetch [repo]` should fetch all branches,
meanwhile `git pull` will merge only the current branch.
To fetch all remotes, use `git fetch --all`.

24. `git remote show [repo]` shows additional info about the target repo.
`stale` means that the branch has been removed in the remote repo.

25. It's possible to revert multiple commits in a row (if there are no merge commits!):
`git revert --no-commit HEAD~4..`.
`4` is a number of commits to be reverted. Don't forget to do a commit after it.

26. It's possible to compare files from different branches 
(helpful when some file was moved incorrectly):
`git diff [branch]:[file] [file]`.


## Tags

1. Tags are printed in the alphabetical order and can be filtered:
`git tag -l v0.11.0\*`.

2. There are two types of tags: lightweight and commented.
The first type doesn't have any additional information
(don't use `-a`, `-s`, `-m` params to create such tag),
but the second one has a separate hash, author info and other data:
`git tag -a v1.4 -m "Version 1.4"`.

3. Use `git show [tag]` to get info about the tag.

4. Tags are assigned to the current state (branch and commit).
To create a post factum tag (for some old commit), use:
`git tag -a [tag] [commit hash]`.

5. To send your tags to the remote repo, use
`git push origin [tag]` or `git push origin --tags` to send all local tags.

6. Sorting customization is possible when querying tags, for example:
`git tag --sort=-creatordate`.


## Aliases

1. To configure aliases for some commands use the following example:
`git config --global alias.co checkout`.
Usage: `git co ...`.

2. Aliases can launch external executables:
`git config --global alias.visual "!gitk"`.


# Branching

1. Every commit (excepting the original one) has links to its ancestor
or (in the case of merge) ancestors.

2. Branch - is a new lightweight pointer to some commit.

3. `HEAD` points on our current local location (commit or branch).

4. To visualise branches of the repo:
`git log --oneline --decorate --graph --all`
(maybe a shorter version is better: `git log --graph --oneline`).

5. If each branch have new independent commits,
their merging will result in a new commit with a merge result 
(merge commit will contain links to both merging ancestors).
If only one branch has got some independent commits (updates),
in this case fast forwarding is possible (no merge commit will be created).

6. **An empty message in the editor rejects the commit!**

7. Branches support namespaces in their namings divided by slashes.

8. `git branch -v` to see the latest commit in each branch.

9. To list all branches merged or not merged with a current one,
use `git branch --merged` and `git branch --no-merged`.

10. You can remove a branch with `git branch -d [branch]`,
but Git will not allow you to remove unmerged branches 
(in this case you'll need to use `-D` instead).

11. To send your current branch to the remote repo:
`git push [repo] [branch]`, or you can specify the target remote branch:
`git push [repo] [local branch]:[remote branch]`.
To send all branches `git push [repo] --all`.

12. If you don't want to type your credentials every time you interact with HTTPS,
configure its caching with `git config --global credential.helper cache`
(or you can use Keychain in OSX).

13. If you checkout to the local branch created from the remote branch,
this local branch will become so called tracking branch
(directly bound to the same remote branch).
In this local branch `git pull` and `git push` commands
will know that they should work with the corresponding remote repo and branch.

14. To create a local branch from a remote one without tracking:
`git checkout -b [local branch] [remote repo]/[branch]`
(maybe it will require `--no-track` arg).
To start tracking this branch: `git checkout --track [remote repo]/[branch]`.

15. If you want to bind a local branch with some other remote branch for tracking,
use `--set-upstream-to` (or `-u`): `git branch -u origin/develop`.
If specified branch does not exist yet 
or if `--force` has been given, acts exactly like `--track`.

16. A short reference to the current upstream branch is `@{u}`:
`git merge @{u}`.

17. Use `git branch -vv` to see all configured upstreams.

18. Deletion of remote branches:
`git push [remote_name] :[branch_name]` or `git push [remote_name] --delete [branch_name]`.
Deletion will remove only the branch pointer on the server, 
the actual data will be removed later after garbage collection,
so the data can be easily restored immediately.

19. Commits difference between the local branch and its upstream:
`git log dev..origin/dev`.
Shows commits which exist in the seconds branch
but missing in the first one.


## Rebasing

1. Rebasing is better because it keeps the history straightforward, 
even if it represents results of multiple parallel branches.

2. `get rebase --onto master server client`:
- `master`: commits from this branch will be taken without any modifications;
- `server`: feature branch, parent for `client`, 
all commits from it WILL NOT be included into rebasing;
- `client`: feature branch, a child of `server`, 
will get unmodified commits only from `master` 
and put only its own modified commits on top of it.
After that `master` can merge commits from `client`,
`client` will be totally detached from `server` now.

3. We can do rebasing regardless from the currently active branch with:
`git rebase [main branch] [feature branch]`.

4. **Don't rebase on public branches!**
Rebase only in private branches or to put in order commits.

5. Rebasing can fix duplicated commits after someone's rebasing.
No duplicated commits will be in result when you do:
`git rebase origin/master` while on the local `master`.
Similar result can be leveraged with `git pull --rebase`.
You can configure Git to do `git pull --rebase` on `pull`
with `git config --global pull.rebase true`.


# Serving

1. Remote repos don't need a working dir and consist of `.git` folder only.

2. There are 4 available protocols: local, HTTP, SSH, Git.
The local one allows to use local dirs as a remote repo.
Helpful for contributors using the common file system.
Start using the locally distributed repo with:
`git clone /opt/git/project.git` or
`git clone file:///opt/git/project.git`.
The second variant launches the same routines 
used to get the repo from the network:
it's slower but fetches a full copy with additional links and left objects.
To add a local upstream into an existing project:
`git remote add local_proj /opt/git/project.git`.

3. HTTP can be simplier because it provides credentials auth
rather than keys configuration for SSH.
It's the most popular protocol nowadays
because it provides auth and encryption similar to SSH.
Only one endpoint address is used for HTTPs (in contrast to SSH).

4. There is a simple (deprecated) version of HTTP protocol.
It can be very easily configured in your server's public folder:
you just need to create a `--bare` repo in it and configure a post-update hook inside:
```
mv hooks/post-update.sample hooks/post-update
chmod a+x hooks/post-update
```
This hook is required to configure correct downloading and cloning via HTTP.
After these configs done, other users can fetch the configured repo with:
`git clone https://example.com/gitproject.git`.
In this case all repo's files will be served as usual static files.
No remote writing to the repo is possible.

5. In the case of Git any HTTP protocol is faster than SSH.

6. SSH is the simpliest to configure protocol for remote servers.
To fetch a repo using SSH:
`git clone ssh://user@server:project.git`.
The protocol can be omitted, Git uses SSH by default:
`git clone user@server:project.git`.
Username can be omitted too, Git will use creds of the user
authorized in the system.
SSH doesn't support anonymous access - even for reading.

7. Git protocol is a daemon similar to SSH, listens port 9418, doesn't require auth.
It's not easy to configure a Git daemon.
To make a repo work via Git protocol, add `git-export-daemon-ok` file.
It's not safe, because it allows access for anyone or to nobody,
doesn't use auth and encryption.
That means, it's better to allow read only access to it,
because anyone, who knows the URL, can write to the repo.
But this type of the protocol is faster due to absent encryption.


## SSH configuration

1. To start serving a Git repo, prepare a bare repo:
`git clone --bare my_project my_project.git`.
This command prepares a Git repo without a working dir from a usual repo.
There is a convention to name folders with bare repos ending with `.git`.

2. No additional configs are required to provide an access to the bare repo via SSH.
The bare repo should just be accessible via usual SSH.
If there are write permissions for the repo's folder,
it will be possible for users to write into the repo.
You can grant write permissions for the group with `--shared`:
`cd some_repo.git && git init --bare --shared`.

3. If you don't want to create a new user in OS for each contributor via SSH,
you can create one user (e.g. *git*) 
and ask all contributors to send you their public keys to configure access for them.

4. `ssh-keygen` can generate a key pair without a passphrase.
Private key is stored in `~/.ssh/id_rsa`, public - in `~/.ssh/id_rsa.pub`.
On the server the public key should be appended into `~/.ssh/authorized_keys`.

5. If you don't want to allow shell usage for *git* user,
change his shell program into something else in `/etc/passwd`,
it can be `git-shell` for example: `/usr/bin/git-shell`.
In this case *git* user will not be able to connect to the server via regular SSH,
he can only execute Git-related commands with it.
Git shell can be configured in various ways, read `git help shell`.


## Git configuration

1. It's not secure without a firewall, because it's publicly available.
Only one command is required for configuration:
`git daemon --reuseaddr --base-path=/opt/git/ /opt/git`:
- `--reuseaddr`: allows a server to restart without awaiting for timeout of old connection;
- `--base-path`: users will not require to add a full path to exported repos for cloning.

2. To make the daemon above start automatically in Ubuntu systems configure Upstart,
create a new file - `/etc/event.d/local-git-daemon` with the following contents:
```
start on startup
stop on shutdown
exec /usr/bin/git daemon \
    --user=git --group=git \
    --reuseaddr \
    --base-path=/opt/git/ \
    /opt/git/
respawn
```
To start the daemon manually without restart:
`initctl start local-git-daemon`.

3. For security reasons commands and configs above should be processed
for a read only user (e.g. *git-ro*).

4. To make a bare repo accessible via Git,
put a `git-daemon-export-ok` file into it.
This will enable Git protocol access without auth for the repo.


## Smart HTTP configuration

1. The goal of the configuration is to pipe all target information 
getting to the server to `git-http-backend` CGI.
See https://git-scm.com/book/en/v2/Git-on-the-Server-Smart-HTTP.

2. Auth is processed by the configured web-server,
`git-http-backend` controls only data interactions via HTTP.

3. Simple UI (GitWeb) can be added for the Git server.
It can be done temporarily with `git instaweb` or `git instaweb --httpd=webrick`
from the target project, `--httpd` selects a preferred web-server.
To stop this server, fire `git instaweb --httpd=webrick --stop`.

4. For persistent usage GitWeb should be manually configured 
(e.g. with Apache integration) or can be configured via `apt`,
see https://git-scm.com/book/en/v2/Git-on-the-Server-GitWeb.


# Distributed Git

1. Besides one central repo, the workflow can be organized to have one central repo
and multiple working repos for separate teams.
In this way it's simplier to manage the central repo
without interruptions on updates of contributors.

2. For large projects the system can have an additional level in the hierarchy:
one central repo and a level of intermediate repos managed by their own leads.
Regular developers fetch master branch of the central repo
but send their updates only to the repos of their level.

3. `git diff --check` allows to check updates on spurious whitespaces
which will make further merging more difficult.

4. One commit - one update or atomic idea.
If you did lots of updates without separate commits,
staging can help you to divide your updates
and create step-by-step commits.
Instead of adding the whole updates with `git add`
use `git add --patch` (or `git add -p`) 
to add updates step-by-step interactively.

5. If you need to write a huge commit message,
write one summary line (less than 50 chars),
then one empty line,
then the whole description (about 72 chars).
You can use dashes or asterisks to enumerate statements.

6. `git log --no-merges` - self-explanatory.

7. You can check commits difference before merging of your updates:
```
git fetch origin
git log [feature_branch]..origin/master
```

8. Local branches can be created from remote ones:
`git checkout -b feature origin/master`.

9. `git rebase -i` allows to reorganize your updates
or merge multiple commits into a single one.
You can do the interactive rebasing for the latest 3 commits with
`git rebase -i HEAD~3`.
Keep in mind that rebasing overwrites the history,
so don't rebase in any commits which are already available in public. 

10. When doing forks for further pull requests,
it's better not to merge updates of `origin/master` into your feature branch.
Also, don't touch your fork's master branch,
only pull updates into it from the forked repo.
You can do a pull request without web UI with:
`git request-pull origin/master [name_of_my_fork_repo]`.
All target updates should be sent to the remote branch of your fork first.

11. If your PR has not been rejected, but some updates were requested,
create a new branch from the central upstream,
merge it with your previous branch, squashing (joining into one) its commits
and without doing a commit,
do required updates, commit and push:
```
git checkout -b featureV2 origin/master
git merge --no-commit --squash feature
# do required updates 
git commit
git push [name_of_my_fork_repo] featureV2
```

12. Git supports sending of updates via email with patches.
It even provides ways to actually send required updates via IMAP.


## Maintaining a project


### Fetching and checking difference

1. To fetch someone's remote branch from another repo to process a PR:
```
get remote add jessica git://github.com/jessica/myproject.git
git fetch jessica
git checkout -b rubyclient jessica/ruby-client # creating a local branch for a requested PR branch
```
After that it's a good idea to check which commits were added in the PR:
`git log master..rubyclient -p`: `-p` to see commits' diffs.

2. `git fetch -p` to remove all local **remote-tracking** branches which are removed in remote.
`git remote prune [repo]` does the same for a specific remote `repo` without fetch.
These commands remove **only remote-tracking** branches, not simple local branches.

3. `git pull [repo_url] [branch_name]` to do one-time pull from the remote repo
with `repo_url` without adding this remote repo to the list of your remotes.
`[branch_name]` specifies the name of the branch from `[repo_url]`.

4. `git diff master` from the PR feature branch can show bad outputs in the case
when `master` was fast-forwarded after the feature branch was created from it.
In this case it will show that feature branch tries to remove some fast-forwarded updates.
There will no problems if `master` is the ancestor of the PR feature branch.
But if the story differs in some point, the difference will look strange.
In this case we want to compare the latest commit of the PR branch
with a first common ancestor in `master`.
To find such ancestor and check the difference with it, do:
```
git merge-base contrib master # contrib - is a PR feature branch
git diff [output_hash_of_the_command_above]
```
But these commands are too verbose, use three dots for the same:
`git diff master...contrib`.


### Integration of contributed updates

1. `git cherry-pick [hash]` to make a copy of the specified via hash commit
into your current branch. The copied commit will have another SHA-1 hash.

2. One of possible ways to check if rebasing is required before merging into a public branch
is to do on this public branch: `git merge [feature_branch] --ff-only`.

3. Git allows to sign tags and commits to make sure they come from trusted sources.
More details: https://git-scm.com/book/en/v2/Git-Tools-Signing-Your-Work.

4. `git describe master` outputs the name of the closest tag,
how many commits were done after it and partial hash of this anchor commit.
You can use `git show [result_of_the_command_above]` to get the detailed information.

5. Git has commands to archive the project (its specific folder) in its current state:
- tarball: ```git archive master --prefix='project/' | gzip > `git describe master`.tar.gz```;
- zip: ```git archive master --prefix='project/' --format=zip > `git describe master`.zip```.

6. To prepare a log of commits done after a specific tag
(convenient to send a backlog to the team):
`git shortlog --no-merges master --not v1.0.1`.


# GitHub

1. Git adds an email of the author into a commit.
To make GitHub match your email correctly,
it should be the same as your GitHub email,
or it should be added into **Emails** configs in GitHub.

2. GitHub supports 2FA to be configured in **Account security**.

3. If your merge request has some conflicts,
it's better to use merging instead of rebasing with the target branch.
But if you insist on rebasing, don't publish these updates into the branch
prepared for the pull request (because it's already became public).
Create a new branch and new PR in this case and remove the old ones. 

4. If you apply PRs in the web UI of GitHub,
it will always create merge commits,
even if fast-forward is possible.

5. `git ls-remote origin` to see all remote tags, branches, PRs and other links:
```
5fd724b54a1143bed2d3aa0ff8f5a1ec0bc61e30	refs/pull/986/head
70bde75d4c5ce797f54763c6e193c473c8c8b7d7	refs/pull/986/merge
```
PRs have two links. `/head` one refers to the commit of the remote repo
used by the contributor in the PR (the corresponding hash is not available in your own repo).
`/merge` one refers to the commit can occur if we tap on **Merge** button in PR's description.
To use these links you can configure their automatic fetching into your specific local branches,
see [Pull Request Refs](https://git-scm.com/book/en/v2/GitHub-Maintaining-a-Project).

6. PRs can target any branch or another PR.


## Markdown and comments

1. PRs and issues have mutually unique numbers:
it's not possible to have both PR #3 and issue #3
in the same project at the same time.

2. Use `#3` to refer someone's issue or PR in the current project,
Refer any issue or PR in any foreign project with `username/repo#3`.
To refer some commit, paste its full hash into the message.

3. GitHub supports enhanced Markdown in comments, e.g. task lists.
These items are interactive, the Markdown code will be updated automatically
when you tap on these items.
Another profit - state of these tasks will be rendered
in summaries of the corresponding PRs:
- [x] Completed task
- [ ] Uncompleted task

4. GitHub supports correct syntax highlighting for blocks of code:
```java
for (int i = 0; i < 5; ++i) {}
```

5. Citing is available with `>`:
> Some cited sentence.

6. Emoji are also supported, they start and end with `:`, e.g. `:bug:`.

7. Adding `!` before the link makes it rendered as an image:
![Alizee](https://salvemusic.com.ua/wp-content/uploads/2020/03/alizee-2-768x950.jpg)

8. All updates in your PRs will have email notifications.
You can reply to these emails - it will add the same comments
to the discussion of the corresponding PR.

9. Tap on **Unsubscribe** to stop receiving any notifications
(notifications can be initiated by various actions).

10. If both email and site types of notifications are enabled,
reading of an email notification with images showing enabled in the email client
will lead to marking of this notification as read on the site.


## Special files

1. README with any textual extensions - contains the descriptions about:
- the purpose of the project;
- how to configure and install the project;
- how to launch and use the project;
- license of the project;
- how to contribute into the project.

2. CONTRIBUTING - can have various extensions,
contains the guides for contribution into the project 
which will be shown on creation of PRs.


## Organisations

1. Organisations can have groups (**Teams**)
simplifying access management for groups of users.
Teams can be referenced with `@orgname/teamname`.


## GitHub integrations

1. GitHub supports integrations with thousands of external services
including CI, bug tracking systems, chat and documentation systems.

2. Webhooks allow to send configured POST requests with payloads
on specific actions in GitHub.
Hooks can be helpful to analyze the latest commits and set statuses for them.
These statuses will output additional prompts on PR pages.

3. To use auth-related GitHub APIs, 
it's better to register a new corresponding app
and generate a new token for it.
Markers are more configurable rather than regular login-pass access.
Using auth increases the allowed rate of requests per hour.

4. It's possible to store large files outside the repository (both local and remote) with 
[Git Large File Storage](https://git-lfs.github.com/).
LFS replaces large files such as audio samples, videos, datasets, and graphics 
with text pointers inside Git, 
while storing the file contents on a remote server like GitHub.com or GitHub Enterprise.
By using such text pointers, the actual files are getting removed (most likely) from the Git index
(with all corresponding consequences).


# Git tools


## Revision selection

1. `git show [hash]` also works with hashes and branches, not only with tags.
It shows the latest commit.

2. `git log --abbrev-commit` shows commit hashes long enough to be unique.

3. `git rev-parse master` shows a commit hash related to the object.

4. `git reflog` to view the journal of all updates in the repo.
The journal stores data for about a couple of months,
so it's difficult to get journal data for really old updates.
The journal keeps only your LOCAL updates in the repo.

5. `git show HEAD@{5}` to check the HEAD pointer (repo's state) 5 steps ago.
`git show master@{yesterday}` to check `master` branch pointer's state at yesterday.

6. `git log -g master` to view the journal of `master` 
in the similar as `git log` format.

7. `^` means an ancestor of the commit, e.g.:
`git show HEAD^`.
You can add a number after `^` 
to specify a concrete ancestor in the case of merge
(1 - for the active branch, 2 - for branch being merged).

8. `~` also specifies an ancestor of the commit,
works in the same way as `^` without a number.
But when the number is provided,
it specifies a "grandfather" ancestor of n-th depth
for the current commit.

9. `HEAD~3` is the same as `HEAD^^^`.
Multiple ancestor types can be combined:
`HEAD~3^2`.

10. To view commits were added between the first and second specified commits (branches):
`git log master..experiment`.
A good advice to check commits in the local branch which are not sent to the remote yet:
`git log origin/master..HEAD`.
If you omit one part of this record, Git will substitute `HEAD` in its place,
for example, the following is the same as the command above: `git log origin/master..`.

11. Difference between multiple branches is supported too.
The following commands are equivalent:
```
git log  refA..refB
git log ^refA  refB
git log  refB --not refA
```
The two bottom most spellings are helpful to combine many references:
```
git log refA refB ^refC
git log refA refB --not refC
```
The commands above are the same and mean:
*all commits in `refA` or `refB` but not in `refC`*.

12. To get commits which are only in the `master` or `experiment` branch,
but not in the both branches at the same time:
`git log master...experiment`.
Add `--left-right` parameter to get from which side every commit is placed:
`git log --left-right master...experiment`.

13. `git add -i` to stage files in interactive mode -
works with multiple files in contrary to `git add -p [file]`
which works only with one file.
In this mode you can:
- `u` or `2`: to add files to the index (the system will ask which ones,
pressing **Enter** without any typing will add all files to the index);
- `r` or `3`: restore (move back to the working dir) files from the index;
- `d` or `6`: view diffs of indexed files (similar to `git diff --cached`);
- `p` or `5`: the same as `git add -p [file]` - step-by-step indexing,
if you don't know what the shorcuts of possible actions mean, type `?`.

14. There are other partial variants for basic commands such as:
```
git reset --patch      # to partially reset files from the index
git checkout --patch
git stash save --patch
```

15. When using merging to pull the latest updates from a target branch into a feature branch,
all merge commits and commits merged from the target branch 
are getting included into outputs of `git diff` and `git log` commands 
if the difference between two commits with a merge commit in between 
is requested at the feature branch.
For `git log` it's possible to use:
- `--no-merges`: to exclude merge commits only;
- `--first-parent`: to exclude all commits pulled into the feature branch from the target branch
in the result of intermediate merges.
But even with `-p` only updates for particular commits are going to be shown.
This difference is not smart: when merge and related commits are excluded,
all intermediate updates done during the merge commit are excluded as well.
The reason for all this is that `git diff` works only with two nodes: 
it can not show the difference between two commits 
also excluding all updates matching the target branch.

16. As a workaround to solve the problem above 
(the problem is that to see all differences between two commits of a feature branch 
excluding updates coming from intermediate merges which are common with the target branch), 
it's possible to create a temporary branch 
with everything before the first commit of the interest merged into it
and compare (`git diff`) the latest state of the feature branch with this temporary branch.
The only caveat of such approach is that if there are merge conflicts,
they should be resolved for this merging into the temporary branch anyway.
But for simplification in resolving the conflicting places 
it's possible to prefer the target branch's versions everywhere.


## Stashing

1. `git stash` or `git stash save` to save all state
(both indexed and in the working dir) 
in the stash to restore it later.

2. `git stash list` to see the entries of saved states.

3. `git stash apply` to restore THE LATEST saved state only.
To restore a more specific state entry, use:
`git stash apply stash@{2}`.
We can restore stash into any branch with any state.
Stash restoration will restore indexed files to non-indexed,
to make these files indexed again, use:
`git stash apply --index`.

4. Stash restoration doesn't clear the stashed state from the stack.
To clear the stash:
- `git stash clear`: to clear THE WHOLE stash;
- `git stash drop stash@{0}`: to drop only one specific state entry from the stash;
- `git stash pop`: to restore a SINGLE stashed state and drop it from the stash.

5. `git stash --keep-index` to make all changes already added to the index left intact.

6. By default stashing doesn't touch untracked files.
But it's possible to stash untracked files too with:
`git stash --include-untracked` or `git stash -u`.

7. Stash supports the interactive mode:
`git stash --patch`.

8. To unapply stashed updates after some editing, use:
`git stash show -p stash@{0} | git apply -R`.
`-p` adds actual diffs to the output.
Omitting `stash@{0}` will use the latest stashed entry.
`-R` stands for reverse patch application.

9. `git stash branch [branch_name]`:
creates and checks out a new branch from a commit
on which initial stashing was performed,
applies changes from stash into it,
drops the stash entry if this operation was successful.

10. `git clean` removes untracked files from the repo.
Be careful with it because this command is not restorable,
better prefer `git stash --all`.

11. `git clean -f -d`: 
`-d` also removes untracked directories,
`-f` is required if configuration variable `clean.requireForce` is true.
You can also check what will be deleted without actual deletion:
`git clean -n`.
Use `-x` parameter to remove ignored files too.
Use `-i` for interactive step-by-step mode (very helpful for small working dir restorations).

12. It's possible to save current working dir or staged updates, 
use `--binary` to include binary files (images, for example):
`git diff [--cached] [--binary] > some.patch`.
To load the patch:
`git apply some.patch`.


## Signing

1. Make sure you have some keys available:
`gpg --list-keys`.
Otherwise generate a new key with:
`gpg --gen-key`.

2. To use your private key for signing of commits and tags,
configure it for Git:
`git config --global user.signingkey [key_fingerprint]`.

3. To create a signed tag instead of the usual one,
use `-s` instead of `-a`:
`git tag -s v1.5 -m ...`.

4. To check the signature of a tag:
`git tag -v [tagname]`.
You need a public key of the tag's author in your storage
to make this check pass.

5. To sign commits, use `-S` parameter:
`git commit -a -S -m "Signed commit"`.

6. To validate and view commits' signatures, use:
`git log --show-signature`.
Format is also supported with `%G?` parameter:
`git log --pretty="format:%h %G% %aN %s"`

7. `git merge` and `git pull` will not be performed with
`--verify-signatures` when merging with a branch
which have unsigned on invalid commits.

8. To sign a merge commit, use `-S` parameter:
`git merge --verify-signatures -S signed-branch`.


## Search

1. `git grep -n something`: performs a search in files of your working dir,
`-n` adds a number of matches to the output.

2. `git grep --count something`: lists only found files with matches count,
doesn't output matched fragments.

3. To include function names where the target query is used:
`git grep -p something *.c`.

4. `git grep --break --heading -n -e '#define' --and \( -e LINK -e BUF_MAX \) v1.8.0`:
- `--break --heading`: for better formatting (empty line separation, name of the found file on top);
- `-e`: states each part of the pattern;
- `--and`: joins patterns;
- `\( ... \)`: `--or` is the default operator, so it will be used here;
- `v1.8.0`: version to search for the pattern in.

5. To search for commits where specific contents were updated:
`git log -S[what_to_search]`.
To use regular expressions in this search, use `-G` instead.

6. To search for commits with updates of `git_deflate_bound` function in `zlib.c`:
`git log -L :git_deflate_bound:zlib.c`.

7. To search the entire history of a file (including its renames):
`git log --follow -p -- [filename]`.


## Rewriting history

1. `git commit --amend` to modify the name or contents of the latest commit,
don't use it if the target commit is already public.

2. There is no separate instrument to modify the history,
so interactive rebasing should be used to change commit names
or their contents.
Rebasing should know from which commit you want to start modifications:
`git rebase -i HEAD~3` (for last 3 commits).

3. Rebasing modifies the history even if commit messages were not changed,
so avoid doing this on public commits.

4. Squashing merges (melds) a commit with a previous one
and merges (joins) commit messages in the result commit message.

5. To split a commit:
- mark it to be edited in the interactive rebase;
- reset all its updates into unindexed state: `git reset HEAD^`;
- do required indexations, commits;
- return to rebasing to complete: `git rebase --continue`.

6. Interactive rebasing updates hashes of all commits in the requested range.
Don't modify public commits, don't include unnecessary commits into the range.

7. To apply updates in the whole history, use `filter-branch`.
For example, to remove a passwords file in the whole history:
`git filter-branch --tree-filter 'rm -f passwords.txt' HEAD`.
`--tree-filter` does the stated command for each version and makes commits again.

8. It's better to run `filter-branch` in some testing branch first
and only after that apply the same updates to the `master`.
To make `filter-branch` apply to all branches, use `--all`.

9. `git filter-branch --subdirectory-filter trunk HEAD` -
sets the root directory to the nested `trunk` dir,
Git will also remove commits unrelated to this folder.

10. There is a script to change emails in all commits in the history.
All hashes in all commits will be changed (not only in the ones with a target email),
see [Changing Email Addresses Globally](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History).


## Reset and checkout

1. `git reset [hash]` moves a branch to the `hash`
(meaning all commits after the `hash` will be dropped!).
All updated files between the head and the `hash` will be unstaged.
`--soft` flag will do the same, but the difference will be in staged state.
Helpful to do something similar to `git commit --amend`.

2. When using `--hard` option, resetting will add an addition step
to restore the working dir to the index of the target commit
(meaning nothing will be in staged or unstaged states).
`git reset [target] --hard` is very dangerous,
because it really removes the data and makes its restoration impossible
if it was not committed (otherwise `reflog` to rescue).

3. Without any target (commit hash) reset will move back a target file from the index:
`git reset file.txt`.
Speaking other words, indexing for this file will be replaced into its HEAD state
(meaning to the state before any updates of this file).

4. `git reset [hash] [file]` to move a single file to its previous state.

5. `git reset` supports `--patch` for partial applications.

6. Resetting can be used to join multiple commits into a single one, e.g.:
```
get reset --soft HEAD~2 # dropping 2 latest commits, keeping their changes as unstaged
# doing required updates
git commit # saving the updated version in one commit
```

7. Checkout can work without actual moving of HEAD when some file has been provided:
`git checkout [target] [file]`.
In this case both working dir and the index for the `file` 
will be in the `target` state.
This command is dangerous to lose the data!

8. Checkout can work in the step-by-step mode with `--patch` parameter.


## Advanced merging

1. Before potentially difficult merge it's better to clean-up the working dir
by making a new commit in a temp branch or stashing -
there is a risk to lose all unsaved data on merge.

2. If you did not expect to resolve any conflict,
you can abort merging with `git merge --abort`.

3. When the conflict is due to whitespaces updates
(when someone uses tabs and someone whitespaces),
add an additional flag to overcome it:
`git merge -Xignore-all-space [branch] --no-commit`.

4. We can get all versions of files being merged:
```
git show :1:hello.rb > hello.common.rb # saving the common ancestor state
git show :2:hello.rb > hello.ours.rb   # our version of the file
git show :3:hello.rb > hello.theirs.rb # common version appearing during the merge
```
We can get hashes for all of these versions with `git ls-files -u`.

5. We can update splitted versions of the files above and merge them after that:
`git merge-file -p hello.ours.rb hello.common.rb hello.theirs.rb > hello.rb`.
This version is better than `-Xignore-all-space`, 
because whitespaces will be fixed, not just ignored.

6. `git diff --ours` to compare the current state with the state before merge in your branch.
`git diff --theirs` to compare the merge result with another merging branch before the merge.
`git diff --base` to get the difference for both branches in contrary to their common (base) state.

7. If you did some updates in the file during the conflict 
and want to reset its state to position, 
when the conflict markers were untouched yet, do the following:
`git checkout --conflict=diff3 [file]`.
`--conflict` uses `merge` option by default (can be changed in configs),
but `diff3` also adds a base version into markers.

8. `git checkout` in the case of conflict can also have 
`--ours` or `--theirs` flags 
to choose one version of the file without merging.

9. To get commits for each side of the merge:
`git log --oneline --left-right HEAD...MERGE_HEAD`.
Adding `--merge` flag will reduce the number of printed commits
to the ones which are the reasons of the current conflicts only:
`git log --oneline --left-right --merge`.
Adding `-p` flag will also include updates in the conflict file.

10. `git diff` during the conflict outputs all conflicting fragments
(because all other fragments will be staged).

11. To view all merge details, use `git show [merge_commit]`
or `git log --cc -p -1`.

12. To cancel merge commit which was not published yet,
use usual reset: `git reset --hard HEAD~`.
Another possible way (for commits which were already published) -
is to do a revert: `git revert -m 1 HEAD`.
`-m 1` specifies an ancestor to be selected.

13. In the case of revert if you want to merge the branches again,
Git will show a message about no updates possible.
To overcome this, revert the previous revert and merge again:
```
git revert [hash_of_prev_revert_commit]
git merge [branch]
```

14. Git can do automatic merging and in the case of conflicts
prefer versions from one side, e.g.:
```
git merge -Xours   [branch]
git merge -Xtheirs [branch]
```
The similar thing can be applied to `merge-file`:
`git merge-file --ours`.

15. We can ignore one of branches totally 
and make a fictious merge commit by using a strategy:
`git merge -s ours [branch]`.
Make sure there are no differences with the previous state of your branch:
`git diff HEAD HEAD~`.

16. To include another project (branch) 
into some subfolder of your existing project -
`git read-tree --prefix=rack/ -u rack_branch`:
- `--prefix`: where to put the root of the branch `rack_branch`;
- `rack_branch`: target branch to include the root from.
After that all updates of the subfolder project can be easily pulled
with regular `git checkout rack_branch && git pull`.

17. To merge pulled updates from `rack_branch` into `master`:
`git merge -s subtree`. 
But in this case the whole history of projects will be merged.
It's better to do the following:
```
git checkout master
git merge --squash -s subtree --no-commit rack_branch
```
In this case we get something similar to submodules but without actual submodules.
The way with subtrees is more difficult and error-prone.

18. The usual diff will not work in the case of subtrees,
use instead: `git diff-tree -p rack_branch`.

19. It's possible to do interactive merging with step by step hunks comparison by:
`git checkout -p target_branch_to_merge --`.


## Command rerere

1. Git has **rerere** util to cache resolving of conflicts
and reuse these cached schemes again automatically.
It stores pre image - original parts of the conflict
and post image - how the conflict from the original parts was resolved.
To enable such caching, execute:
`git config --global rerere.enabled true`.
This automatic resolving will keep related files as unstaged,
for automatic indexing of such resolving, enable autoupdate:
`git config --global rerere.autoupdate true`.

2. **rerere** does merging (rebasing) automatically.
There is a practice for long-living branches
when you process merging and conflicts manually
(then run a bunch of tests),
then cancel them and use **rerere** instead
(which works for rebasing too).

3. Usually we use `git status` to get the source of the conflict.
With **rerere** enabled, we can also check the parts
added to the pre image: `git rerere status`.

4. `git rerere diff` shows the current state of conflicts solving
and learned ways to solve specific conflicts.
The way to solve the conflict will be saved on commit.

5. You can cancel automatic conflict solving applied,
take a look on the original conflict
and resolve it automatically with **rerere**:
```
git checkout --conflict=merge hello.rb
cat hello.rb
git rerere
```


## Debugging with Git

1. To view the latest updates applied to the file (its parts):
`git blame -L 12,22 simplegit.rb`,
where `-L 12,22` stands for lines range of interest to be checked.
Commits with `^` represent lines included into the initial commit.

2. To get the origin (commits the code was taken from) of the code
(maybe due to refactoring or file renaming),
use `-C`: `git blame -C -L 143,153 GITPackUpload.m`.

3. To find a bad block of the code which led to errors, use `bisect`.
Start it: `git bisect start`.
Mark the current commit as the one having issues: `git bisect bad`.
Then point Git to the latest working commit: `git bisect good [commit]`.
In this case Git will switch you to the middle of the good->bad commits chain.
Now you should run your tests and notify Git whether the issue appears or not
with `git bisect bad` or `git bisect good`.
When the procedure is over, reset to your initial HEAD state with
`git bisect reset`.

4. Binary search can be automated if you have some test 
which returns `0` in the good case and something else in the case of errors.
Start the search with specifying the bad and good anchor commits:
`git bisect start HEAD v1.0`.
Then run `bisect` in the auto mode: `git bisect run test-error.sh`.


## Submodules

1. `git submodule add [url]` - to add a submodule into a project.
In the end of this command a path to place the submodule can be added.
The command above will lead to creation of `.gitmodules`,
this file describes all submodules and should be kept in the repo.

2. Meanwhile `.gitmodules` should be public,
it's possible to override the actual endpoint to interact with a submodule
using local configs: `git config submodule.[submodule_name].url [private_url]`.

3. Git doesn't watch for any updates inside of a submodule's folder.
You can only get a diff of its configs with:
`git diff --cached --submodule`.
This command also shows a current commit to be used in the submodule
(submodule's folder is a some kind of commit metadata itself).

4. Usual cloning will not fetch submodules' contents, only their empty folders.
To fetch submodules:
```
git submodule init   # to init a local config file
git submodule update # to fetch the latest contents of submodules 
                     # targeting to the commit specified in the main project
```

5. The same result as above can be achieved simplier with:
`git clone --recursive [url]`.

6. Navigate into submodule's folder, 
fetch and merge its required updates on required branch.
Go back to the main project,
do a commit to fix submodule's state to be used with the project.

7. To update a specific submodule to its latest remote state on `master`
(instead of the target commit specified in the root project):
`git submodule update --remote [submodule]`.
This command fetches the latest updates only if fast-forward is possible.
Local configs (`.git/config`) or `.gitmodules` can be configured 
to follow another branch: 
`git config -f .gitmodules submodule.[submodule_name].branch [branch]`.
Without `-f .gitmodules` these configs will be local.

8. `git log -p --submodule` to view updates related only to submodules configs.

9. To merge or rebase the latest updates for a submodule from remote
(when simple fast-forwarding is not possible due to local updates), 
use `--merge` or `--rebase` flags: `git submodule update --remote --merge`.

10. To push all local updates with submodules, use:
`git push --recurse-submodules=check`.
`check` will prevent pushing of the main project
if some local updates of submodules were not published yet.
`on-demand` will send updates of all submodules first,
after that the main project will be pushed.

11. If there are multiple updates of the same submodule
(meaning an anchor commit of the submodule 
has been changed at the same time by two authors),
automatic merging will not be performed.
View details with `git diff`.
Go to the submodule's directory,
make sure you are on the correct branch with
`git rev-parse HEAD`,
merge it with a conflicting commit (shown by `git diff`):
`git branch try-merge [commit]`.
Do commit in the submodule, do commit in the main project.

12. It's possible to apply the same command to all submodules:
`git submodule foreach 'git stash'`.
Or: `git submodule foreach 'git checkout -b featureA'`.

13. If you create a submodule in one branch, commit it,
switch to another branch, remove submodule's dir
and get back to the submodule branch, 
it will be required to init and update the submodule again.

14. Checkouts from branches with some directory
to another branches with the samely named submodules
can be not very straightforward.
It can require checkouts in the forced mode (`-f`)
and subsequent checkouts inside of submodules' folders.


## Bundles

1. Git can bundle bunches of updates (commits) into a binary format.
`git bundle create repo.bundle HEAD master` bundles all updates of the repo
to restore its master branch.
Use `HEAD` if bundle is supposed to be used for cloning.
Otherwise specify the range of updates (refs or commits) to be included into it.

2. To restore the bundle above:
`git clone repo.bundle repo`. 
`repo` - is a folder name to be used for the restored repo.
If there is no `HEAD` stated during bundling,
specify a target branch (existing in the bundle)
to perform unbundling.

3. It's possible to bundle all updates,
but it's better to include only the actual updates.
Getting new commits not available in the remote:
`git log --oneline origin/master..master`.
Bundling only new commits:
`git bundle create commits.bundle master ^[hash_to_include_commits_from_excluding_anchor]`.

4. Verify (whether it has valid data and required ancestors) 
a bundle before its application: `git bundle verify [bundle_file]`.

5. To view branches in a bundle available for extraction:
`git bundle list-heads [bundle_file]`.

6. To fetch some branch from a bundle (`master`) into some local branch (`other-master`):
`git fetch [bundle_file] master:other-master`.


## Replace

1. To divide some repository into two -
one with old history and one with only new commits, do the following:
- history: create a new branch from a target boundary commit hash,
push it to the history repo's master branch -
`git branch history [ending_commit] && [adding_remote] && git push project-history history:master`;
- we need an instersection between new repos,
so they should have some commit in common;
we also need to add an introduction commit 
to the repo with a new history with all instructions;
use `commit-tree` to create a hash of the new tree without ancestors:
`echo 'get history from ...' | git commit-tree [commit_before_intersection]^{tree}`.
After that we need to rebase a new history into newly created tree:
`git rebase --onto [hash_of_the_new_tree] [commit_before_intersection]`.
Now we are ready to publish this tree into the new repo too.

2. To get the previous history for the cloned repo with the newest commits:
- add and fetch the remote with the previous history;
- join the histories with `replace`: 
`git replace [hash_of_new_common] [hash_of_old_common]`.


## Credential storage

1. Cached passwords are never stored on the disk
and removed in 15 minutes.

2. In the saving mode creds are saved in a textual file
(no credentials should be typed until you change the pass on the server),
but unfortunately they are stored in the opened mode in the home dir.

3. For macOS (Windows) it's possible to persistently save creds 
in the keychain (winstore) in the encrypted mode.

4. To select credentials storage mode, use:
`git config --global credential.helper cache`. 
Some of these modes have additional parameters.

5. `git credential fill` to see how credentials will be prepared
for specific protocol, host and other params. 

6. It's possible to configure any other custom program
to be used as credentials provider.


# Customizing Git

1. All settings have multiple levels: 
`--system` (all users), `--global` (all repos of the user) and local.
Settings are stored in simple text files can be edited by hand.

2. To see the list of all available configs: `man git-config`.

3. To use a specific text editor: `git config --global core.editor vim`.

4. To use a custom template to type commit messages:
`git config --global commit.template ~/.gitmessage.txt`.

5. Configuring a program to view outputs of `log` and `diff` commands:
`git config --global core.pager more`.

6. Configure `help.autocorrect` to automatically correct misspellings in commands.

7. See `color.*` configs to adjust color schemes in Git.

8. To get a global `.gitignore` for the current user:
`git config --global --get core.excludesfile`,
to set (update) it:
`git config --global core.excludesfile ~/.gitignore_global`


## Formatting and whitespaces

1. Git has `core.autocrlf` config which allows to configure better
interoperability for new lines between Linux and Windows systems.
If all developers work only in one OS, this config can be disabled.

2. All configs to process whitespaces should be listed in the `core.whitespace` config.
It can be also configured to emit only warnings or try to do fixes on encountered cases.
For private commits (which are not published yet) it's possible to do whitespace fixes
during rebasing: `git rebase --whitespace=fix`.


## Server configs

1. Server-side, this operation slows down the performance, 
to check objects sent from clients: `git config --system receive.fsckObjects true`.

2. Some commits (for example after rebasing) 
can be pushed to the server anyway with `-f` flag.
To make such thing impossible, configure the server:
`git config --system receive.denyNonFastForwards true`.

3. To disable deletion of branches and tags on the server:
`git config --system receive.denyDeletes true`.


## Git attributes

1. Attributes can be included into the repo via `.gitattributes` files
or can be stored only locally in `.git/info/attributes`.
The attributes configure merge strategies for separate files,
describe how to compare binary files,
filter contents before writing into the repo.

2. To treat all `.pbxproj` files as binaries (even if they are encoded in UTF-8),
create `.gitattributes` with the following contents: `*.pbxproj binary`.

3. `.docx` files are treated as binaries in Git.
To treat these files as textual:
- configure `.gitattributes` with `*.docx diff=word`, `word` filter should be configured;
- download and install **docx2txt** program;
- write a wrapper script for this program named `docx2txt`:
```
#!/bin/bash
docx2txt.pl $1 -
```
- `chmod a+x` the created wrapper;
- configure Git to use this wrapper: `git config diff.word.textconv docx2txt`.

4. Something similar can be configured to diff images 
(by comparing JPEG's EXIF metadata):
- download and install `exiftool`;
- create `.gitattributes`: `*.png diff=exif`;
- configure Git to use the filter: `git config diff.exif.textconv exiftool`.

5. `.gitattributes` can configure filters to be applied 
before checking out (smudge) and before staging (clean):
- filter to be applied for `*.c` files (in `.gitattributes`): `*.c filter=indent`;
- actions for the filter to be done on clean:  `git config --global filter.indent.clean  indent`;
- actions for the filter to be done on smudge: `git config --global filter.indent.smudge cat`.

6. Excluding parts from archives, into `.gitattributes`: `test/ export-ignore`.
The `test/` folder now will not be included into archives with
`git archive` commands.

7. To add `LAST_COMMIT` file with a corresponding date of the latest commit into archives:
```
echo 'Last commit date: $Format:%cd$' > LAST_COMMIT
echo "LAST_COMMIT export-subst" >> .gitattributes
git add LAST_COMMIT .gitattributes
```

8. Configuring a preferable merging strategy
(into `.gitattributes`): `database.xml merge=ours`.


## Git hooks

All hooks are stored in the `.git/hooks` folder.
On repo initialisation it's filled with hooks templates.
To make Git execute a hook, it should be correctly named.
Make sure every required hook has executable rights.
Commit validation hooks on the client side are more preferable,
because they prevent users from wrong actions before they were done.


### Client hooks

1. Client-side hooks are not copied on clone.

2. `pre-commit` checks all files,
makes sure tests passed before any commit. 
Non-zero result of this hook prevents commit creation,
but this can be ignored with `git commit --no-verify`.

3. `prepare-commit-msg` is used to preapre interactive
commit messages templates.

4. `commit-msg` aborts commit creation on any non-zero result.
Has a commit message as an argument.
Helpful to validate commit messages.

5. `post-commit` is executed right after commit's creation,
has no arguments, helpful to do notifications.

6. There is a couple of email-related hooks.

7. `pre-rebase` - before rebasing, 
can stop rebasing if returns non-zero result.

8. `post-rewrite` is launched after commands
which modify the history (`--amend` or `rebase`).
Has a command name initiated history modification as an arg.
Used for notifications.

9. `post-checkout` - after successful checkout:
to configure your working dir
(to move ignored binaries for example).

10. `post-merge` - after successful merge
(to restore data in the working tree, for example).

11. `pre-push` - before push, 
name and URL of the remote server as arguments.
Non-zero result prevents pushing.

12. Git does garbage collection time to time with `git gc --auto`.
`pre-auto-gc` is called before such garbage collection.


### Server hooks

1. `pre-receive` - before fetching and saving of proposed by the client data.
Non-zero results will prevent the data from receiving and saving.

2. `update` - the same as `pre-receive`, but launched per each sent branch.
Non-zero results prevent only a single branch from updating,
all other updates will be processed separately.
Takes a branch name, hashes of commits before and after 
(this one is proposed by the client) update.

3. `post-receive` - usually for notifications.
Make sure it doesn't make any heavy lifting,
because it doesn't abort the connection until completes.


# Git and other systems

1. Git has a bridge to interact with SVN (and vice versa) - `git svn`.
While interacting with SVN try using rebasing 
and keep your history linear.
Every call to `git svn` interacts with SVN.
Don't rewrite history already published.
Don't publish anything to a remote Git repo at the same time,
use SVN repos only.

2. To send updates both to SVN and Git remote repos,
send updates to SVN first, because it modifies existent commits
by adding specific `git-svn-id`s, so commit hashes will be updated too.  

3. To use branching with SVN, 
prefer rebasing to send updates to SVN branch,
because SVN supports only linear history.
Merging will send to SVN only one squashed commit with all updates.
Try to use branching in SVN as less as possible.

4. Branch creation is a little bit specific too 
to make it interoperable with SVN.


# Git internals

1. Git is fundamentally a content-addressable filesystem 
with a VCS user interface written on top of it.
Low-level utils which provide basic functions and support piping
are compared with plumbing sometimes,
meanwhile high-level commands for end-users - with porcelain.

2. To do a backup of a repository, 
it's enough to copy its `.git` folder.

3. `description` file in `.git` folder is only used by **GitWeb**,
`config` contains configurational params related to the project,
`info` contains all rules for ignoring 
which you don't want to place into `.gitignore`,
`hooks` contains hooks described previously.

4. `HEAD`, `index`, `objects/` and `refs/` are crucial for the repo:
- `objects`: database of the repo;
- `refs`: pointers to commit objects in the db (branches);
- `HEAD`: points to the branch you are inside of right now;
- `index`: information from the index.


## Objects in Git

1. Git stores its data in the key-value format.

2. Writing the data into the db with `git hash-object` -
`echo 'test content' | git hash-object -w --stdin`:
- `-w`: to write the data;
- `--stdin`: to read the data from the standard input (not from a file).
The command above outputs SHA-1 hash 
with a checksum of your data and header.
Check out how the data has been saved in Git:
`find .git/objects -type f`.

3. Git saves one file per one saving unit named with SHA-1 hash:
it has a subfolder named with 2 first characters of the hash
and the file itself inside of this subfolder named with the rest 38 chars.

4. To extract data from the db use `git cat-file`:
`git cat-file -p [hash]`.
`-p` makes the command get the data type 
and reproduce the contents correctly.

5. Every update saved for a file with `git hash-object -w [file]`
creates a new object in the db which can be restored later.
No previous objects are deleted.
Git doesn't keep filenames in the db
and saves all files in the blob format.

6. Git can show a file type of a db saved entity:
`git cat-file -t [hash]`.
To retreive a size of the file from the db, use parameter `-s`.


### Tree objects

1. Git supports a tree type of objects which is like UNIX dirs
(meanwhile blobs are similar to regular files).
Trees can store multiple objects (each one with its own hash):
blobs and other trees (trees are used for directories).
For example, to get a tree of the latest commit in the project:
`git cat-file -p master^{tree}`.
`master^{tree}` stands for a tree object 
pointed by the latest commit in `master`.

2. Git creates new trees from the index.
To create a new tree, something should be added to the index.
To add something into the index on low level, use
`git update-index --add --cacheinfo 100644 [hash] [filename]`:
- `--add`: because the file was not indexed yet;
- `--cacheinfo`: because the file is in the db, not in any dir;
- `100644`: access rights.

3. Allowable access rights for files 
(dirs and submodules can have other rights too):
- `100644`: regular file;
- `100755`: executable file;
- `120000`: symlink.

4. Use `git write-tree` to write the staging area out to a tree object:
`git write-tree`.
Read the contents of the created tree: `git cat-file -p [hash]`.
Check the type of the created tree: `git cat-file -t [hash]`.

5. Create a new tree with the second version of `test.txt` and a new file as well:
```
echo 'new file' > new.txt
git update-index --add --cacheinfo 100644 [hash_of_file_v2_in_db] test.txt
git update-index --add new.txt
```
Write out that tree 
(recording the state of the staging area or index to a tree object):
`git write-tree`.
Check result of the command above:
`git cat-file -p [hash_of_the_new_tree]`.

6. We can read trees into index with `git read-tree`:
`git read-tree --prefix=bak [hash_of_the_tree]`.
`--prefix=bak` to read an existing tree as a subtree
(`bak` will become a subdir in the index).
Save the result tree from the index:
`git write-tree`.


### Commit objects

1. Commit objects store various content-state info:
who has saved the state, when and why.

2. To create a commit object, use `commit-tree`.
It accepts hash of the required tree 
and previous commit objects 
(if such objects exist - with `-p [prev_commit_hash]`):
`echo 'first commit' | git commit-tree [hash]`.
The result object will have the `commit` type:
`git cat-file -p [hash]`.
User's data is taken from configs.

3. To check the log of such raw commits, use:
`git log --stat [latest_commit_hash]`.


### Object storage

1. Headers of objects have the following format:
`[type] [content_length]\0`, for example: `blob 16\u0000`.
Git concatenates the header with contents 
and gets SHA-1 for the result data.

2. Git uses **zlib** to compress stored objects.
Object's data include both header with concatenated contents.

3. Objects are saved into `.git/objects/${sha1[0..2]}/${sha1[2..40]}`

4. All objects are stored in the same way,
only the type in the header differs: `blob`, `tree`, `commit`.


## Git references

1. All references are stored in `.git/refs`.
References store SHA-1 hashes with simple filenames.

2. To create a new reference to the latest commit:
`echo "[hash_of_the_latest_commit]" > .git/refs/heads/master`.

3. It's not recommended to edit references files directly,
better use `git update-ref`:
`git update-ref refs/heads/master [hash]`.
Otherwise the history in `reflog` will not be updated.

4. Branch is a simple pointer or reference to the head of a line of work.

5. To start a new branch using low-level utils, use `git update-ref`:
`git update-ref refs/heads/test [commit_hash]`.

6. References to remote branches are stored in 
`.git/refs/remotes/[remote]/[branch]`.
Remote branches are read only,
`HEAD` will not target them on checkout.


### HEAD pointer

1. Git stores SHA-1 of the latest commit in the `HEAD` file.
Its contents are represented with a symbolic reference to the current branch:
`ref: refs/heads/master`.

2. `git commit` creates a new commit object with an ancestor 
pointing to the current `HEAD` value.

3. It's better to edit the `HEAD` with `git symbolic-ref`:
- `git symbolic-ref HEAD`: to view the current value;
- `git symbolic-ref HEAD refs/heads/test`: to update.


### Tags

1. Tag objects are very close to commit objects:
it contains the name of the author, date, comment and pointer.
But the pointer in the case of tag points to some commit instead of tree.

2. Lightweight tags can be created with `git update-ref`:
`git update-ref refs/tags/v1.0 [commit_hash]`.

3. Annotated tags create tag objects with additional data. 
In this case Git points to a tag object 
(which points to a commit object), 
rather than to a commit directly.


### Links specification

1. In `.git/config` there are entries for all remotes 
with the following format:
`fetch = +refs/heads/*:refs/remotes/origin/*`.
First part - is the source from the remote,
second - is the destination in the local repo.
`+` makes Git refresh references even when ff is not possible.

2. This configuration (fetching) can be done single time
without adding anything to persistent configs:
`git fetch origin master:refs/remotes/origin/mymaster`.
`master` - branch in the remote.
`refs/remotes/origin/mymaster` - local destination for fetching.
It's possible to state multiple branches mappings 
in a single fetching request.

3. Pushing has similar configs:
`push = refs/heads/master:refs/heads/qa/master`.
First part - is a local branch,
second - remote.


## Pack files

1. Git stores each version of the same file 
(even if these versions have minor differences)
in separate db objects initially.
But when `git gc` is called
or when amount of such similar objects becomes bigger
or when we push the data to the remote server,
these data are packed into a single binary file
(to reduce required storage space).

2. Packed files are stored in `.git/objects/pack`.
Packs consist of two files: 
`pack-[hash].idx` and `pack-[hash].pack`.
The first one stores shifts in data storage and checksums 
of files written in the pack-file.

3. While packing Git searches for files with similar names and sizes,
saves only difference between them.

4. To check the contents of a pack file, use `git verify-pack`:
`git verify-pack -v .git/objects/pack/pack-[hash].idx`.


## Transfer protocols

1. Remote servers have `info/refs` and `objects/info/`
with various metadata required to perform fetching from it.
These files are generated with `update-server-info`,
this is why we enable `post-receive` hook in dumb HTTP servers. 

2. Pushing and fetching is done with a pair of programs for each side
(4 in total: 2 for server, 2 for client).


## Maintenance and data recovery

1. `git gc` packs separate objects or other packs into single packs,
multiple refs into single files (`.git/packed-refs` file),
removes objects with no commit references to,
removes old objects (created months ago).
This command is launched automatically time to time,
or can be launched manually: `git gc --auto`.

2. To restore data (commit), 
we need to find hash of a commit to be restored via `git reflog`
or via more verbose `git log -g`.
Journal logs are stored in `.git/logs/`.
Then we should create a new (restoration) branch 
pointing to the removed commit:
`git branch [branch] [hash]`.
After that we can merge with a restored branch.

3. What if journal logs (`.git/logs/`) were removed?
Use `git fsck` to check validity of the db,
this command will output all unreachable objects
including dangling commits.

4. There is a problem when some huge file has been added to the repo,
this file is not needed anymore and was removed in the latest commit,
but it's still stored in objects, 
because there are some links to it in the history. 
The methodology described below rewrites the history
by updating all commits starting from the one when the huge file was added.
Make sure to notify the team about the updated history.

5. To find huge files, use:
`git verify-pack -v .git/objects/pack/pack-29…69.idx | sort -k 3 -n | tail -3`,
where `29…69` - is the actual packs range.
Select hash of the biggest blob from the output.
Get the name of the file with:
`git rev-list --objects --all | grep [hash_of_the_blob]`.
Find commits which modified the file:
`git log --oneline -- [filename]`.
Remove the huge file starting from its initial appearance commit:
`git filter-branch --index-filter 'git rm --cached --ignore-unmatch [file]' -- [hash]^..`.
`--index-filter` to modify files in the index (not on the disk),
`--tree-filter` is allowable too, but it will not be so fast.
`git rm --cached`, because the removal should be done from the index (not from the disk).
`--ignore-unmatch` disables outputs with error messages 
when no matching files were found.
After that it's recommended to clean-up outdated references and logs
and do repack again:
```
rm -Rf .git/refs/original
rm -Rf .git/logs/
git gc
# optionally then
git prune --expire now
```

6. `git count-objects -v` shows the size of the repo.


# Appendices

1. Git has out-of-the-box program to view 
and visualise history - `gitk`.

2. Another out-of-the-box util is to edit commits:
`git gui`.

3. There are some command-line utils
helping to interact with Git
in its official repo in the `contrib` folder.

4. **Libgit2** library provides high-level C API
to integrate your programs with Git.
There are also wrappers for this library 
for other programming languages.
