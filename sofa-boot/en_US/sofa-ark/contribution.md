> You can visit [Development Route](./roadmap) first to learn more about development tasks and future planning.

## Preparations

Before contributing any code, we need to know how to use the Git tool and the GitHub website.

* Refer to the [Git official books](http://git-scm.com/book/zh/v1) for the Git tool usage. The first few chapters will help you get a quick start.
* Read [Git collaboration process](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html) through

## GitHub Code Contribution Process

### Submitting an issue

Whether you want to fix a bug of SOFAArk or add a new feature of SOFAArk, you have to submit an issue to describe your demand before you submit the code on [GitHub  address](https://github.com/sofastack/sofa-ark) in SOFAArk. There are several advantages of doing so:

* There will not be any conflict with other developers or their plans for this project to result in repetitive work.
* The maintenance personnel of SOFAArk will discuss about the bug or new function you submitted, to determine if the modification is necessary, or if there is any room for improvement or any better solution.
* Start developing and submitting code after agreement to reduce the cost of communication between both parties as well as the number of rejected pull requests.


### Getting the source code

To modify or add a function, click the `fork` button in the upper left corner to copy a SOFAArk trunk code to your code repository, after submitting an issue.

### Pulling a branch

Perform all the SOFAArk modifications on the branch, and submit a `pull request` after the modifications, which will be merged to the trunk by the project maintenance personnel after Code Review.  
Therefore, after getting the introduction to source code steps, you need to:

* Download the code locally. You may select the git/https mode in this step.

  ```text
  git clone https://github.com/{your account}/sofa-ark.git 
  ```

* Pull a branch to prepare for code modification.

  ```text
  git branch add_xxx_feature
  ```
  
After the preceding command is executed, your code repository will switch to the corresponding branch. To view the current branch, execute the following command:
  
```text
git branch -a
```

If you want to switch back to the trunk, execute the following command:
  
```text
git checkout -b master
```
  
If you want to switch back to the branch, execute the following command:
  
```text
git checkout -b "branchName"
```

### Modify the code and submit it locally.

After a branch is pulled, you can modify the code.

#### When modifying the code, note the following:

* Keep the code style consistent. SOFAArk uses the Maven plug-in to keep the code style consistent. Before submitting the code, execute the following commands locally:
  
  ```text
  mvn clean compile
  ```

* Supplement unit test code.
* New modifications should have passed existing unit tests.
* You should provide the new unit test to prove that the previous code has bugs and the new code has fixed such bugs. Execute the following command to run all tests:
  ```text
  mvn clean test
  ```

#### Other do's and don'ts

* Retain the original style of the code you are editing, especially the spaces and line feeds in the code.
* Delete useless annotations.
* Annotate the places where the logic and functionality are not easy to understand.
* Update documents in a timely manner.

After the code is modified, run the following command to submit all modifications to the local repository:
  
  ```
  git commit -am 'comment'
  ```

### Submit the code to a remote repository

After being submitted to the local repository, the code is synchronized to the remote repository. Execute the following command to submit the local modification to github:

```
git push origin "branchname"
```

If you clicked the `fork` button to get the source code, the modified code is pushed to your code repository rather than the SOFAArk repository.

### Submit a request for merging the code into the trunk

After submitting the code to GitHub, you can send a request to merge your modified code into the SOFAArk trunk code. To do so, enter your repository on GitHub and press the `pull request` button in the upper right corner. Select the target branch (usually `master`), and the system will notify the SOFAArk personnel, who will review your code and merge it into the trunk as part of SOFAArk if it meets the requirements.

#### Code review

After you submit the code, it will be assigned to maintenance personnel for review. You need to wait for the review result. If you have not received a reply after several days, leave a message under the PR, and put an at (@) sign before the name of the responsible person.

The person will leave a comment containing the code review suggestions on the corresponding PR or issue. If you find the suggestions reasonable, update your patch based on the comments.

#### Merge the code into the trunk

After your code passes the review, the SOFAArk maintenance personnel will merge it into the trunk. You do not need to participate in this step. After the code is merged, you will receive a message.
