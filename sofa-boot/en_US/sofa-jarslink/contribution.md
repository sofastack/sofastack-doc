> You can go into the [development route](./roadmap) to learn more about development tasks and future planning.

## Preparations

Before contributing any code, we need to know how to use the Git tool and the GitHub website.

* For the use of git tools, refer to [official books on git](http://git-scm.com/book/zh/v1) and get familiarized by reading the first few chapters.
* For the git collaboration process, refer to the article named [Git Collaboration Process](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html).

## GitHub Code Contribution Process

### Submitting an issue

No Matter whether you are fixing a Jarslink bug or adding a Jarslink feature, submit an issue on the Jarslink [GitHub address](https://github.com/sofastack/sofa-jarslink) to describe the bug you are going to fix or the feature you intend to add before you submit the code. There are several advantages of doing so:

* There will not be any conflict with other developers or their plans for this project to result in repetitive work.
* The Jarslink maintenance personnel will discuss the bug or new feature you submitted to determine whether the modification is necessary, or if there is any room for improvement or a better solution.
* Start developing and submitting code after agreement to reduce the cost of communication between both parties as well as the number of rejected pull requests.


### Getting the source code

To modify or add a feature, click the `fork` button in the upper left corner to copy Jarslink trunk code to your code repository, after submitting an issue.

### Pulling a branch

Perform all the Jarslink modifications on the branch, and submit a `pull request` after the modifications, which will be merged into the trunk by the project maintenance personnel after the code review.  
Therefore, after getting the introduction to source code steps, you need to:

* Download the code locally. You may select the git/https mode in this step.


  ```
  git clone https://github.com/your account name/sofa-jarslink.git 
  ```

* Pull a branch to prepare for code modification.

  ```
  git branch add_xxx_feature
  ```
  
  After the preceding command is executed, your code repository will switch to the corresponding branch. To view the current branch, execute the following command:
  
  ```
  git branch -a
  ```
  
  If you want to switch back to the trunk, execute the following command:
  
  ```
  git checkout -b master
  ```
  
  If you want to switch back to the branch, execute the following command:
  
  ```
  git checkout -b "branchName"
  ```

### Modify the code and submit it locally.

After a branch is pulled, you can modify the code.

#### When modifying the code, note the following:

* Keep the code style consistent.

  Jarslink uses the Maven plugin to keep the code style consistent. Before submitting the code, be sure to execute the following commands locally
  
  ```plain
  mvn clean compile
  ```

* Supplement unit test code.
* New modifications should have passed existing unit tests.
* You should provide the new unit test to prove that the previous code has bugs and the new code has fixed such bugs.

  Execute the following command to run all tests:
  ```
  mvn clean test
  ```

  You can also use the IDE to help execute a command.

#### Other do's and don'ts

* Retain the original style of the code you are editing, especially the spaces and line feeds in the code.
* Delete useless annotations.
* Annotate the places where the logic and functionality are not easy to understand.
* Update documents in a timely manner.

After the code is modified, execute the following command to submit all modifications to the local repository:
  
  ```
  git commit -am 'Add xx feature'
  ```

### Submit the code to a remote repository

After being submitted to the local repository, the code is synchronized to a remote repository. Execute the following command to submit the local modification to github:

```
git push origin "branchname"
```

If you clicked the 'fork' button to get the source code, the modified code is pushed to your code repository rather than the Jarslink repository.

### Submit a request for merging the code into the trunk

After submitting the code to GitHub, you can send a request to merge your modified code into the Jarslink trunk code. To do so, you need to enter the corresponding repository on your GitHub and press the `pull request` button in the upper right corner. Select the target branch (usually `master`, and the system will notify the Jarslink personnel, who will review your code and merge it into the trunk as part of Jarslink if it meets the requirements.

#### Code review

After you submit the code, it will be assigned to maintenance personnel for review. You need to wait for the review result. If you have not received a reply after several days, leave a message under the PR, and put an at (@) sign before the name of the responsible person.

The person will leave a comment containing the code review suggestions on the corresponding PR or issue. If you find the suggestions reasonable, update your patch based on the comments.

#### Merge the code into the trunk

After your code passes the review, the Jarslink maintenance personnel will merge it into the trunk. You do not need to participate in this step. After the code is merged, you will receive a message.
