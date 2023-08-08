# Managing Issue Status Transition via Github

The following is a guide on managing the status transition for an issue created in the [TF Web Jira project](https://tf-web.atlassian.net/). The idea is to trigger the status change of an issue by specific Github actions and events such as branch creation, pull request creation, etc. 

There are three possible types of issues: _Bug_, _Story_, and _Task_. Only _Bug_ and _Task_ issues are related to code, so only these tasks can be affected by the rules outlined below.

## Rules

Issue status changes:
- **TO DO** 🡪 **IN PROGRESS**: when a new branch (related to the issue) is created.
- **IN PROGRESS** 🡪 **IN REVIEW**: when a pull request into the _staging_ branch is created.
- **IN REVIEW** 🡪 **IN PROGRESS**: when a corresponding pull request is declined.
- **IN REVIEW** 🡪 **IN STAGING**: when a corresponding pull request is merged into the _staging_ branch.
- **IN STAGING** 🡪 **READY FOR DEPLOY**: when a pull request into the _main_ or _master_ branch is created from _staging_ branch.
- **READY FOR DEPLOY** 🡪 **COMPLETED**: when a corresponding pull request is merged into the _main_ or _master_ branch.

Note that the above order is the only possible order of status transitions. For example, a transition from **IN REVIEW** to **COMPLETED** is impossible.

## Steps

### Step 1

Let's consider an issue with the "TO DO" status in the backlog or sprint board:
![image](https://github.com/andreyxdd/developers_guide/assets/20891090/9bba2b09-b358-4f71-b7ad-5fa3d3c6544c)

Click on "Create branch" to create a new Github branch associated with the issue. Choose the corresponding repository and branch of origin, then type in the name of a new branch.

Follow the format of `{jira-issue-key}/{username}/{new-branch-name}`. Otherwise, Jira won't be able to identify the branch:
![image](https://github.com/ton-developers/developers_guide/assets/20891090/ddc7ea36-0a7f-4c5c-94cb-99cb6b9b9a5c)

Once it's done, the branch should be created in the specified Github repo:
![image](https://github.com/andreyxdd/developers_guide/assets/20891090/7e7c19b4-cb79-4152-b0b8-6e3d31a18639)

Also, the status of the issue in Jira should change to "IN PROGRESS".
![image](https://github.com/andreyxdd/developers_guide/assets/20891090/0e400c69-5cc7-46f6-b2fc-d59ead0f9f92)


### Step 2

Work on the code.

Optionally, when committing new changes to your branch, you can specify the issue key in the commit message:
![image](https://github.com/andreyxdd/developers_guide/assets/20891090/6369a341-9c1d-4c31-8afb-e027c9c347e3)

This will make the commit appear in the issue description. The history of commits can also be viewed from Jira:
![image](https://github.com/andreyxdd/developers_guide/assets/20891090/cef1d5f9-91bc-4757-9143-da7171a6d027)

### Step 3

Once you are done with all the necessary modifications to address the issue, create a pull request into the _stagin_ branch. Note that the issue status transition won't be triggered if the pull request is made into a branch other than _staging_.

Now, on the issue page, you should see the status changed to **IN REVIEW**. Note that it might not be instant -- Jira might take a minute to identify the event.

### Step 4

Let your colleagues know about a new PR or assign reviewers in Github. Once the PR is successfully merged into _staging_, the issue status should change to **IN STAGING**.

Also, the rules are configured so that if the PR is declined (by you or your reviewers), the issue status changes back to **IN PROGRESS**.

### Step 5

After the new code is deployed at the _staging_ environment and our team approves the updates, create a pull request into the _main_ or _master_ branch (depending on the project repo). Make sure to include the issue key at the beginning of the pull request title -- `{jira-issue-key} {pull-request-title}`. This is the only way to identify that your PR into _main_/_master_ belongs to the given Jira issue.

For example:
![image](https://github.com/andreyxdd/developers_guide/assets/20891090/aa5818ff-5450-43d3-a663-6844b5e189aa)


This should trigger the issue status change from **IN STAGING** to **READY FOR DEPLOY**.

### Step 6

Finally, once the PR into the _main_ or _master_ branch is merged, the issue status changes to **COMPLETED**. Congrats!

![image](https://github.com/andreyxdd/developers_guide/assets/20891090/7513ff4c-f0a7-4e78-a578-eb2fb9c0892a)

Now you can calmly wait for users to report the new bugs in production as a result of your changes!

## Automation Logs

If at any of the above steps, the issue status hasn't changed, please look up the _Audit log_ in the automation settings of your Jira project (Project -> Project Settings (on the left tab) -> Automation -> Open corresponding rule). If this didn't help, contact your Jira admin.
