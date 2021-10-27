
## Code Deployment Steps 

### Get status of Git
* `git status`

### Create New Branch for your code deployment. Get Jira ticket number and add hypens with small description and all lowercase. For new task, this should be out of stable.
* `git checkout -b {{Name of Branch}}` origin/stable

* Example: Get Jira ticket number and add hypens with small description and all lowercase.
`git checkout -b pt-34-signal-field-stats origin/stable`

### Make code changes to your code

### Add the changed code to git
`git add .`

### Stash your git branch if you want to change the branch to stable
`git stash`

### Get stable branch with updated Code
`git fetch`

### Change the branch to Stable if not at stable.
`git reset --hard origin/stable`

### Add your branch to stable
`git stash pop`

### Run bootstrap for application to update from Stable branch
`npm run bootstrap`

### Check the status again
`git status`

### Commit your code. The Jira ticket and small code description.
`git commit -m "PT-34 Script to log number of fields in the signals across all orgs"`

### Push your changes to Git
`git push -u origin pt-34-signal-field-stats`

### Check the Pull request

### In the PR for running the test
* go to services/juno and run test 
`npm run test`

---------------------------------------------------------------------

## Any time if you want to make any changes to code based on pull request comments

### Check the branch. It should point to your pull request branch
`git status`

## make changes to your code

## Add your file
`git add .`

### Commit your code. The Jira ticket and small code description.
`git commit -m "PT-34 Script for logging number of fields in the signals across all orgs. The code changed to remove concurrent execution and added linear execution"`

### Push your changes to the same branch
`git push -u origin pt-34-signal-field-stats`




