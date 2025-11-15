- Amend a `Nth` commit using interactive `rebase`
	1. `git rebase -i HEAD~N`
	2. A editor will open up with your last `N` commits which will look like this
		```bash
		pick a1b2c3d Fix typo in API route
		pick d4e5f6g Add webhook handler
		.
		.
		.
		pick e2c9f6g init commit		
		```
	3. remove the word `pick` to `edit` in front of the commit you want to change
		```bash
		pick a1b2c3d Fix typo in API route
		edit d4e5f6g Add webhook handler
		.
		.
		.
		pick e2c9f56g init commit
		```
	4. save and close, git will stop at the commit where you specified pick with the following prompt
		```bash
		Stopped at d4e5f6g... Fix typo in API route
		You can amend the commit now.
		```
	5. Now, you can go into the editor and make all the changes you needed to make. After you made those changes run these commands
		```bash
		git add .
		git commmit --amend
		```
	6. Now you have completed your change in the commit and can continue the `rebase` with the following command
	```bash
	git rebase --continue
	```

- To revert a commit, you can run
	```bash
git reset --hard HEAD~1
	```
	This will delete the commit and all the changes done in that permanently so use it only when you really don't want to keep any change in that commit.

- To copy a commit from branch `A` to branch `B` you can use `git cherry-pick` as follows
	```bash
git log A # prints history of branch A, copy the hash of commit
git checkout B # switch to the branch B
git cherry-pick <commit-hash>
	```
	It will copy the commit whose `hash` is presented from branch `A` to `B`, but the commit still remains on branch `A`

- To stop tracking a file you are already tracking, adding in `.gitignore` won't be enough. Run the following command to stop tracking the file
```bash
git rm --cached <PATH_TO_FILE> # This will stop tracking the file
# it removes the file from git index
# if you want to remove the file from your working directory too
# then remove --cached option
git rm <PATH_TO_FILE>
# if you want to remove a folder
git rm -r --cached <PATH_TO_FOLDER>
```