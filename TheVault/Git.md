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