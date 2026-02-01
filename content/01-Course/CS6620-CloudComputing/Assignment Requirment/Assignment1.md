> Due: Feb 6 12am

You are required to use AWS python SDK (aka boto3) to do the following tasks in your AWS account (create your own with free tier):
1. Create two IAM roles. One is called `Dev` and the other `User`
2. Attach IAM policies to the roles so that `Dev` has full access to S3, while `User` can only list/get S3 buckets and objects
3. Create an IAM user (name is up to you)
4. Use the created user to assume the `Dev` role and create the following buckets and objects
	 -  A bucket with a unique name (up to you what you want to call it)
	 -  Create object `assignment1.txt` in the bucket, by uploading a txt file whose content is a string `Empty Assignment 1`
	 -  Create object `assignment2.txt` in the bucket, by uploading another txt file whose content is `Empty Assignment 2`
	 -  Create object `recording1.jpg` in the bucket, by uploading a small pic (your pick).
5. Quit the `Dev` role and now assume the `User` role, and do the following
	 -  Find all the objects whose key has the prefix `assignment` and compute the total size of those objects.
6. Quit the `User` role and now assume the `Dev` role, then delete the all the objects and the bucket.

Submission: Zip up your python files and submit it on Gradescope

Demo steps:
1. Clean up all the S3 object, IAM users/roles and relevant AWS resources from previous runs.
2. Run your code, then show the newly created S3 object, IAM user/roles to the TA, in AWS console.
3. The TA will pick a couple of code snippets and ask you to explain, making sure you understand your code.