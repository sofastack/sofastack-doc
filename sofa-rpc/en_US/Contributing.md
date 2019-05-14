> Before you read this document, you are suggested to read the [SOFARPC development roadmap](https://github.com/sofastack/sofa-rpc/wiki/RoadMap) to learn about development tasks and future plans.

## Preparations

Before you contribute code, you need to learn the basic use of Git tool and GitHub website.

* For how to use the Git tool, see [Git official documenation](http://git-scm.com/book/zh/v1) and pay attention to the first few chapters.
* For Git collaboration process, see [Git collaboration process](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html).

## GitHub code contribution process

### Submit an issue

No matter to fix SOFARPC bugs or add SOFARPC features, before submitting the codes, you must submit an issue on SOFARPC's GitHub to describe the bugs to be fixed and the functions you want to add.

There are several benefits of doing this:

* Avoid the conflict with other developers or their plans for this project, thus eliminating repetitive work.
* SOFARPC operations staff discuss your bugs or new features to determine if the changes are necessary and whether there is space for improvement or a better approach.
* Reduce communication cost between you and the SOFARPC operations staff, thus reducing the cases that pull request is rejected.


### Get source codes

To modify or add features, after you submit the issue, you can click **Fork** in the upper left corner to copy the SOFARPC trunk code to your code repository.

### Pull branches

All SOFARPC modifications are made on the branch. After modification, submit the `pull request`. After the code review, the project operations staff merge the branches to the trunk.

Therefore, you must complete the following steps after getting the source codes:

* Download the codes locally through Git or HTTPs.<br> 
	  ```
	  git clone https://github.com/your account name/sofa-rpc.git
  ```

* Pull branches for code modifications.<br> 

	  ```
	  git branch add_xxx_feature
	  ```

After executing the above command, your code repository switches to the corresponding branch. Execute the following command to see your current branch:<br>

 
	```
	  git branch -a
	```
	
If you want to switch back to the trunk, execute the following command:<br>
	  ```
	  git checkout -b master
	  ```
	
If you want to switch back to the branch, execute the following command:<br>
	```
	  git checkout -b "branchName"
	 ```


### Modify and submit codes locally

Once the branch is pulled, you can modify the code.


#### Attentions for modifying codes

* Keep a consistent code style.<br>
SOFARPC keeps the code format consistent through the Maven plugin. You must execute the following command locally before committing the code:
  
  ```plain
  mvn clean compile
  ```

* Supplemental unit test code.
* New modifications should have passed the existing unit tests.
* Provide new unit tests to prove that the previous code has bugs, and the new code has fixed these bugs.<br>
You can run all tests with the following command:
  ```
  mvn clean test
  ```

	You can also use IDE to assist the test running.

#### Other attentions

* Keep the original code style, especially the spacing and alignment.
* Delete the useless comments directly.
* Add comments for the logics and functions that cannot be easily understood.
* Update documentation timely.

After modification, execute the following command to submit all modifications locally:<br>
  
  ```
  git commit -am 'add xx function'
  ```

### Submit codes to a remote repository

After the codes are submitted a local repository, the codes are synchronized with the remote repository. Execute the following command to submit the local modification to GitHub:

```
git push origin "branchname"
```

If you did this through `fork`, then the `origin` codes here are pushed to your own code repository, not the SOFARPC code repository.

### Submit the request of merging codes to trunk 

Once the modified codes are submitted to GitHub, you can send a request to merge your changed codes into the SOFARPC trunck codes. At this point, you need to go to the corresponding repository on your GitHub, click `pull request` button in the upper right corner, and select the target branch which is usually `master`. The system will notify the SOFARPC staff. The SOFARPC staff will review your codes. If the codes meet the requirements, they will be merged to the trunk and become a part of SOFARPC.

#### Code review

The codes that you submitted will be assigned to the operations staff for review, please wait patiently. If you don't receive any response after a few days, you can leave a message under PR and notify the corresponding staff.

The Code Review comments will be directly addressed to the corresponding PR or Issue. If you agree the comments, please apply these suggestions to your patch.

#### Merge codes to trunk

If your modification passes the Code Review, the SOFARPC operations staff will merge the codes to the trunk. You don't have to participate in this step. When the codes are merged, you will receive a prompt about mergence success.
